---
layout: post
title: "poj Integer Intervals&差分约束"
description: "poj Integer Intervals&差分约束"
category: "图论"
tags: []
---



题意: 

数轴上有n个区间，每个区间都是连续的int区间，现在要在数轴上任意取一堆元素，构成一个元素集合V要求每个区间和元素集合V的交集至少有两个不同的元素，求集合V最小的元素
个数。

设s[x] =从0到x的所有在集合中的数的个数。则ai到bi的个数即S[bi] - S[ai-1]。

因此有

* 1 S[bi] - S[ai-1] >= 2。

又根据s[x]本身的性质，
后面的一定不比前面的小，后面的最多比前面多一，有：

* 2  s[i + 1] - s[i] >= 0
* 3  s[i + 1] - s[i] <= 1
建图，求解即可;



	
		#include<cstdio>
		#include<iostream>
		#include<cstring>
		#include<queue>
		using namespace std;
		const int maxn=11000;
		const int maxm=61000;
		#define inf (1<<29)
		struct EDGE{
			int to,next;
			int cost;
		}ee[maxm];
		int siz,head[maxn],dist[maxn],cnt[maxn],vis[maxn];
		void init(){
			siz=1;
			memset(vis,0,sizeof vis);
			memset(dist,0,sizeof dist);
			// memset(cnt,0,sizeof cnt);
			memset(head,-1,sizeof head);
		}
		int n,m;//100-100
		void addedge(int u,int v,int cost){
			ee[siz].to=v,ee[siz].cost=cost,ee[siz].next=head[u];
			head[u]=siz++;
		}
		void spfa(){
			queue<int>que;
			for(int i=0;i<maxn;i++)
			dist[i]=-inf;
			dist[0]=0;
			que.push(0);
			while(!que.empty()){
				int u=que.front();
				que.pop();
				vis[u]=0;
				for(int i=head[u];i!=-1;i=ee[i].next){
					int v=ee[i].to;
					if(dist[v]<dist[u]+ee[i].cost){
						dist[v]=dist[u]+ee[i].cost;
						if(!vis[v]){
							que.push(v);
							vis[v]=1;
							if(++cnt[v]>n){
								break;
							}
						}
					}
				}
			}
		// return true;
		}
		int main(){
			while(scanf("%d",&n)!=EOF){
				int u,v,maxx=-1;
				init();
				for(int i=0;i<n;i++){
					scanf("%d%d",&u,&v);
					maxx=max(maxx,v+1);
					addedge(u,v+1,2);
				}
				for(int i=0;i<=maxx;i++){
					addedge(i,i+1,0);
					addedge(i+1,i,-1);
				}
				spfa();
				printf("%d\n",dist[maxx]);
			}
			return 0;
		}




