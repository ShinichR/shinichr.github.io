---
layout: post
title: "poj Honeymoon Hike&dfs&二分"
description: "poj Honeymoon Hike&dfs&二分"
category: "图论"
tags: []
---



题意:

即为给一个矩阵，求从(1,1)走到(n,n)的路径中最大高度-最小高度的值最小,走的方向为上下左右.
解法即为二分最大高度和最小高度进行dfs.比赛时TLE没想到二分啊。

	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#define maxn 110
	using namespace std;
	int vis[maxn][maxn];
	int n;
	int cnt[maxn][maxn];
	int dir[4][2]={-1,0,0,1,1,0,0,-1};
	bool dfs(int a,int b,int down ,int up){
		if(cnt[a][b]>up||cnt[a][b]<down)return false;
		if(a==n&&b==n)return true;
		vis[a][b]=1;
		for(int i=0;i<4;i++){
			int c=a,d=b;
			c+=dir[i][0],d+=dir[i][1];
			// printf("c=%d,d=%d\n",c,d);
			if(vis[c][d]||c<1||d<1||c>n||d>n)continue;
			if(dfs(c,d,down,up))return true;
		}
		return false;
	}
	int bi_search(int l,int r){
		int mid;
		while(l<r){
			mid=(l+r)/2;
			int tmp=cnt[1][1]-mid;
			int low=tmp>0?tmp:0;
			for(;low<=cnt[1][1];low++){
				printf("low=%d...",low);
				memset(vis,0,sizeof vis);
				if(dfs(1,1,low,low+mid))break;
			}
			if(low<=cnt[1][1])r=mid;//mid大了
			else l=mid+1;//小了
		}
		return r;
	}
	int main(){
		int cas;
		scanf("%d",&cas);
		for(int ii=1;ii<=cas;ii++){
			scanf("%d",&n);
			for(int i=1;i<=n;i++){
				for(int j=1;j<=n;j++){
					scanf("%d",&cnt[i][j]);
				}
			}
			printf("Scenario #%d:\n%d\n\n",ii,bi_search(0,200));
		}
		return 0;
	}

