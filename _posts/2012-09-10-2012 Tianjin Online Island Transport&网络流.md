---
layout: post
title: "2012 Tianjin Online Island Transport&网络流"
description: "2012 Tianjin Online Island Transport&网络流"
category: "图论"
tags: []
---


题意：

有那个岛屿，m条无向路，每条路有一个cap，求从最西的岛屿能运多少个乘客到最东的岛屿，纯网络流！




	#include<cstdio>
	#include<iostream>
	#include<cstring>
	using namespace std;
	#define maxn 100010
	#define maxm 410000
	const int inf=0x3f3f3f3f;
	struct EDGE{
		int to,from,next;
		int cap;
	}ee[maxm];
	int head[maxn],dep[maxn],gap[maxn];
	int n,m,src,des,siz;//src=start,des=end;
	void init(){
		siz=0;
		memset(head,-1,sizeof head);
	}
	void addedge(int u,int v,int cap){
		ee[siz].from=u,ee[siz].to=v,ee[siz].cap=cap;
		ee[siz].next=head[u];
		head[u]=siz++;
		ee[siz].from=v,ee[siz].to=u,ee[siz].cap=0;
		ee[siz].next=head[v];
		head[v]=siz++;
	}
	int que[maxn];
	void bfs(){
		memset(dep,-1,sizeof dep);
		memset(gap,0,sizeof gap);
		gap[0]=1;
		int front=0,rear=0;
		dep[des]=0;
		que[rear++]=des;
		int u,v;
		while(front!=rear){
			u=que[front++];
			front=front%maxn;
			for(int i=head[u];i!=-1;i=ee[i].next){
			v=ee[i].to;
			if(ee[i].cap!=0||dep[v]!=-1)continue;
				que[rear++]=v;
				rear=rear%maxn;
				++gap[dep[v]=dep[u]+1];
			}
		}
	}
	int cur[maxn],stack[maxn];
	int SAP(){
		int res=0;
		bfs();//init;
		int top=0;
		memcpy(cur,head,sizeof cur);
		int u=src,i;
		while(dep[src]<n){
			if(u==des){
			int tmp=inf,inser=n;
			for(int i=0;i!=top;i++){
				if(tmp>ee[stack[i]].cap){
					tmp=ee[stack[i]].cap;
					inser=i;
				}
			}
			for(int i=0;i!=top;i++){
				ee[stack[i]].cap-=tmp;
				ee[stack[i]^1].cap+=tmp;
			}
			res+=tmp;
			top=inser;
			u=ee[stack[top]].from;
		}
		if(u!=des&&gap[dep[u]-1]==0)break;
		for(i=cur[u];i!=-1;i=ee[i].next){
			if(ee[i].cap!=0&&dep[u]==dep[ee[i].to]+1)
			break;
		}
		if(i!=-1){
			cur[u]=i;
			stack[top++]=i;
			u=ee[i].to;
		}
		else{
			int minn=m;
			for(i=head[u];i!=-1;i=ee[i].next){
				if(ee[i].cap==0)continue;
				if(minn>dep[ee[i].to]){
				minn=dep[ee[i].to];
				cur[u]=i;}
			}
			--gap[dep[u]];
			++gap[dep[u]=minn+1];
			if(u!=src)
			u=ee[stack[--top]].from;
			}
		}
		return res;
	}
	int main(){
		int cas;
		scanf("%d",&cas);
		while(cas--){
			scanf("%d%d",&n,&m);
			int x,y;
			init();
			int Min=inf,Max=-inf;
			for(int i=1;i<=n;i++){
				scanf("%d%d",&x,&y);
				if(x<=Min){src=i,Min=x;}
				if(x>=Max){des=i,Max=x;}
			}
			int u,v,w;
			for(int i=0;i<m;i++){
				scanf("%d%d%d",&u,&v,&w);
				addedge(u,v,w);
				addedge(v,u,w);
			}
			int ans=SAP();
			printf("%d\n",ans);
		}
		return 0;
	}

