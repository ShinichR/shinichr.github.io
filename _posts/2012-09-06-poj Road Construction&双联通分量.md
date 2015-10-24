---
layout: post
title: "poj Road Construction&双联通分量"
description: "poj Road Construction&双联通分量"
category:  "图论"
tags: []
---




题意：在一个岛上有一些旅游景点，旅游景点之间有路径进。当施工的时候，若一条路在施工，则这条路是无法通行。问至少还需要增加多少条路，能够保证在施工的时候，任意两个旅游景点之间仍然是可通的。
**在一个无向图中，如果一条边是割边，那么占用这条边，必有不可达的点。可以先缩点，如果缩点后有点度为一，则这个点只有一条路径可以到达，需要加一条边，答案即为度为1的点个数m---（m+1）/2**



	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#define maxn 1010
	using namespace std;
	int n,m;
	struct EDGE{
		int u,v;
		int next;
	}ee[maxn*2];
	int siz,head[maxn*2];
	int dfn[maxn],low[maxn];
	int stack[maxn],sc[maxn],belong[maxn];
	int top,cnt,Ctime;
	void addedge(int u,int v){
		ee[siz].u=u,ee[siz].v=v,ee[siz].next=head[u];
		head[u]=siz++;
	}
	void init(){
		siz=0;
		top=0,cnt=0,Ctime=0;
		memset(belong,0,sizeof belong);
		memset(dfn,0,sizeof dfn);
		memset(low,0,sizeof low);
		memset(head,-1,sizeof head);
		memset(stack,0,sizeof stack);
	}
	void tarjan(int u,int ff){
		sc[++top]=u;
		stack[u]=1;
		dfn[u]=low[u]=++cnt;
		for(int i=head[u];i!=-1;i=ee[i].next){
			int v=ee[i].v;
			if(ff==v)continue;//
			if(!dfn[v]){
				tarjan(v,u);
				low[u]=min(low[u],low[v]);
				if(low[v]>dfn[u]){//桥
					printf("low[%d]=%d,dfn[%d]=%d\n",v,low[v],u,dfn[u]);
					++Ctime;
					int vv;
					do{
					vv=sc[top--];
					// printf("%d,%d\n",vv,Ctime);
					stack[vv]=0;
					belong[vv]=Ctime;
				}while(vv!=v);
			}
		}
		else
			low[u]=min(low[u],dfn[v]);
		}
	}
	int main(){
		while(scanf("%d%d",&n,&m)!=EOF){
			int a,b;
			init();
			for(int i=0;i<m;i++){
				scanf("%d%d",&a,&b);
				addedge(a,b);addedge(b,a);
			}
			tarjan(1,1);
			printf("Ctime=%d\n",Ctime);
			int degree[maxn];
			int viss[maxn][maxn];
			memset(degree,0,sizeof degree);
			memset(viss,0,sizeof viss);
			for(int i=1;i<=n;i++){
			
				for(int j=head[i];j!=-1;j=ee[j].next){
					int v=ee[j].v;
					if(viss[i][v])continue;
					// printf("%d---%d---%d\n",v,belong[i],belong[v]);
					if(belong[v]!=belong[i]){
						degree[belong[v]]++;
						degree[belong[i]]++;
						viss[i][v]=viss[v][i]=1;
					}
				}
				// printf("\n");
			}
			int ans=0;
			for(int i=1;i<=Ctime;i++){
				printf("%d\n",degree[i]);
				if(degree[i]==1)ans++;
			}
			printf("%d\n",(ans+1)/2);
		}
		return 0;
	}


