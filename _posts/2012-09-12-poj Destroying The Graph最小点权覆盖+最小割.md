---
layout: post
title: "poj Destroying The Graph最小点权覆盖+最小割"
description: "poj Destroying The Graph最小点权覆盖+最小割"
category: "图论"
tags: []
---


题意：

 有 N 个节点M 条边的的有向图，每个点对应两种操作；将该点所有入边删除 ‘+’ ，花费w+；将该点所有出边删除 ‘ - ’,w- ;问如何操作将所有边删除使得花费的总和最小。


思路:

将每个点拆分成两个点, i 和 i+n ,建立一个源点 s=0,汇点 u=2\*n+1。
在源点和每个点(i<=n)点连一条边，容量为删除出边的费用,在汇点和每个点(i>n&&i<=2*n)的点连一条边，容量为删除入边的费用,  如果有 u 到 v 的边，在u 和 v +n 之间连一条边，容量为无穷大
第一个答案即使求最大流即最小割，接着从源点深搜找出残流不为 0 的点，把这些点标记掉，剩下的为标记的点即为删除的顶点。可以联想到二分图的最小点集覆盖，求最小的点集，让所有的边都与之关联。



	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<queue>
	using namespace std;
	#define maxn 250
	#define maxm 110000
	const int inf=0x1f1f1f1f;
	struct EDGE{
		int to,from,next;int re;
		int cap;
	}ee[maxm];
	int head[maxn],dep[maxn],gap[maxn];
	int n,m,src,des,siz;//src=start,des=end;
	void init(){
		siz=0;
		memset(head,-1,sizeof head);
	}
	void addedge(int u,int v,int cap){
		ee[++siz].from=u,ee[siz].to=v,ee[siz].cap=cap;
		ee[siz].next=head[u];
		ee[siz].re=siz+1;
		head[u]=siz;
		ee[++siz].from=v,ee[siz].to=u,ee[siz].cap=0;
		ee[siz].next=head[v];
		ee[siz].re=siz-1;
		head[v]=siz;
	}
	int que[maxn];
	void bfs(){
		memset(dep,-1,sizeof dep);
		memset(gap,0,sizeof gap);
		gap[0]=1;
		dep[des]=0;
		queue<int>que;
		que.push(des);
		int u,v;
		while(!que.empty()){
			u=que.front();
			//printf("u=%d\n",u);
			que.pop();
			for(int i=head[u];i!=-1;i=ee[i].next){
				v=ee[i].to;
				// printf("i=%d\n",i);
				if(ee[i].cap!=0||dep[v]!=-1)continue;
				que.push(v);
				++gap[dep[v]=dep[u]+1];
			}
		}
	}
	int dis[maxn],pre[maxn],stack[maxn];
	int viss[maxn];
	int SAP(int N)
	{
		memset(dis,0,sizeof(dis));
		memset(gap,0,sizeof(gap));
		gap[0]=N;
		int s=0;
		int u=0,ca=inf,ans=0;
		while (dis[s]<N)
		{
			while (1)
			{
				bool flag=false;
				for (int j=head[u];j!=-1;j=ee[j].next)
				{
					int i=ee[j].to;
					if (ee[j].cap && dis[u]==dis[i]+1)
					{
						ca=min(ca,ee[j].cap);
						pre[i]=j;u=i;
						if (i==des)
						{
							ans+=ca;
							while (i!=s)
							{
								u=pre[i]; //printf("^sdgfsd\n");
								ee[u].cap-=ca;
								ee[ee[u].re].cap+=ca;
								i=ee[ee[u].re].to;
							}
							u=s;ca=inf;
						}
					flag=true;
					break;
					}
				}
				if (!flag) break;
			}
			int Min=N;
			for (int j=head[u];j!=-1;j=ee[j].next)
			if (ee[j].cap && dis[ee[j].to]<Min)
			Min=dis[ee[j].to];
			if (--gap[dis[u]] == 0) break;
			gap[dis[u]=Min+1]++;
			if (u!=s) u=ee[ee[pre[u]].re].to;
		}
		return ans;
	}
	void dfs(int u){
		viss[u]=1;
		for(int i=head[u];i!=-1;i=ee[i].next){
			int v=ee[i].to;
			if(!viss[v]&&ee[i].cap){
				//
				printf("%d,%d\n",v,ee[i].cap);
				viss[v]=1;
				dfs(v);
			}
		}
	}
	int main(){
		while(scanf("%d%d",&n,&m)!=EOF){
			int a,b;
			init();
			src=0,des=n*2+1;
			for(int i=1;i<=n;i++){
				scanf("%d",&a);
				addedge(i+n,des,a);
			}
			for(int i=1;i<=n;i++){
				scanf("%d",&b);
				addedge(src,i,b);
			}
			for(int i=1;i<=m;i++){
				scanf("%d%d",&a,&b);
				addedge(a,b+n,inf);
			}
			int ans=SAP(des+1);
			memset(viss,0,sizeof viss);
			dfs(src);
			int num=0;
			for(int i=1;i<=n;i++){
				if(!viss[i])num++;
				if(viss[i+n])num++;
			}
			printf("%d\n%d\n",ans,num);
			for(int i=1;i<=n;i++){
				if(!viss[i]){
					printf("%d -\n",i);
				}
				if(viss[i+n]){
					printf("%d +\n",i);
				}
			}
		}
		return 0;
	}	

