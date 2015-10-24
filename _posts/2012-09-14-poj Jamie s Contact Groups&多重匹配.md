---
layout: post
title: "poj Jamie s Contact Groups&多重匹配"
description: "poj Jamie s Contact Groups&多重匹配"
category: "图论"
tags: []
---



题意：

有一个列表，列表中是一些人的姓名与该人可以从属的分组。现在让把每个人归到一个分组里，求最多人数的分组的最少人数。


**二分最多人数的分组的最小人数进行多重匹配**即可


网络流来判定，构图是由**s出发，连边到人容量为1，每个人和小组之间的对应关系连边，容量为1，小组和汇点t连边，容量为二分出来的mid，sap判断汇点是否是n**。

###



**二分多重匹配**:

	#include <cstdio>
	#include <numeric>
	#include <iostream>
	#include <vector>
	#include <set>
	#include <cstring>
	#include <string>
	#include <map>
	#include <cmath>
	#include <ctime>
	#include <algorithm>
	using namespace std;
	//1000.500
	#define maxn 1100
	#define maxm 550
	int N,M;
	int match[maxn][maxm];
	int maze[maxn][maxm],vis[maxn];
	int M_count;
	bool find_path(int u){
		for(int i=1;i<=M;i++){
		if(!vis[i]&&maze[u][i]){
			vis[i]=true;
			if(match[i][0]<M_count){
				match[i][++match[i][0]]=u;
				return true;
			}
			for(int j=1;j<=match[i][0];j++){
				if(find_path(match[i][j])){
						match[i][j]=u;
						return true;
					}
				}
			}
		}
		return false;
	}
	bool mult_match(){
		memset(match,0,sizeof(match));
		for ( int i=1;i<=N;i++)
		{
			memset(vis,0,sizeof(vis));
			if (!find_path(i)) return false;
		}
		return true;
	}
	int main(){
		while(scanf("%d%d",&N,&M),N||M){
			memset(maze,0,sizeof maze);
			char s[22];
			for(int i=1;i<=N;i++){
				scanf("%s",s);
				while(1){
					int d;
					scanf("%d",&d);
					maze[i][d+1]=1;
					if(getchar()=='\n')
						break;
				}
			}
			int left=1,right=N;
			while(left<right){
				M_count=(left+right)>>1;
				if(mult_match()){
					right=M_count;
				// cout<<"right="<<M_count<<endl;
				}
				else left=M_count+1;
				//cout<<"high="<<M_count<<endl;
			}
			printf("%d\n",right);
		}
		return 0;
	}

###