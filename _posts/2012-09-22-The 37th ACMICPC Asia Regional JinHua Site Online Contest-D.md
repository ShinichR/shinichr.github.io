---
layout: post
title: "The 37th ACM/ICPC Asia Regional JinHua Site Online Contest-D"
description: "The 37th ACM/ICPC Asia Regional JinHua Site Online Contest-D"
category: "图论"
tags: []
---



题意很明了，枚举等号的所有情况，dfs左边的和右边的。
对于左右的所有情况可以先用一个数组存下来



	#include<iostream>
	#include<cstring>
	#include<cstdio>
	#include<algorithm>
	#include<map>
	#include<queue>
	#include<stack>
	#define ll long long
	#define oo 1000000000
	using namespace std;
	ll ans;
	int st[20][20],len,mid;
	char str[20];
	ll lefts,rights;
	void dfs2(int n,ll num){
		if(n==len+1){
			rights=num;
			if(lefts==rights)ans++;
			return ;
		}
		for(int i=n;i<=len;i++)
		dfs2(i+1,num+st[n][i]);
	}
	void dfs1(int n,ll num){
		if(n==mid){
			lefts=num;
			dfs2(mid,0);
			return ;
		}
		for(int i=n;i<mid;i++)
		dfs1(i+1,num+st[n][i]);
	}
	int main(){
		int i,j,k;
		while (gets(str+1)){
			ans=0;
			if (str[1]=='E') break;
			len=strlen(str+1);
			memset(st,0,sizeof st);
			for(int i=1;i<=len;i++){
				for(int j=i;j<=len;j++){
					for(int k=i;k<=j;k++){
						st[i][j]=st[i][j]*10+str[k]-'0';
					}
				}
			}
			for(mid=2;mid<=len;mid++)
			dfs1(1,0);
			printf("%I64d\n",ans);
		}
		return 0;
	}