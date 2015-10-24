---
layout: post
title: "POJ 2394 Checking an Alibi spfa&最短路"
description: "POJ 2394 Checking an Alibi spfa&最短路"
category:  "图论"
tags: []
---


题意：

有F块土地，有土地上有牛（可能有多头），粮仓在编号为1的土地上，问在M时间内有多少头牛可以到达粮仓，并按升序输出牛的编号。求一次最短路就可以了


	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<vector>
	#include<queue>
	#include<algorithm>
	using namespace std;
	#define maxn 2010
	#define maxm 510
	#define inf (1000000000)
	struct EDGE{
		int v,cost;
		int next;
	}ee[maxn];
	int cnt,siz,head[maxm];
	int dist[maxm];
	int n,m,totalcost,c;
	int vis[maxm];
	void init(){
		siz=0;
		for(int i=0;i<maxm;i++)dist[i]=inf;
		memset(head,-1,sizeof head);
		memset(vis,0,sizeof vis);
	}
	void addedge(int u,int v,int cost){
		ee[siz].v=v,ee[siz].cost=cost,ee[siz].next=head[u];
		head[u]=siz++;
	}
	void spfa(){
		queue<int>que;
		dist[1]=0;
		que.push(1);
		while(!que.empty()){
			int u=que.front();
			que.pop();
			vis[u]=0;
			for(int i=head[u];i!=-1;i=ee[i].next){
				int v=ee[i].v;
				int cost=ee[i].cost;
				if(dist[v]>dist[u]+cost){
					dist[v]=dist[u]+cost;
					if(!vis[v]){
						vis[v]=1;
						que.push(v);
					}
				}
			}
		}
	}
	int main(){
		while(scanf("%d%d%d%d",&n,&m,&c,&totalcost)!=EOF){
			int a,b,cc;
			init();
			for(int i=0;i<m;i++){
				scanf("%d%d%d",&a,&b,&cc);
				addedge(a,b,cc);
				addedge(b,a,cc);
			}
			int viss[maxm];
			for(int i=1;i<=c;i++){
				scanf("%d",&viss[i]);
			}
			spfa();
			int ans=0;
			for(int i=1;i<=c;i++){
				if(dist[viss[i]]<=totalcost){
					ans++;
				}
			}
			printf("%d\n",ans);
			sort(anss,anss+p);
			for(int i=1;i<=c;i++){
				if(dist[viss[i]]<=totalcost){
					printf("%d\n",i);
				}
			}
		}
		return 0;
	}
