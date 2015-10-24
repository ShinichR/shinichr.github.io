---
layout: post
title: "poj A Simple Problem with Integers&线段树"
description: "poj A Simple Problem with Integers&线段树"
category: "数据结构"
tags: []
---

题意很明了，如果线段树的每个节点只存sum（即区间总和），那每次更新就必须更新所有节点，这样效率太低。再定义一个增量lnc，对于每个区间sum=(sum+lnc*(r-l+1));每次更新时如果区间正好覆盖节点，则lnc增加。否则向下传递lnc值，并同时更新sum值，置lnc为0；查询时也要传递lnc值.
	
	
	#include <cstdio>
	#include <iostream>
	#include <vector>
	#include <set>
	#include <cstring>
	#include <string>
	#include <map>
	#include<list>
	#include <cmath>
	#include<stack>
	#include <algorithm>
	#include<iostream>
	#include<queue>
	using namespace std;
	#define MID(x,y) ((x+y)>>1)
	#define L(x) (x<<1)
	#define R(x) (x<<1|1)

	const int MAX = 50010*2;
	struct Tnode{
		int l,r;
		long long sum;
		long long lnc;
	};
	Tnode node[MAX*4];
		int a[MAX];
		int maxx,minn;
		void init(){
		memset(node,0,sizeof(node));
	}
	void update(int t,int l,int r,long long lnc){
		if(node[t].l==l&&node[t].r==r){
			node[t].lnc+=lnc;
			node[t].sum+=lnc*(r-l);
			return ;
		}
		if(node[t].lnc){
			node[R(t)].lnc+=node[t].lnc;
			node[L(t)].lnc+=node[t].lnc;
			node[R(t)].sum+=node[t].lnc*(node[R(t)].r-node[R(t)].l);
			node[L(t)].sum+=node[t].lnc*(node[L(t)].r-node[L(t)].l);
			node[t].lnc=0;
		}
		int mid=MID(node[t].l,node[t].r);
		if(l>=mid){
			update(R(t),l,r,lnc);
		}
		else{
			if(r<=mid)
			update(L(t),l,r,lnc);
			else {
				update(R(t),mid,r,lnc);
				update(L(t),l,mid,lnc);
			}
		}
		node[t].sum=node[R(t)].sum+node[L(t)].sum;
	}
	void build_tree(int t,int l,int r){
		node[t].l=l,node[t].r=r;
		if(r==l+1){
			node[t].sum=a[l];
			return ;
		}
		int mid=MID(l,r);
		build_tree(L(t),l,mid);
		build_tree(R(t),mid,r);
		node[t].sum=node[R(t)].sum+node[L(t)].sum;
	}
	long long ans;

	long long query(int t,int l,int r){
		if(node[t].l==l&&node[t].r==r){
		return node[t].sum;
		}
		if(node[t].lnc){
			node[R(t)].lnc+=node[t].lnc;
			node[L(t)].lnc+=node[t].lnc;
			node[R(t)].sum+=node[t].lnc*(node[R(t)].r-node[R(t)].l);
			node[L(t)].sum+=node[t].lnc*(node[L(t)].r-node[L(t)].l);
			node[t].lnc=0;
		}
		int mid=MID(node[t].l,node[t].r);
		if(l>=mid){
			return query(R(t),l,r);
		}
			else{
			if(r<=mid)
			return query(L(t),l,r);
			else {
				return query(R(t),mid,r)+query(L(t),l,mid);
			}
		}
	}
	int main(){
		int n,k;
		while(scanf("%d%d",&n,&k)!=EOF){
			init();
			for(int i=0;i<n;i++){
				scanf("%d",&a[i]);
			}
			build_tree(1,0,n+1);
			char s[10];
			int l,r;
			long long d;
			for(int i=0;i<k;i++){
				scanf("%s",s);
				if(s[0]=='Q'){
				scanf("%d%d",&l,&r);
				ans=query(1,l-1,r);
				printf("%I64d\n",ans);
			}
			else{
				scanf("%d%d%lld",&l,&r,&d);
				if(d!=0)
				update(1,l-1,r,d);
				}
			}
		}
		return 0;
	}

