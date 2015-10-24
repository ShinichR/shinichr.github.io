---
layout: post
title: "poj 3469 Dual Core CPU&最小割"
description: "poj 3469 Dual Core CPU&最小割"
category: "图论"
tags: []
---


题意：

计算机里配置了双核的CPU， 有 N 个原件， 每个原件在 每个CPU 上有w耗费。有 m 对原件协同工作，耗费w，如果这些对原件是在同一个CPU上工作，耗费为0。问使用N个模块最少的费用。

建图;

把两块cpu看成源点与汇点，每个元件在cpu上的花费为弧长，如果有两个元件协同工作，连接这两个元件，弧长为耗费.即求此图的最小割。
TLE了5,6次，最后改了模板，总算A了。


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
				if (--gap[dis[u]] == 0) break; /*gap优化层次树出现断层则结束算法*/
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
	int main()
	{
		while (scanf("%d%d",&n,&m) != EOF)
		{
			init();
			int a,b,w;
			int source=1,sink=n+2;
			for(int i=2;i<=n+1;i++){
				scanf("%d%d",&a,&b);
				addedge(source,i,a);
				addedge(i,sink,b);
			}
			for(int i=0;i<m;i++){
				scanf("%d%d%d",&a,&b,&w);
				a++,b++;
				addedge(a,b,w);
				addedge(b,a,w);
			}
			printf("%d\n", SAP(source, sink, sink+1));
		}
		return 0;
	}

