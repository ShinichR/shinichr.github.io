---
layout: post
title: "poj network of schools 强联通&缩点"
description: "poj network of schools 强联通&缩点"
category: "图论"
tags: []
---

题意:

**task A** :在已经存在的图中，最少需要给多少个点，才能把这个图给遍历完。就是**求入度为0点**。

**task B **:至少要加**多少条边，才能使得从任何一个顶点出发，都能到达全部顶点,即强联通**。即出度为0的点连入度为0的点，即找两个中的max就是答案,**特殊的当原图只有一个强联通分量时，taskb的答案为0 **.




	#include<cstdio>
	#include<iostream>
	#include<cstring>
	using namespace std;
	struct EDGE{
		int u,v;
		int next;
	}ee[110*110];

	int head[110],siz,n,cnt;
	int dfn[110],low[110];
	int stacks[110],sc[110];
	int belongs[110];
	int top,cctime;

	void init(){
		siz=1,cnt=0;
		top=0,cctime=0;
		memset(dfn,0,sizeof dfn);
		memset(low,0,sizeof low);
		memset(belongs,0,sizeof belongs);
		memset(stacks,0,sizeof stacks);
		memset(head,-1,sizeof head);
	}
	void addedge(int u,int v){
		ee[siz].u=u,ee[siz].v=v,ee[siz].next=head[u];
		head[u]=siz++;
	}
	void tarjan(int s){// tarjan求强联通
		sc[++top]=s;//入栈
		dfn[s]=low[s]=++cctime;
		stacks[s]=1;
		for(int i=head[s];i!=-1;i=ee[i].next){
			int t=ee[i].v;
			if(!dfn[t]){
				tarjan(t);
				low[s]=min(low[s],low[t]);
			}
			else if(stacks[t]){
				low[s]=min(low[s],dfn[t]);
			}
		}
		
		if(dfn[s]==low[s]){
			int t;
			cnt++;
			do{
				t=sc[top--];
				stacks[t]=0;//出栈
				belongs[t]=cnt;//标记点属于哪个强联通
			}while(s!=t);
		}
	}
	int main(){
		while(scanf("%d",&n)!=EOF){
			init();
			for(int i=1;i<=n;i++){
				int c;
				while(scanf("%d",&c),c){
					addedge(i,c);
				}
			}
			for(int i=1;i<=n;i++){//求强联通分量
				if(!dfn[i]){
					tarjan(i);
				}
			}
		
			int vis[110][110];
			int in[110],out[110];
			memset(vis,0,sizeof vis);
			memset(in,0,sizeof in);
			memset(out,0,sizeof out);
			for(int i=1;i<=n;i++){
				for(int j=head[i];j!=-1;j=ee[j].next){
				int vv=ee[j].v;
				if(belongs[i]!=belongs[vv]&&!vis[belongs[i]][belongs[vv]]){
					vis[belongs[i]][belongs[vv]]=1;
					//printf("%d,%d\n",belongs[i],belongs[vv]);
					in[belongs[vv]]++;
					out[belongs[i]]++;
					}
				}
			}
			int taska=0,taskb=0;
			for(int i=1;i<=cnt;i++){//强联通的入出度统计
				if(!in[i])taska++;
				if(!out[i])taskb++;
			}
			if(cnt==1)
				printf("1\n0\n");//
			else
				printf("%d\n%d\n",taska,max(taska,taskb));
		}
		return 0;
	}
