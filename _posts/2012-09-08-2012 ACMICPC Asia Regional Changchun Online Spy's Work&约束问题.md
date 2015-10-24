---
layout: post
title: "2012 ACM/ICPC Asia Regional Changchun Online Spy's Work&约束问题"
description: "2012 ACM/ICPC Asia Regional Changchun Online Spy's Work&约束问题"
category: "图论"
tags: []
---

题意：


给一颗树，树上的每个节点的值大于等于1，给m个约束条件，问这些条件是否相互矛盾.
每个节点定义一个区间，表示他的取值范围，输入约束条件的时候就开始更新，当约束条件是=时判是否矛盾即可，最后dfs一下，更新所有点的区间范围，若有点min>max，则有矛盾


	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<vector>
	using namespace std;
	#define maxn 10100
	#define pb push_back
	vector<int>v[maxn];
	long long inf=(long long) 1 << 60;
	int n,m;
	long long minn[maxn],maxx[maxn];
	void init(){
		for(int i=0;i<maxn;i++)v[i].clear();
		for(int i=0;i<maxn;i++)minn[i]=1;
		for(int i=0;i<maxn;i++)maxx[i]=inf;
	}
	void dfs(int u){
		long long sizes=0;
		for(int i=0;i<v[u].size();i++){
			int vv=v[u][i];
			dfs(vv);
			sizes+=minn[vv];
		}
		sizes+=1;
		minn[u]=max(minn[u],sizes);//做交集
		maxx[u]=min(maxx[u],inf);
		// printf("u=%d,%d\n",u,sizes);
	}
	int main(){
		while(cin>>n){
			int c;
			init();
			for(int i=2;i<=n;i++){
				cin>>c;
				v[c].pb(i);
			}
			cin>>m;
			char s[10];
			int a;
			long long b;
			bool flag=false;
			for(int i=0;i<m;i++){
				cin>>a>>s>>b;
				if(s[0]=='='){
				if(minn[a]>b||maxx[a]<b)
				flag=true;
				minn[a]=max(minn[a],b);//4 = 2,4 =4
				maxx[a]=min(maxx[a],b);
			}
			else if(s[0]=='<'){
				maxx[a]=b-1;
			}
			else if(s[0]=='>'){
				minn[a]=b+1;
			}
		}
		if(!flag)dfs(1);
		for(int i=1;i<=n;i++){
			if(minn[i]>maxx[i])
			flag=true;
		}
		if(flag)printf("Lie\n");
			else printf("True\n");
		}
		return 0;
	}
