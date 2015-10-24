---
layout: post
title: "poj Tram&floyd"
description: "poj Tram&floyd"
category: "图论"
tags: []
---



题目很水,题意也很简单.

附上代码


	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<queue>
	using namespace std;
	const int maxn=110;
	#define inf (1<<29)
	int maps[maxn][maxn];
	int n,A,B;
	void floyd(){
		for(int k=1;k<=n;k++){
			for(int i=1;i<=n;i++){
				for(int j=1;j<=n;j++){
					maps[i][j]=min(maps[i][k]+maps[k][j],maps[i][j]);
				}
			}
		}
	}
	int main(){
		while(scanf("%d%d%d",&n,&A,&B)!=EOF){
			for(int i=1;i<maxn;i++){
				for(int j=1;j<maxn;j++){
						maps[i][j]=i==j?0:inf;
				}
			}int k,s;
			for(int i=1;i<=n;i++){
				scanf("%d",&k);
				for(int j=1;j<=k;j++){
					scanf("%d",&s);
					maps[i][s]=j==1?0:1;
				}
			}
			floyd();
			printf("%d\n",maps[A][B]==inf?-1:maps[A][B]);
		}
		return 0;
	}


