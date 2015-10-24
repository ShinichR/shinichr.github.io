---
layout: post
title: "2012 Chengdu Online Food&最大流"
description: "2012 Chengdu Online Food&最大流"
category: "图论"
tags: []
---


题目：


给出N个人喜欢的饮料种类以及食物种类，每个人只能取其中一种且只能取一种，
现在给出有F中食物以及D种饮料，以及他们各自的数量，问如何安排食物以及饮料，
使得得到一个食物和一瓶饮料两者的人最多.


最大流问题：


对人进行**拆点（-a，a），进行连边，cap为1，定义一个超级源点s和超级汇点t，s与每个食物相连，cap即为食物的数量，t与drink相连，cap即为数量。对于每个人对食物的喜好和-a这边的点相连，drink即与a相连，cap都为1**，用sap求最大流即使答案。
建边时i+m处理成立i+m+1，wrong到吐了.

###
	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<queue>
	using namespace std;
	#define maxn 20010
	#define maxm 1800000
	const int inf=(1<<29);
	struct EDGE{
	int v,next;
	int cap;
	}ee[maxm];
	int head[maxn],gap[maxn]; //dep[maxn];
	int n,m,src,des,siz;//src=start,des=end;
	void init(){
		siz=0;
		memset(head,-1,sizeof head);
	}
	void addedge(int u,int v,int cap){
		ee[siz].v=v,ee[siz].cap=cap;
		ee[siz].next=head[u];
		head[u]=siz++;
		ee[siz].v=u,ee[siz].cap=0;
		ee[siz].next=head[v];
		head[v]=siz++;
	}
	int dis[maxn],pre[maxn];
	int cur[maxn],aug[maxn];
	int SAP(int s, int e, int n)
	{
		int max_flow = 0, v, u = s;
		int id, mindis;
		aug[s] = inf;
		pre[s] = -1;
		memset(dis, 0, sizeof(dis));
		memset(gap, 0, sizeof(gap));
		gap[0] = n;
		for (int i = 0; i <= n; ++i){//初始化当前弧为第一条弧
			cur[i] = head[i];
		}
		while (dis[s] < n)
		{
			bool flag = false;
			if (u == e)
			{
				max_flow += aug[e];
				for (v = pre[e]; v != -1; v = pre[v])//路径回溯更新残留网络
				{
					id = cur[v];
					ee[id].cap -= aug[e];
					ee[id^1].cap += aug[e];
					aug[v] -= aug[e]; //修改可增广量，以后会用到
					if (ee[id].cap == 0) u = v; //不回退到源点，仅回退到容量为0的弧的弧尾
				}
			}
			for (id = cur[u]; id != -1; id = ee[id].next)
			{
			// 从当前弧开始查找允许弧
				v = ee[id].v;
				if (ee[id].cap > 0 && dis[u] == dis[v] + 1) //找到允许弧
				{
					flag = true;
					pre[v] = u;
					cur[u] = id;
					aug[v] = min(aug[u], ee[id].cap);
					u = v;
					break;
				}
			}
			if (flag == false)
			{
				if (--gap[dis[u]] == 0) break; /*gap优化层次树出现断则结束算法*/
				mindis = n;
				cur[u] = head[u];
				for (id = head[u]; id != -1; id = ee[id].next)
				{
					v = ee[id].v;
					if (ee[id].cap > 0 && dis[v] < mindis)
					{
						mindis = dis[v];
						cur[u] = id; //修改标号的同时修改当前弧
					}
				}
				dis[u] = mindis + 1;
				gap[dis[u]]++;
				if (u != s) u = pre[u]; //回溯继续寻找允许弧
			}
		}
		return max_flow;
	}
	int a,k;
	int main()
	{
		while (scanf("%d%d%d",&n,&m,&k) != EOF)
		{
			init();
			int source=0,sink=2*n+m+k+1;
			
			for(int i=1;i<=m;i++){
				scanf("%d",&a);
				addedge(source,i,a);
				
			}
			for(int i=1;i<=k;i++){
				scanf("%d",&a);
				addedge(2*n+m+i,sink,a);
				
			}
			char str[250];
			for(int i=1;i<=n;i++){
				scanf("%s",str);
				addedge(i+m,i+m+n,1);
				for(int j=0;j<m;j++){
					if(str[j]=='Y'){
						addedge(j+1,i+m,1);// 写成了i+m+1,wrong 疯了
					}
				}
			}
			for(int i=1;i<=n;i++){
				scanf("%s",str);
				for(int j=0;j<k;j++){
					if(str[j]=='Y'){
						addedge(n+m+i,2*n+m+j+1,1);
						// printf("%d,%d,%d\n",n+m+i,2*n+m+j+1,1);
					}
				}
			}
			printf("%d\n", SAP(source, sink, sink+1));
		}
		return 0;
	}
###