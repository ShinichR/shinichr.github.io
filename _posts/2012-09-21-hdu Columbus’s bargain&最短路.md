---
layout: post
title: "hdu Columbus’s bargain&最短路"
description: "hdu Columbus’s bargain&最短路"
category: "图论"
tags: []
---


题意：

有一种金币交易，交易规则有3种：

* 1、每次买一个东西可以用一个glass bead代替1金币。
* 2、可以用相同的价格物品进行交换。
* 3、可以用N1+r金币交换到N2物品。


求最后每件物品的最小价格，并且最后输出有多少个物品价格=其他任意两个物品价格之和。


建立一个超级源点，连接每个物品，边权为cost-1，表示用了一个glass bead，再枚举所有商品，如价格相同就建一条0的边，表示可以交换。之后对于m个替换情况，依次建边即可。


每件物品的最小价格即为每个点到源点的距离，用spfa即可，最后3个for即可找出第二个问的解。

	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<queue>
	using namespace std;
	#define maxn 500
	#define inf 999999999
	struct EDGE{
	int v,cost;
	int next;
	}ee[maxn];
	int siz,head[maxn];
	void addedge(int u,int v,int cost){
	ee[siz].v=v,ee[siz].cost=cost;ee[siz].next=head[u];
	head[u]=siz++;
	}
	void init(){
	memset(head,-1,sizeof head);
	siz=0;
	}
	int n;
	int dist[maxn],vis[maxn];
	void spfa(){
		for(int i=0;i<maxn;i++)dist[i]=inf;
		for(int i=0;i<maxn;i++)vis[i]=0;
		queue<int>que;
		que.push(0);
		dist[0]=0;
		vis[0]=1;
		while(!que.empty()){
			int u=que.front();
			que.pop();
			vis[u]=0;//wrong 的根源
			for(int i=head[u];i!=-1;i=ee[i].next){
				int v=ee[i].v;
				if(dist[v]>dist[u]+ee[i].cost){
					dist[v]=dist[u]+ee[i].cost;
					if(!vis[v]){
						vis[v]=1;
						que.push(v);
					}
				}
			}
		}
		for(int i=1;i<=n;i++)
		printf("%d %d\n",i,dist[i]);
		//printf("\n");
	}
	int main(){
		int cas;
		int aa[maxn];
		scanf("%d",&cas);
		while(cas--){
			init();
			scanf("%d",&n);
			int m,u,cost,a,b;
			for(int i=1;i<=n;i++){
				scanf("%d%d",&u,&cost);
				aa[i]=cost;
				addedge(0,i,cost-1);//每次买一个东西可以用一个玻璃珠代替1金币。
			}
			scanf("%d",&m);
			for(int i=1;i<=n;i++){//价格相同可以替换
				for(int j=1;j<=n;j++){
					if(i!=j&&aa[i]==aa[j])
						addedge(i,j,0);
				}
			}
			for(int i=1;i<=m;i++){
				scanf("%d%d%d",&a,&b,&cost);
				addedge(a,b,cost);//可以用N1+r金币交换到N2物品
			}
			spfa();//求最后每件物品的最小价格
			int ans[maxn];
			memset(ans,0,sizeof ans);
			for(int j=1;j<=n;j++){
				for(int i=1;i<=n;i++){
					for(int k=1;k<=n;k++){
						if(i!=j&&j!=k&&k!=i&&(dist[k]==dist[i]+dist[j])){
							// printf("%d,%d,%d\n",dist[k],dist[i],dist[j]);
							ans[k]=1;
						}
					}
				}
			}
			int kk=0;
			for(int i=1;i<=n;i++)if(ans[i]==1)kk++;
			printf("%d\n",kk);
		}
		return 0;
	}
	
