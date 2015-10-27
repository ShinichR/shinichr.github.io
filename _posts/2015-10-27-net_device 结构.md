---
layout: post
title: "net_device 结构"
description: "net_device 结构"
category: "kernel"
tags: []
---

###net_device网络设备结构

在linux中使用struct net\_device结构体来描述每一个网络设备

设备名 

	char    name[IFNAMSIZ];  
	char    *ifalias;  //用于SNMP协议  

	struct pm_qos_request_list pm_qos_req; 
	//  用于Wi-Fi和千兆以太网，可以帮助控制网络的延迟和带宽的需求，以达到在可用的前提下省电的目的。 

硬件信息 :

	//网络设备内存映射时在主机中的内存区域
	unsigned long	mem_end;
	unsigned long	mem_start;
	//网络设备I/O基地址
	unsigned long	base_addr;
	//中断号
	unsigned int	irq;
	//传输介质，如双绞线、同轴电缆等，在多端口设备中指定使用哪个端口
	unsigned char	if_port;
	/* if_port可能的取值如下：
	enum {
       		IF_PORT_UNKNOWN = 0,
        	IF_PORT_10BASE2,
        	IF_PORT_10BASET,
        	IF_PORT_AUI,
        	IF_PORT_100BASET,
        	IF_PORT_100BASETX,
        	IF_PORT_100BASEFX
	};
	 **/ 
	// DMA通道
	unsigned char	dma;
	//最大传输单元，以太网数据帧最大为1500字节
	unsigned int	mtu;
	//网络设备硬件类型，如10Mbps以太网ARPHRD_ETHER
	unsigned short	type;
	//硬件数据帧头的长度，以太网为14字节
	unsigned short	hard_header_len;
	//广播地址
	unsigned char	broadcast[MAX_ADDR_LEN];
	//硬件(如MAC)地址长度以及设备的硬件地址
	unsigned char	addr_len;
	unsigned char	*dev_addr;
	unsigned char	perm_addr[MAX_ADDR_LEN];
	unsigned char	addr_assign_type;
	
标识符 	

	int	ifindex; //标识网络设备的唯一索引号
	int	iflink;  //用于虚拟网络设备
	unsigned short	dev_id;	//用于共享网络设备

分配套接字缓冲区时预留空间的长度 

	unsigned short	needed_headroom;
	unsigned short	needed_tailroom;
在sysfs文件系统中输出网络设备信息 
	
	struct device	dev;
	const struct attribute_group *sysfs_groups[4];
网络设备相关链表 

	//以设备名为关键字的网络设备哈希链表
	struct hlist_node	name_hlist;
	//网络设备链表
	struct list_head	dev_list;
	//支持NAPI传输的网络设备链表
	struct list_head	napi_list;
	//被注销的网络设备链表
	struct list_head	unreg_list;
	//网络设备硬件地址组成的链表
	struct netdev_hw_addr_list	dev_addrs; 
	/* n-tuple filter list attached to this device */
	struct ethtool_rx_ntuple_list ethtool_ntuple_list;
	//单播地址链表
	struct netdev_hw_addr_list	uc;
	//组播地址链表
	struct netdev_hw_addr_list	mc;
	//防止单播地址链表和组播地址链表被并发访问的自旋锁
	spinlock_t		addr_list_lock;
	//监听所有组播地址
	unsigned int		allmulti;
	//延迟注册/注销的网络设备链表
	struct list_head	todo_list;
	//以索引号为关键字的网络设备哈希链表
	struct hlist_node	index_hlist;
	//链路查看机制链表
	struct list_head	link_watch_list;
混杂模式	

	//混杂模式时的单播地址个数
	int		uc_promisc;
	//混杂模式的计数器
	unsigned int	promiscuity;

网络层协议特定数据 
	
	struct vlan_group __rcu	*vlgrp;		/* VLAN group */
	void			*dsa_ptr;	/* dsa specific data */
	void 			*atalk_ptr;	/* AppleTalk link 	*/
	struct in_device __rcu	*ip_ptr;	/* IPv4 specific data	*/
	struct dn_dev __rcu     *dn_ptr;        /* DECnet specific data */
	struct inet6_dev __rcu	*ip6_ptr;       /* IPv6 specific data */
	void			*ec_ptr;	/* Econet specific data	*/
	void			*ax25_ptr;	/* AX.25 specific data */
	struct wireless_dev	*ieee80211_ptr;	/* IEEE 802.11 specific data,
						   assign before registering */

设备硬件功能特性

	unsigned long		features;
		// features的可能取值如下：
	#define NETIF_F_SG		1	/* Scatter/gather IO. */
	#define NETIF_F_IP_CSUM		2	/* Can checksum TCP/UDP over IPv4. */
	#define NETIF_F_NO_CSUM		4	/* Does not require checksum. F.e. loopack. */
	#define NETIF_F_HW_CSUM		8	/* Can checksum all the packets. */
	#define NETIF_F_IPV6_CSUM	16	/* Can checksum TCP/UDP over IPV6 */
	#define NETIF_F_HIGHDMA		32	/* Can DMA to high memory. */
	#define NETIF_F_FRAGLIST	64	/* Scatter/gather IO. */
	#define NETIF_F_HW_VLAN_TX	128	/* Transmit VLAN hw acceleration */
	#define NETIF_F_HW_VLAN_RX	256	/* Receive VLAN hw acceleration */
	#define NETIF_F_HW_VLAN_FILTER	512	/* Receive filtering on VLAN */
	#define NETIF_F_VLAN_CHALLENGED	1024	/* Device cannot handle VLAN packets */
	#define NETIF_F_GSO		2048	/* Enable software GSO. */
	#define NETIF_F_LLTX		4096	/* LockLess TX - deprecated. Please */
						/* do not use LLTX in new drivers */
	#define NETIF_F_NETNS_LOCAL	8192	/* Does not change network namespaces */
	#define NETIF_F_GRO		16384	/* Generic receive offload */
	#define NETIF_F_LRO		32768	/* large receive offload */
	#define NETIF_F_FCOE_CRC	(1 << 24) /* FCoE CRC32 */
	#define NETIF_F_SCTP_CSUM	(1 << 25) /* SCTP checksum offload */
	#define NETIF_F_FCOE_MTU	(1 << 26) /* Supports max FCoE MTU, 2158 bytes*/
	#define NETIF_F_NTUPLE		(1 << 27) /* N-tuple filters supported */
	#define NETIF_F_RXHASH		(1 << 28) /* Receive hashing offload */
	#define NETIF_F_GSO_SHIFT	16
	#define NETIF_F_GSO_MASK	0x00ff0000
	#define NETIF_F_TSO		(SKB_GSO_TCPV4 << NETIF_F_GSO_SHIFT)
	#define NETIF_F_UFO		(SKB_GSO_UDP << NETIF_F_GSO_SHIFT)
	#define NETIF_F_GSO_ROBUST	(SKB_GSO_DODGY << NETIF_F_GSO_SHIFT)
	#define NETIF_F_TSO_ECN		(SKB_GSO_TCP_ECN << NETIF_F_GSO_SHIFT)
	#define NETIF_F_TSO6		(SKB_GSO_TCPV6 << NETIF_F_GSO_SHIFT)
	#define NETIF_F_FSO		(SKB_GSO_FCOE << NETIF_F_GSO_SHIFT)
	#define NETIF_F_GSO_SOFTWARE	(NETIF_F_TSO | NETIF_F_TSO_ECN | \
					 NETIF_F_TSO6 | NETIF_F_UFO)
	#define NETIF_F_GEN_CSUM	(NETIF_F_NO_CSUM | NETIF_F_HW_CSUM)
	#define NETIF_F_V4_CSUM		(NETIF_F_GEN_CSUM | NETIF_F_IP_CSUM)
	#define NETIF_F_V6_CSUM		(NETIF_F_GEN_CSUM | NETIF_F_IPV6_CSUM)
	#define NETIF_F_ALL_CSUM	(NETIF_F_V4_CSUM | NETIF_F_V6_CSUM)
	#define NETIF_F_ONE_FOR_ALL	(NETIF_F_GSO_SOFTWARE | NETIF_F_GSO_ROBUST | \
					 NETIF_F_SG | NETIF_F_HIGHDMA |		\
					 NETIF_F_FRAGLIST)
	
分配net_device结构体及其私有数据时为对齐所需的填充位数目

	unsigned short  padded;

其他信息
	
		//NETPOLL相关信息
	struct netpoll_info	*npinfo;
	//网络命名空间
	struct net		*nd_net;
	//中间层的私有数据
	union {
		void				*ml_priv;
		struct pcpu_lstats __percpu	*lstats; /* loopback stats */
		struct pcpu_tstats __percpu	*tstats; /* tunnel stats */
		struct pcpu_dstats __percpu	*dstats; /* dummy stats */
	};
	//GARP协议相关
	struct garp_port __rcu	*garp_port;
	//虚拟局域网相关
	unsigned long vlan_features;
	//GSO最大值
	unsigned int		gso_max_size;
	//max exchange id for FCoE LRO by ddp
	unsigned int		fcoe_ddp_xid;
	//PHY实例
	struct phy_device *phydev;

网络设备的运行状态
	
		unsigned long		state;
	/* state的可能取值如下：
	enum netdev_state_t {
		__LINK_STATE_START,
		__LINK_STATE_PRESENT,
		__LINK_STATE_NOCARRIER,
		__LINK_STATE_LINKWATCH_PENDING,
		__LINK_STATE_DORMANT,
	};
	**/
	unsigned int		flags;
	unsigned short		gflags;
	unsigned int            priv_flags; //类似flags,但对用户空间不可见

统计信息

	struct net_device_stats	stats;
	//在接收过程中丢弃的数据包数目（在网络驱动中不使用此项）
	atomic_long_t		rx_dropped; 

RFC 2863

	unsigned char		operstate;
	/* operstate的可能取值如下：
	enum {
		IF_OPER_UNKNOWN,
		IF_OPER_NOTPRESENT,
		IF_OPER_DOWN,
		IF_OPER_LOWERLAYERDOWN,
		IF_OPER_TESTING,
		IF_OPER_DORMANT,
		IF_OPER_UP,
	};
	 **/ 
	//映射到RFC2863兼容状态的策略
	unsigned char		link_mode;
	/* link_mode的可能取值如下：
	enum {
		IF_LINK_MODE_DEFAULT,
		IF_LINK_MODE_DORMANT,
	};
	 **/
网络设备的操作函数

	//使用无线网络设备扩展功能的一组操作函数
	const struct iw_handler_def *	wireless_handlers;
	//无线网络设备处理函数所使用的数据
	struct iw_public_data *	wireless_data;
	//网络设备驱动程序需要实现的一组操作函数
	const struct net_device_ops *netdev_ops;
	//支持ethtool功能的一组操作函数
	const struct ethtool_ops *ethtool_ops;
	//数据链路层协议头相关的一组操作函数
	const struct header_ops *header_ops;
	//析构函数，注销网络设备时被调用
	void (*destructor)(struct net_device *dev);
	//rtnetlink操作函数
	const struct rtnl_link_ops *rtnl_link_ops;
	//DCB操作函数
	const struct dcbnl_rtnl_ops *dcbnl_ops;
数据包的收发队列

	//RPS(Receive Packet Steering)特性
	struct kset		*queues_kset;
	struct netdev_rx_queue	*_rx;
	unsigned int		num_rx_queues;
	unsigned int		real_num_rx_queues;
	rx_handler_func_t __rcu	*rx_handler;
	void __rcu		*rx_handler_data;
	struct netdev_queue __rcu *ingress_queue;

发送队列 

	struct netdev_queue	*_tx ____cacheline_aligned_in_smp;
	unsigned int		num_tx_queues;
	unsigned int		real_num_tx_queues;
	struct Qdisc		*qdisc;
	unsigned long		tx_queue_len;
	spinlock_t		tx_global_lock;
	//XPS(Transmit Packet Steering)特性
	struct xps_dev_maps __rcu *xps_maps;

### 接口
#####分配一个网络设备函数，即分配一个struct net\_device结构体：

	alloc_netdev(sizeof_priv, name, setup);

这个alloc_netdev()函数本质上是一个宏定义：
    	
	#define alloc_netdev(sizeof_priv, name, setup) \
     alloc_netdev_mq(sizeof_priv, name, setup, 1)**

	参数介绍：
        sizeof_priv:为分配给网络设备私有空间的大小；
        name :网络设备的名称；
        setup:对分配的网络设备进行初始化的回调函数；
        queue_count:分配给网络设备的子队列数；

	struct net_device *alloc_netdev_mq( int sizeof_priv, const char *name, 
	            void (*setup)( struct net_device *), unsigned int queue_count )
	{
	       struct net_device *dev ;
	        
	       struct netdev_queue *tx ;
	        
	       int alloc_size ;
	        
	       alloc_size  = sizeof(struct net_device);
	        
	       dev = kzalloc(alloc_size, GFP_KERNEL);
	        
	       tx = kcalloc(queue_count, sizeof(struct netdev_queue), GFP_KERNEL);
	        
	       dev_addr_init(dev);//对struct net_device中的dev_addrs成员进行初始化；
	        
	       dev_unicast_init(dev); //对struct net_device中的uc成员进行初始化； 
	        
	       dev_net_set(dev, &init_net);//对网络设备的命名空间进行初始化，默认为init_net;
	        
	       dev->_tx = tx ;//设置网络设备的发送队列；
	       dev->num_tx_queues = queue_count ;
	       dev->real_num_tx_queues = queue_count ;
	        
	       netdev_init_queues(dev);//对struct net_device中的rx_queue成员进行初始化；
	        
	       setup(dev); //对struct net_device结构体进行初始化；
	        
	       strcpy(dev->name, name);//设置网路设备的设备名称；   
	        
	}
	 
	EXPORT_SYMBOL(alloc_netdev_mq);

偶尔你没有看到alloc\_netdev，而是alloc\_etherdev也不奇怪

alloc\_etherdev()是alloc\_netdev()针对以太网的"快捷"函数

	linux-2.6.22/net/ethernet/eth.c
	----------------------------------------
	struct net_device *alloc_etherdev(int sizeof_priv)
	{
		return alloc_netdev(sizeof_priv, "eth%d", ether_setup);
	}

#####释放一个网络设备：
    	
	void free_netdev(struct net_device *dev);
    EXPORT_SYMBOL(free_netdev);

#####注册一个网络设备，只有对一个网络设备进行注册以后，这个网络设备才会在内核中起作用：

    int register_netdev(struct net_device *dev)
    EXPORT_SYMBOL(register_netdev);
    返回值：
           0：表示注册成功；
           a negative errno code ：表示注册失败；

	int register_netdev(struct net_device *dev)
	{
	    struct hlist_head *head;
	    struct hlist_node *p;
	    int ret;
	    struct net *net = dev_net(dev); // 获取网络设备所在的命名空间；
	    rtnl_lock();//获取rtnl信号量；
	     
	    if(!dev_valid_name(dev->name)) //判断网络设备的设备名是否有效；
	    {}  
	     
	    dev->ifindex = dev_new_index(net); //从网络设备所在的命名空间中找到一个全局唯一的网络
	                                       //接口索引值；
	    dev->iflink = dev->ifindex ;
	     
	    //用于判断网络命名空间中是否有相同名字的网络设备存在；
	    head = dev_name_hash(net, dev->name);
	    hlist_for_each(p,head)
	    {
	       struct net_device *d = hlist_entry(p, struct net_device, name_hlist);
	       if(！strncmp(d->name, dev->name, 32))
	          {
	             ret = -EEXIST ;
	          }
	    }
	     
	    set_bit(__LINK_STATE_PRESENT, &dev->state);//设置网络设备的状态；
	    list_netdevice(dev); //将网络设备叫入到相应的命名空间之中；
	}
	EXPORT_SYMBOL(register_netdev);

#####注销一个网络设备结构体：

    void unregister_netdev(struct net_device *dev);
    EXPORT_SYMBOL(unregister_netdev);

#####设置一个网络设备的最大传输单元：
    int dev_set_mtu(struct net_device *dev, int new_mtu);
    EXPORT_SYMBOL(dev_set_mtu);
  
#####改变一个网络设备的flag标识符：
    int dev_change_flags(struct net_device *dev, unsigend flags);
    EXPORT_SYMBOL(dev_change_flags);
 




