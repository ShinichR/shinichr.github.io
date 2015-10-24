---
layout: post
title: "poj Domino Effect 最短路"
description: "poj Domino Effect 最短路"
category: "图论"
tags: []
---


题意为给你一些骨牌，以及骨牌到其他列骨牌倒下所要花费的时间,每次倒下都是从1开始的，问最后倒下的发生在那一列或那两列，以及全部倒下的时间
先求出每列骨牌的最短路,最后倒下的那列时间为它,再枚举两列同时倒下的时间t1,若有t1大于t2,说明最后是两列同时倒,否则是一列.

代码:


	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<vector>
	#include<queue>
	#define inf 0x7f7f7f7f
	#define pb push_back
	using namespace std;

	int dist[510];
	int maps[510][510];
	struct Edge{
		int u;
		int cost;
	};

	vector<Edge>v[510];
	int n,m;

	void spfa(){
		queue<int>que;
		while(!que.empty())que.empty();
		int vis[550];
		memset(vis,0,sizeof vis);
		que.push(1);
		dist[1]=0;
		//vis[1]=1;
		while(!que.empty()){//spfa求每列倒下的最短时间
		int uu=que.front();que.pop();
		
		vis[uu]=0;
		int siz=v[uu].size();
		
		for(int i=0;i<siz;i++){
			int vv=v[uu][i].u;
			int cost=v[uu][i].cost;
		
			if(dist[vv]>dist[uu]+cost){
				dist[vv]=dist[uu]+cost;
				if(!vis[vv]){
					vis[vv]=1;
					que.push(vv);
				}
			}
		}
	}
	int main(){
		int cas=0;
		while(scanf("%d%d",&n,&m),n||m){
			int a,b,c;
			for(int i=0;i<510;i++)v[i].clear();
			for(int i=0;i<=n;i++)
				for(int j=0;j<=n;j++)
					maps[i][j]=(i==j?0:inf);
		
			for(int i=0;i<=n;i++)dist[i]=inf;
			for(int i=0;i<m;i++){
				scanf("%d%d%d",&a,&b,&c);
				Edge e;e.u=b,e.cost=c;
				v[a].pb(e);
				e.u=a,e.cost=c;
				v[b].pb(e);
				maps[a][b]=maps[b][a]=c;
			}
			spfa();
			double ans1=-1.0;
			int pos=1;
			for(int i=1;i<=n;i++){
				//每列倒下的最短时间
				//
				printf("dist=%d\n",dist[i]);
				if(ans1<dist[i]){
					ans1=dist[i];
					pos=i;
				}
			}
			double ans2=-1.0;
			int pos1,pos2;
			for(int i=1;i<=n;i++){//枚举所有列之间倒下的情况
				for(int j=1;j<=n;j++){
					if(((dist[i]+dist[j]+maps[i][j])/2.0)>ans2&&maps[i][j]<inf){//更新最后倒下的那两列之间倒下的时间
		
					printf("%d,%d,%d\n",dist[i],dist[j],maps[i][j]);
					ans2=(dist[i]+dist[j]+maps[i][j])/2.0;
					pos1=i,pos2=j;
					}
				}
			}
			if(ans2>ans1){//若有两列之间倒下的最长时间大于每列	倒下	的最长时间，肯定最后倒下的是从两头一起倒
				printf("System #%d\nThe last domino falls after %.1f seconds, between key dominoes %d and %d.\n\n",++cas,ans2,pos1,pos2);
			}
			else
				printf("System #%d\nThe last domino falls 	after %.1f seconds, at key domino %d.\n\n",+	+cas,ans1,pos);
		}
		return 0;
	}


