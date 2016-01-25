---
layout: post
title: "kernel socket发送数据过程"
description: "kernel socket发送数据过程"
categories: [kernel,linux]
tags: []
---

对应用户层来讲，我们使用socket一般都是用socket创建一个套接字，用send ，write来发送数据。对应kernel来说，数据发送出去要经过很多步骤。

#####socket创建
socket接口对应kernel的接口是

	SYSCALL_DEFINE3(socket, int, family, int, type, int, protocol)
	（socket.c）

SYSCALL\_DEFINE3 这个宏的原理可以参考这篇文章：
<http://blog.csdn.net/hxmhyp/article/details/22699669>

socket创建过程：

	1:sys_socket->sock_create ->__sock_create->pf->create
	2:sys_socket->sock_map_fd //创建文件描述符

pf->create 实际调用的就是inet\_create

inet\_create在inet\_init中注册：

	(void)sock_register(&inet_family_ops);
	
	static const struct net_proto_family inet_family_ops = {
		.family = PF_INET,
		.create = inet_create,
		.owner	= THIS_MODULE,
	};
	(af_inet.c)

inet\_create根据参数protocol来查找注册的inetsw，查找对应protocol的处理handler

	//inet_create()：
	list_for_each_entry_rcu(answer, &inetsw[sock->type], list) {

		err = 0;
		/* Check the non-wild match. */
		if (protocol == answer->protocol) {
			if (protocol != IPPROTO_IP)
				break;
		} else {
			/* Check for the two wild cases. */
			if (IPPROTO_IP == protocol) {
				protocol = answer->protocol;
				break;
			}
			if (IPPROTO_IP == answer->protocol)
				break;
		}
		err = -EPROTONOSUPPORT;
	}
	
	//inet_init():
	for (r = &inetsw[0]; r < &inetsw[SOCK_MAX]; ++r)
		INIT_LIST_HEAD(r);

	//注册protocol
	for (q = inetsw_array; q < &inetsw_array[INETSW_ARRAY_LEN]; ++q)
		inet_register_protosw(q);

	static struct inet_protosw inetsw_array[] =
	{
		{
			.type =       SOCK_STREAM,
			.protocol =   IPPROTO_TCP,
			.prot =       &tcp_prot,
			.ops =        &inet_stream_ops,
			.no_check =   0,
			.flags =      INET_PROTOSW_PERMANENT |
				      INET_PROTOSW_ICSK,
		},
	
		{
			.type =       SOCK_DGRAM,
			.protocol =   IPPROTO_UDP,
			.prot =       &udp_prot,
			.ops =        &inet_dgram_ops,
			.no_check =   UDP_CSUM_DEFAULT,
			.flags =      INET_PROTOSW_PERMANENT,
	       },
	
	       {
			.type =       SOCK_DGRAM,
			.protocol =   IPPROTO_ICMP,
			.prot =       &ping_prot,
			.ops =        &inet_dgram_ops,
			.no_check =   UDP_CSUM_DEFAULT,
			.flags =      INET_PROTOSW_REUSE,
	       },
	
	       {
		       .type =       SOCK_RAW,
		       .protocol =   IPPROTO_IP,	/* wild card */
		       .prot =       &raw_prot,
		       .ops =        &inet_sockraw_ops,
		       .no_check =   UDP_CSUM_DEFAULT,
		       .flags =      INET_PROTOSW_REUSE,
	       }
	};

这里以tcp为例，最终ops就是inet\_stream_ops，sendmsg就是inet\_sendmsg
	
	const struct proto_ops inet_stream_ops = {
		.family		   = PF_INET,
		.owner		   = THIS_MODULE,
		.release	   = inet_release,
		.bind		   = inet_bind,
		.connect	   = inet_stream_connect,
		.socketpair	   = sock_no_socketpair,
		.accept		   = inet_accept,
		.getname	   = inet_getname,
		.poll		   = tcp_poll,
		.ioctl		   = inet_ioctl,
		.listen		   = inet_listen,
		.shutdown	   = inet_shutdown,
		.setsockopt	   = sock_common_setsockopt,
		.getsockopt	   = sock_common_getsockopt,
		.sendmsg	   = inet_sendmsg,
		.recvmsg	   = inet_recvmsg,
		.mmap		   = sock_no_mmap,
		.sendpage	   = inet_sendpage,
		.splice_read	   = tcp_splice_read,
	#ifdef CONFIG_COMPAT
		.compat_setsockopt = compat_sock_common_setsockopt,
		.compat_getsockopt = compat_sock_common_getsockopt,
		.compat_ioctl	   = inet_compat_ioctl,
	#endif
	};

tcp socket创建过程就完了，然后研究发送数据（省去tcp握手等过程）

####send
send调用sys\_sendto,sys\_sendto->sock\_sendmsg

sendmsg对应的tcp\_sendmsg

	//inet_sendmsg()
	return sk->sk_prot->sendmsg(iocb, sk, msg, size);

	sk->sk_prot初始化：inet_create中查找对应的protocol handler，answer_prot对应的就是tcp_prot
	{
			.type =       SOCK_STREAM,
			.protocol =   IPPROTO_TCP,
			.prot =       &tcp_prot,
			.ops =        &inet_stream_ops,
			.no_check =   0,
			.flags =      INET_PROTOSW_PERMANENT |
				      INET_PROTOSW_ICSK,
	},
	//inet_create():
	answer_prot = answer->prot;
	sk = sk_alloc(net, PF_INET, GFP_KERNEL, answer_prot);

	//sk_alloc（）:
	sk->sk_prot = sk->sk_prot_creator = prot;
	

tcp\_sendmsg:
这里不讲针对tcp 包的特殊处理只讲发送出去的流程。
tcp\_sendmsg 主要处理了，数据分段，skb插入发送队列尾部，发送

TCP发送数据共有三种途径

	__tcp_push_appending_frames,tcp_push_one,tcp_push，其中tcp_push调用了__tcp_push_pending_frames。

	到底调用哪个或哪些函数取决于是否有PUSH标志、NAGLE是否开启、和一些其他情况。

	__tcp_push_appending_frames是试图一次发送完缓存队列中所有的skb。tcp_push_one先计算拥塞窗口，然后只发送窗口大小的数据，如果窗口大小为0，则不发送任何数据。

	这三个接口实际调用的发送接口都是tcp_transmit_skb

	//tcp_output.c tcp_transmit_skb()
	err = icsk->icsk_af_ops->queue_xmit(skb, &inet->cork.fl);

queue\_xmit:tcp对应的就是ip\_queue\_xmit

	const struct inet_connection_sock_af_ops ipv4_specific = {
		.queue_xmit	   = ip_queue_xmit,
		.send_check	   = tcp_v4_send_check,
		.rebuild_header	   = inet_sk_rebuild_header,
		.sk_rx_dst_set	   = inet_sk_rx_dst_set,
		.conn_request	   = tcp_v4_conn_request,
		.syn_recv_sock	   = tcp_v4_syn_recv_sock,
		.net_header_len	   = sizeof(struct iphdr),
		.setsockopt	   = ip_setsockopt,
		.getsockopt	   = ip_getsockopt,
		.addr2sockaddr	   = inet_csk_addr2sockaddr,
		.sockaddr_len	   = sizeof(struct sockaddr_in),
		.bind_conflict	   = inet_csk_bind_conflict,
	#ifdef CONFIG_COMPAT
		.compat_setsockopt = compat_ip_setsockopt,
		.compat_getsockopt = compat_ip_getsockopt,
	#endif
	};
	//tcp_v4_init_sock()
	icsk->icsk_af_ops = &ipv4_specific;
		

接着通过路由子系统查找出口，设置ip header
	
	ip_route_output_ports
			|
	ip_route_output_flow
			|
	__ip_route_output_key
			|
	__mkroute_output
			|
	rth->dst.output = ip_output;
	
	ip_queue_xmit->ip_local_out->dst_output

dst\_output对应ip\_output:
	
ip\_output经过NF\_INET\_POST\_ROUTING hook之后调用ip\_finish\_output，最终调用dev\_queue\_xmit发送出去

		ip_output
			|
	ip_finish_output
			|
	ip_finish_output
			|
	dst_neigh_output
			|
	neigh_hh_output
			|
	dev_queue_xmit

以上就是一个tcp 数据发送出去的流程，省略了驱动层和发送中间的很多细节。