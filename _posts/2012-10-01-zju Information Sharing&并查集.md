---
layout: post
title: "zju Information Sharing&并查集"
description: "zju Information Sharing&并查集"
category: "数据结构"
tags: []
---


题意即为几个人分享信息，用并查集实现

	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<set>
	#include<map>
	using namespace std;
	int n;
	#define N 100005
	int f[N];
	set<int>s[N];
	map<string,int>m;
	void init(){
		for(int i=0;i<N;i++)
		{
			f[i]=i;
			s[i].clear();
		}
		m.clear();
	}
	int find(int x){
		return f[x]==x?x:f[x]=find(f[x]);
	}
	int main(){
		while(scanf("%d",&n)!=EOF){
			char str[20],name1[110],name2[110];
			int tot=0;
			init();
			for(int i=0;i<n;i++){
			scanf("%s",str);
			if(str[0]=='a'){
				scanf("%s",name1);
				m[name1]=tot;
				int qq,nn;
				scanf("%d",&qq);
				while(qq--){
					scanf("%d",&nn);
					s[tot].insert(nn);
				}
				tot++;
			}
			else if(str[0]=='s'){
				scanf("%s%s",name1,name2);
				int q1,q2;
				q1=find(m[name1]);
				q2=find(m[name2]);
				
				if(q1!=q2){
					while(!s[q2].empty()){
						int cur=*s[q2].begin();
						s[q1].insert(cur);
						s[q2].erase(cur);
					}
					m[name1]=q1;
					m[name2]=q1;
					f[q2]=q1;
				}
			}
			else {
				scanf("%s",name1);
				int q1=find(m[name1]);
				printf("%d\n",s[q1].size());
				}
			}
		}
		return 0;
	}

