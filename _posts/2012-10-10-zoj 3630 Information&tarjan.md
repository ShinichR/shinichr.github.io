---
layout: post
title: "zoj 3630 Information&tarjan"
description: "zoj 3630 Information&tarjan"
category: "图论"
tags: []
---


题意：

给出一个有向图，要求删去一个点使得剩下的图中含有点数最多的强连通分量最小。
直接枚举删点，进行tarjan

	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<vector>
	using namespace std;
	#define inf 1<<30
	const int nMax=3150;
	int n,atype,top;
	int dfn[nMax],low[nMax];
	int pos,dep;
	int in_sta[nMax],sta[nMax];
	int somes[nMax];
	struct edge{
		int a,next;
	}edges[20100];
	int head[nMax];
	int belong[nMax];
	int node;
	void addedge(int a,int b){
		edges[pos].a=b;
		edges[pos].next=head[a];
		head[a]=pos;
		pos++;
	}
	void init(){
		pos=1;
		memset(head,0,sizeof head);
	}
	void inits(){
		atype=0;
		dep=1;
		top=0;
		memset(dfn,0,sizeof dfn);
		memset(low,0,sizeof low);
		memset(in_sta,0,sizeof in_sta);
		memset(sta,0,sizeof sta);
		memset(belong,0,sizeof belong);
		memset(somes,0,sizeof somes);
	}
	void Tarjan(int u){
		dfn[u]=low[u]=++dep;
		sta[++top]=u;
		in_sta[u]=1;
		for(int e=head[u];e;e=edges[e].next){
			int v=edges[e].a;
			if(v==node)continue;
			if(!dfn[v]){
				Tarjan(v);
				low[u]=min(low[u],low[v]);
			}
			else if(in_sta[v]){
				low[u]=min(low[u],dfn[v]);
			}
		}
		if(dfn[u]==low[u]){
			atype++;
			int s;
			do{
			s=sta[top--];
			belong[s]=atype;
			somes[atype]++;
			in_sta[s]=0;
			}while(s!=u);
		}
	}
	int main(){
		int n,m;
		while(scanf("%d%d",&n,&m)!=EOF){
			int a,b;
			int ans=inf;
			init();
			for(int i=0;i<m;i++){
				scanf("%d%d",&a,&b);
				addedge(a,b);
			}
			for(node=0;node<n;node++){
				inits();
				for(int i=0;i<n;i++){
					if(!dfn[i]&&i!=node){
						Tarjan(i);
					}
				}
				int maxx=0;
				for(int i=1;i<=atype;i++){
					maxx=max(maxx,somes[i]);
				}
				ans=min(ans,maxx);
			}
			if(ans==1)ans=0;
			printf("%d\n",ans);
		}
		return 0;
	}