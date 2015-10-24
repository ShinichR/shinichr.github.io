---
layout: post
title: "poj 3905Perfect Election&2-sat"
description: "poj 3905Perfect Election&2-sat"
category: "图论"
tags: []
---

题意：


有n个候选人，m组要求，每组要求关系到候选人中的两个人,
“+i +j”代表i和j中至少有一人
被选中，“-i -j”代表i和j中至少有一人不被选中.
“+i -j”代表i被选中和j不被选中这两个事件至少发生
一个，“-i +j”代表i不被选中和j被选中这两个事件至少发生一个。
问是否存在符合所有m项要求的方案存在。


初看貌似2-sat问题，但问题是怎么建边呢，**对于（+a,+b）建边-a->b表示选-a一定就要选+b，因为-a，+a只能选一个，而+a，+b必须选一个则选-a一定选+b，然后根据这种关系连边（连边a->b意义即是选a一定选b），对于点都加个nmax，保证不为负即可！**

最后判断所有对点是否都不在一个强连通分量上**（a，-a都在一个强连通分量上说明方案不可行），都不在说明有一组解满足所有对点只选一个出来且满足题上要求，输出1**。


###
	#include<cstdio>
	#include<iostream>
	#include<cstring>
	using namespace std;
	#define nMax 1005
	#define maxm 1100000
	struct EDGE{
		int to;
		int next;
	}edges[maxm*2];
	int n,atype,top;
	int dfn[nMax*2],low[nMax*2];
	int pos,dep;
	int in_sta[nMax*2],sta[nMax*2];
	int head[nMax*2];
	int belong[nMax*2];
	void addedge(int a,int b){
		edges[pos].to=b;
		edges[pos].next=head[a];
		head[a]=pos++;
	}
	void init(){
		atype=0;
		pos=1;
		dep=1;
		top=0;
		memset(dfn,0,sizeof dfn);
		// memset(low,0,sizeof low);
		memset(head,-1,sizeof head);
		memset(in_sta,0,sizeof in_sta);
		//memset(sta,0,sizeof sta);
		//memset(belong,0,sizeof belong);
		}
	void Tarjan(int u){
		dfn[u]=low[u]=++dep;
		sta[++top]=u;
		in_sta[u]=1;
		for(int e=head[u];e!=-1;e=edges[e].next){
			int v=edges[e].to;
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
	int judges(){
		for(int i=1;i<n;i++){
			if(belong[nMax-i]==belong[nMax+i])return 0;
		}
		return 1;
	}
	int m;
	int main(){
		while(scanf("%d%d",&n,&m)!=EOF){
			int a,b;
			init();
			for(int i=0;i<m;i++){
				scanf("%d%d",&a,&b);
				addedge(nMax-a,nMax+b);
				addedge(nMax-b,nMax+a);
			}
			for(int i=1;i<n;i++){
				if(!dfn[nMax-i])Tarjan(nMax-i);
				if(!dfn[nMax+i])Tarjan(nMax+i);
			}
			printf("%d\n",judges());
		}
	return 0;
	}
###

