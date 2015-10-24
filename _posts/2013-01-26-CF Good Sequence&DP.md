---
layout: post
title: "CF Good Sequence&DP"
description: "CF Good Sequence&DP"
category: "DP"
tags: []
---


题意:

即为求最长的一个序列,该序列任意相邻元素不互斥。


我的做法为把每个数的所有因子(除1)保存在对应的vec[i]
dp[因子]表示当前以因子为终点的最长序列长度
循环对每个数的因子dp[因子]++，找出最大max;再更新每个数的所有因子为max；
最大即为ans


	#include<cstdio>
	#include<iostream>
	#include<vector>
	#include<cmath>
	#include<cstring>
	using namespace std;
	#define pb push_back
	int num[110000];
	int dp[110000];
	vector<int>vec[110000];
	void inits(int x,int idx){
		if(x==1){
			vec[idx].pb(1);
			return ;
		}
		int lens = (int)sqrt(x);
		for(int i=2;i<=lens;i++){
			if (x % i == 0)
			vec[idx].pb(i),vec[idx].pb(x/i);
		}
		if(lens * lens ==x)
		vec[idx].pop_back();

		vec[idx].pb(x);
	}
	int main(){
		int n;
		while(scanf("%d",&n)!=EOF){
			for(int i=0;i<110000;i++)vec[i].clear();
			for(int i=1;i<=n;i++){
				scanf("%d",&num[i]);
				inits(num[i],i);
			}
			memset(dp,0,sizeof dp);
			for(int i=1;i<=n;i++){
				int maxs = -1;
				int len = vec[i].size();
				for(int j=0;j<len;j++){
					printf("%d ",vec[i][j]);
					dp[vec[i][j]]++;
					maxs = max(maxs,dp[vec[i][j]]);
				}
				for(int j=0;j<len;j++){
					dp[vec[i][j]] = maxs;
				}
			}
			int ans = -1;
			for(int j=1;j<=n;j++){
				ans = max(ans,dp[num[j]]);
			}
			printf("%d\n",ans);
		}
		return 0;
	}