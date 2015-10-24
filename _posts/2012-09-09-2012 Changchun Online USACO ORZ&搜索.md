---
layout: post
title: "2012 Changchun Online USACO ORZ&搜索"
description: "2012 Changchun Online USACO ORZ&搜索"
category: "图论"
tags: []
---


题意：

给N(<=15)条边，问这15条边能组成多少个不同的三角形
直接暴力搜索，在判重即可.



	#include<cstdio>
	#include<iostream>
	#include<set>
	using namespace std;
	#define ll long long
	set<long long >sset;
	int arr[20];
	int cas,n;
	void dfs(int a,int b,int c,int i){
		if(i==n){
			if(a>b||b>c||a>c)return ;
			if(a&&b&&c&&a+b>c){
				ll ans=1000000000LL*a+100000LL*b+c;
				sset.insert(ans);
			}
			return ;
		}
		dfs(a+arr[i],b,c,i+1);
		dfs(a,b+arr[i],c,i+1);
		dfs(a,b,c+arr[i],i+1);
	}
	int main(){
		scanf("%d",&cas);
		while(cas--){
			scanf("%d",&n);
			for(int i=0;i<n;i++)
			scanf("%d",&arr[i]);
			sset.clear();
			dfs(0,0,0,0);
			printf("%I64d\n",sset.size());
		}
		return 0;
	}

