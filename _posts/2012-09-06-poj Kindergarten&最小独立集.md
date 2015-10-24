---
layout: post
title: "poj Kindergarten&最小独立集"
description: "poj Kindergarten&最小独立集"
category: "图论"
tags: []
---



题意： 

给你n个男的，m个女的，男的之间都是相互认识的，女的也是，告诉一些男女认识关系，找出最多的人使其中任意一个人都相互认识。


此题即使求最大完全图的最大完全数，**最大完全图=
原图的补图的最大独立数**

而**最大独立集=顶点数-最大匹配数** 

即求**补图的最大匹配m,再用顶点数减m**即是答案



	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#define maxn 202
	using namespace std;
	int pa,pb,m;
	int maps[maxn][maxn];
	int vis[maxn];
	int mat[maxn];
	bool find(int x){
		for(int i=1;i<=pb;i++){
			if(!vis[i]&&maps[x][i]==0){
				vis[i]=1;
				if(!mat[i]||(find(mat[i]))){
					mat[i]=x;
					return true;
				}
			}
		}
		return false;
	}
	int dfs(){
		int ans=0;
		memset(mat,0,sizeof mat);
		for(int i=1;i<=pa;i++){
			memset(vis,0,sizeof vis);
			if(find(i)){
				ans++;
			}
		}
		return ans;
	}
	int main(){
		int cas=0;
		while(scanf("%d%d%d",&pa,&pb,&m),pa||pb||m){
			int a,b;
			memset(maps,0,sizeof maps);
			for(int i=0;i<m;i++){
				scanf("%d%d",&a,&b);
				maps[a][b]=1;
			}
			int ans=dfs();
			printf("Case %d: %d\n",++cas,pa+pb-ans);
		}
		return 0;
	}	
