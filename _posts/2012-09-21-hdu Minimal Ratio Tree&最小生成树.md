---
layout: post
title: "hdu Minimal Ratio Tree&最小生成树"
description: "hdu Minimal Ratio Tree&最小生成树"
category: "数据结构"
tags: []
---


题意：

给出n个点，每个点之间的距离以矩阵形式给出，每个点都有点权。m个点的点权和a，m个点的树的边权和b，求a/b最小，输出最小情况的m个点。


我直接递归枚举从n个点中选m个点的所有情况，再在这m个点中求最小生成树，每次更新最小的a/b，保存结果即可。


	#include<cstdio>
	#include<iostream>
	#include<cstring>
	using namespace std;
	#define inf 99999999;
	int n,m;
	int node[20],edge[20][20];
	int pp,ans[20];
	int ee[20];
	double kk ;//= (99999999*1.0);
	void prim(){
		int dist[20],vis[20];
		for(int i=0;i<20;i++)
		vis[i]=0;
		for(int i=1;i<=n;i++)
		dist[i]=edge[ee[1]][ee[i]];
		dist[1]=0;
		int sum=0;
		int nnode=0;
		
		while(nnode<m){
			int mm=inf;
			int p=-1;
			for(int i=1;i<=m;i++){
				if(!vis[i]&&dist[i]<mm){
					mm=dist[i];
					p=i;
				}
			}
			vis[p]=1;
			nnode++;
			sum+=dist[p];
			for(int i=1;i<=m;i++){
				if(!vis[i]&&dist[i]>edge[ee[p]][ee[i]])
					dist[i]=edge[ee[p]][ee[i]];
			}
		
		}
		int nod=0;
		for(int i=1;i<=m;i++)
		nod+=node[ee[i]];
		double kkk=sum/(nod*1.0);
		if(kkk<kk){
			pp=0;
			kk=kkk;
			for(int i=1;i<=m;i++)
			ans[pp++]=ee[i];
		}
	//printf("sum=%d\n",sum);
	}
	void solve(int nn,int end){
	
		if(nn==m+1){
			prim();
			return ;
		}
		for(int i=end;i<=n;i++){
			ee[nn]=i;
			printf("ee[%d]=%d\n",nn,ee[nn]);
			solve(nn+1,i+1);
		}
	}
	int main(){
		while(scanf("%d%d",&n,&m),n||m){
			pp=0;
			kk = (99999999*1.0);
			for(int i=1;i<=n;i++){
				scanf("%d",&node[i]);
			}
			for(int i=1;i<=n;i++){
				for(int j=1;j<=n;j++){
					scanf("%d",&edge[i][j]);
				}
			}
			solve(1,1);
			for(int i=0;i<m-1;i++)
			printf("%d ",ans[i]);
			printf("%d\n",ans[m-1]);
		}
		return 0;
	}