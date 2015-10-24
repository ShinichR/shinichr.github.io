---
layout: post
title: "poj Quick answer&并查集"
description: "poj Quick answer&并查集"
category: "数据结构"
tags: []
---



题意大概可以看懂，对于删除的点，删前任何到它和它到的query都是NO，所以为了方便定义一个id，删除的点，id定义为一个未用值，这样就保证以后query无误.


###

	#include<iostream>
	#include<cstdio>
	#include<cstring>
	using namespace std;
	#define N 2000010
	int id[10005],father[N];
	int n,num;
	void init(){
		for(int i=1;i<=n;i++)father[i]=i,id[i]=i;
	}
	int find(int x){
		return father[x]==x?x:father[x]=find(father[x]);
	}
	int main(){
		while(scanf("%d",&n)!=EOF){
			init();
			num=n;
			char t[10];
			int yes=0,no=0,a,b;
			while(1){
				cin>>t;
				if(t[0]=='e') break;
				if(t[0]=='c'){
					scanf("%d%d",&a,&b);
					int px=find(id[a]);
					int py=find(id[b]);
					if(px!=py)father[py]=px;
				}
				else if(t[0]=='q'){
					scanf("%d%d",&a,&b);
					int px=find(id[a]);
					int py=find(id[b]);
					if(px!=py)no++;
					else yes++;
				}
				else {
					scanf("%d",&a);
					id[a]=++num;
					father[id[a]]=id[a];
				}
			}
			printf("%d , %d\n",yes,no);
		}
		return 0;
	}

###