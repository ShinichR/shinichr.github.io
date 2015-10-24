---
layout: post
title: "hdu Connections between cities&最近公共祖先"
description: "hdu Connections between cities&最近公共祖先"
category:  "图论"
tags: []
---



根据题意可知所给的图是无环图，而且是一个森林，**原始最短路绝对超时.
想到tarjan求最小公共祖先的离线算法**



	#include<iostream>
	#include<cstdio>
	#include<cstring>
	#include<queue>
	#include<string>
	using namespace std;
	#define N 11111
	#define M 2100000
	#define CLR(arr,val) memset(arr,val,sizeof(arr))
	struct EDGE{
		//int u;加超空间
		int v,cost;
		int next;
	}ee[M];
	int head[N],head1[N];
	int father[N],id[N];
	int ans[M],dist[N];
	int vis[N],siz,cnt;
	void addedge(int u,int v,int cost,int h[]){
		ee[siz].v=v,ee[siz].cost=cost;
		ee[siz].next=h[u];
		h[u]=siz++;
	}
	void init(){
		siz=0,cnt=0;
		CLR(head,-1);
		CLR(head1,-1);
		CLR(vis,0);
		CLR(ans,0);
		CLR(dist,0);
		CLR(father,0);
		CLR(id,0);
	}
	int n,m,q;
	int find(int x){
		return father[x]==x?x:father[x]=find(father[x]);
	}
	void tarjan(int x){
		id[x]=cnt;
		int v;
		vis[father[x]=x]=1;
		for(int i=head1[x];i!=-1;i=ee[i].next){
			if(vis[v=ee[i].v]){//如果该节点被访问过，
			if(id[v]==id[x]) ans[ee[i].cost]=dist[x]+dist[v]-2*dist[find(v)];//并且是同一个树，则找最近公共祖先
				else ans[ee[i].cost]=-1;//否则无法连接
			}
		}
		for(int i=head[x];i!=-1;i=ee[i].next)
			if(!vis[v=ee[i].v])
			{
				dist[v]=dist[x]+ee[i].cost;
				tarjan(v);
				father[v]=x;
			}
	}
	int main(){
		while(scanf("%d%d%d",&n,&m,&q)!=EOF){
			init();
			int a,b,c;
			for(int i=0;i<m;i++){
				scanf("%d%d%d",&a,&b,&c);
				addedge(a,b,c,head);
				addedge(b,a,c,head);
			}
			for(int i=1;i<=q;i++){
				scanf("%d%d",&a,&b);
				addedge(a,b,i,head1);
				addedge(b,a,i,head1);
			}
			for(int i=1;i<=n;i++){
				if(!vis[i]){
					dist[i]=0;
					tarjan(i);
				}
				cnt++;
				/*
				for(int j=1;j<=n;j++){
					printf("%d ",vis[j]);
				}
					printf("\n");*/
			}
			for(int i=1;i<=q;i++){
				if(ans[i]>=0)
					printf("%d\n",ans[i]);
				else 
					printf("Not connected\n");
			}
		}
		return 0;
	}



