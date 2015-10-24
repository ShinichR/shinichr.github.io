---
layout: post
title: "poj Going from u to v or from v to u&弱连通图"
description: "poj Going from u to v or from v to u&弱连通图"
category: "图论"
tags: []
---

题意：

一个有向图，求对于图中任意两个节点，是否都至少存在a->b或者b->a路径之间的一条。

即是:

弱连通图(**将有向图的所有的有向边替换为无向边，所得到的图称为原图的基图。如果一个有向图的基图是连通图，则有向图是弱连通图。**)


判断图是否为弱连通图，**先进行tarjan缩点，最后统计出度和入度为0的点的个数，若都为一，则是弱连通图**。



	#include<cstdio>
	#include<iostream>
	#include<cstring>
	using namespace std;
	#define maxn 11000
	#define maxm 513000
	struct EDGE{
	int to,next;
	}ee[maxm];
	int head[maxn],vis[maxn],siz;
	int n,m;
	int atype,top;
	int dfn[maxn],low[maxn];
	int pos,dep;
	int in_sta[maxn],sta[maxn],belong[maxn];
	void addedge(int u,int v){
		ee[siz].to=v;
		ee[siz].next=head[u];
		head[u]=siz++;
	}
	void init(){
		atype=0;
		pos=1;
		dep=1;
		siz=0;
		top=0;
		memset(dfn,0,sizeof dfn);
		memset(low,0,sizeof low);
		memset(head,-1,sizeof head);
		memset(in_sta,0,sizeof in_sta);
		memset(sta,0,sizeof sta);
		memset(belong,0,sizeof belong);
	}
	void Tarjan(int u){
		dfn[u]=low[u]=++dep;
		sta[++top]=u;
		in_sta[u]=1;
		for(int e=head[u];e!=-1;e=ee[e].next){
			int v=ee[e].to;
			// printf("v=%d\n",v);
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
				in_sta[s]=0;
			}while(s!=u);
		}
	}
	int main(){
		int cas;
		scanf("%d",&cas);
		while(cas--){
			init();
			scanf("%d%d",&n,&m);
			int a,b;
			for(int i=0;i<m;i++){
				scanf("%d%d",&a,&b);
				addedge(a,b);
				//addedge(b,a);
			}
			for(int i=1;i<=n;i++){
				if(!dfn[i])Tarjan(i);
			}
			int in[maxn],out[maxn];
			memset(in,0,sizeof in);
			memset(out,0,sizeof out);
				
			for(int i=1;i<=n;i++){
				for(int j=head[i];j!=-1;j=ee[j].next){
					int v=ee[j].to;
					printf("v=%d\n",v);
					if(belong[i]!=belong[v]){
						in[belong[i]]++;
						out[belong[v]]++;
					}
				}
			}
			int ans1=0,ans2=0;
			for(int i=1;i<=atype;i++){
				if(in[i]==0)ans1++;
				if(out[i]==0)ans2++;
			}
			// printf("%d,%d\n",ans1,ans2);
			if(ans1==1&&ans2==1)
			printf("Yes\n");
			else
			printf("No\n");
		}
	}
