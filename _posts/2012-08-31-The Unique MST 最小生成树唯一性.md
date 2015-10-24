---
layout: post
title: "poj The Unique MST 最小生成树唯一性"
description: "poj The Unique MST 最小生成树唯一性"
category: "图论"
tags: []
---



题意即为判断**MST是否唯一**，唯一则输出总权值,否则输出Not ...!
**先kruskal求MST,记录符合MST的边,然后枚举删边,若还能找到相同权值的MST，则肯定不惟一！！**

trick:数据有无法生成MST的情况这时**输出0**.

代码:


	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<vector>
	#include<queue>
	#include<algorithm>

	#define inf 0x7fffffff
	#define pb push_back
	using namespace std;

	struct EDGE{
	int u,v,cost;
	}ee[101*101];

	int n,m,p;
	int root[220];

	bool cmp(EDGE a,EDGE b){
		return a.cost<b.cost;
	}
	int find(int x){
		return root[x]==x?x:root[x]=find(root[x]);
	}
	queue<EDGE>que;
	EDGE un;

	int kruskal(int flags){//flags 标记这次求MST是否记录符合条件的边
		
		for(int i=0;i<=n;i++)root[i]=i;
		
		int ans=0;
		int pp=0;
		for(int i=0;i<p;i++){
			int u=ee[i].u,v=ee[i].v;
			if(ee[i].u==un.u&&ee[i].v==un.v&&ee[i].cost==un.cost){
			// printf("un=%d,%d,%d\n",un.u,un.v,un.cost);
				continue;
			}

			int px=find(u);
			int py=find(v);
			if(px!=py){
				root[px]=py;
				ans+=ee[i].cost;
				pp++;
				if(flags==1)que.push(ee[i]);//记录第一次MST的边
			}
		}
		// printf("pp=%d\n",pp);
		if(flags==1){
			if(pp==n-1)return ans;
			else return 0;//无MST
		}
		else return ans;
	}
	int main(){
		int t;
		scanf("%d",&t);
		while(t--){
			p=0;
			while(!que.empty())que.pop();

			scanf("%d%d",&n,&m);
			for(int i=0;i<m;i++){
				scanf("%d%d%d",&ee[p].u,&ee[p].v,&ee[p].cost);
				p++;
			}
			sort(ee,ee+p,cmp);
			un.u=n+1,un.v=n+1,un.cost=0;
			int ans1=kruskal(1);
			bool flag=false;
			while(!que.empty()){//枚举删除MST中的边
				un=que.front();
				// printf("%d,%d,%d\n",un.u,un.v,un.cost);
				que.pop();
				int ans2=kruskal(2);
				if(ans2==ans1){
					flag=true;//有相同的MST
					break;
				}
			}
			if(flag&&ans1!=0)
				printf("Not Unique!\n");
			else
				printf("%d\n",ans1);
		}
		return 0;
	}


