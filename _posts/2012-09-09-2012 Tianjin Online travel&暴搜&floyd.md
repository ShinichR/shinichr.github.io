---
layout: post
title: "2012 Tianjin Online travel&暴搜"
description: "2012 Tianjin Online travel&暴搜"
category: "图论"
tags: []
---



题意很简单，就是从1开始旅行，经过一些rule后能不能回到1,由於要得到所有的license（<=15）,先floyd求出各点的最小花费，在暴搜15个点，最后判能否回到1即可.
比赛水了，这题该看的！！！




	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<cmath>
	using namespace std;
	const int maxn=1010;
	int p[maxn],earn[maxn],costs[maxn];
	int h,maps[maxn][maxn];
	int vis[30];
	bool flag;
	#define inf 110000000
	void init(){
		flag=false;
		memset(vis,0,sizeof vis);
		for(int i=0;i<maxn;i++){
			for(int j=0;j<maxn;j++){
				maps[i][j]=i==j?0:inf;
			}
		}
	}
	void floyd(int n){
		for(int k=1;k<=n;k++){
			for(int i=1;i<=n;i++){
				for(int j=1;j<=n;j++){
					maps[i][j]=min(maps[i][j],maps[i][k]+maps[k][j]);
				}
			}
		}
	}
	void dfs(int u,int m,int cost){
		if(flag)return ;
		if(m==h){
			if(cost>=maps[u][1]){//成功回到起点
				flag=true;
			}
			return ;
		}
		for(int i=1;i<=h;i++){
			int v=p[i];
			if(!vis[i]&&cost-maps[u][v]-costs[i]>=0){//可以买license
				cost=cost-maps[u][v]-costs[i]+earn[i];
				vis[i]=1;
				dfs(v,m+1,cost);
				vis[i]=0;
			}
		}
	}
	int main(){
		int cas;
		scanf("%d",&cas);
		while(cas--){
			int n,m,cost;
			scanf("%d%d%d",&n,&m,&cost);
			init();
			int a,b,c;
			for(int i=0;i<m;i++){
				scanf("%d%d%d",&a,&b,&c);
				maps[a][b]=maps[b][a]=min(maps[a][b],c);
			}
			floyd(n);//求各点最小花费
			scanf("%d",&h);
			for(int i=1;i<=h;i++){
				scanf("%d%d%d",&a,&b,&c);
				p[i]=a,earn[i]=b,costs[i]=c;
			}
			dfs(1,0,cost);//从起点1开始
			puts(flag==true?"YES":"NO");
		}
		return 0;
	}

