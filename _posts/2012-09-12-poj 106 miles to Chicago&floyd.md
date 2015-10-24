---
layout: post
title: "poj 106 miles to Chicago&floyd"
description: "poj 106 miles to Chicago&floyd"
category: "图论"
tags: []
---


题意

即为每条路有一个存活的概率，问从1走到n概率最大是多少
应n为100,floyd可以水过,即是一个求最长路的问题，inf定义为-1,保证不通，相同顶点的边定义为1，这样就可以随意相乘.


	#include<cstdio>
	#include<iostream>
	#include<cstring>
	using namespace std;
	#define inf -1.0
	#define maxn 110
	double maps[maxn][maxn];
	int n,m;
	void floyd(){
		for(int k=1;k<=n;k++){
			for(int i=1;i<=n;i++){
				for(int j=1;j<=n;j++){
					maps[i][j]=max(maps[i][j],maps[i][k]*maps[k][j]/100);
				}
			}
		}
	}
	int main(){
	while(scanf("%d",&n),n){
	scanf("%d",&m);
		for(int i=1;i<maxn;i++){
			for(int j=1;j<maxn;j++)
				maps[i][j]=i==j?1:inf;
			}
			int a,b;
			double c;
			for(int i=0;i<m;i++){
				scanf("%d%d%lf",&a,&b,&c);
				maps[a][b]=maps[b][a]=max(maps[a][b],c);
			// printf("%.2f\n",maps[a][b]);
			}
			floyd();
			printf("%.6f percent\n",maps[1][n]);
		}
	}

