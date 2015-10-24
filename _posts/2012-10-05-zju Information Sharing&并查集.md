---
layout: post
title: "zju Information Sharing&并查集"
description: "zju Information Sharing&并查集"
category: "数据结构"
tags: []
---



题意即为给个家谱,姓名前几个点表示是第几代；
输出有:

* 1)dfs输出家谱，
* 2)输出某个人兄弟个数。
* 3)输出最近公共祖先。


注意若a是b的father，则最近公共祖先为a的father.
用3个map，分别表示人物所在层数，父亲是谁，他的儿子。保存儿子的用set，这样保证字典顺序。再用一个map用来输入时保存上一个的层数。
最后输出即可。

	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<vector>
	#include<set>
	#include<map>
	#include<stack>
	using namespace std;
	int main(){
		char name[500];
		int n,siz;
		while(scanf("%d",&n),n){
			map<string,set<string> >son;//存儿子，按字典序
			map<string,string >father;//存父亲
			map<string,int>cnt;//层数
			map<int,string>sta;//
			scanf("%s",name);
			sta[0]=string(name);
			cnt[name]=0;
			for(int i=1;i<n;i++){
				scanf("%s",name);
				int ll=0;
				for(ll=0;name[ll]=='.';ll++);
				cnt[name+ll]=ll;
				father[name+ll]=sta[ll-1];
				sta[ll]=name+ll;
				son[father[name+ll]].insert(name+ll);
			}
			int qq;
			char str[20];
			scanf("%d",&qq);
			for(int i=0;i<qq;i++){
				scanf("%s",str);
				if(str[0]=='L'){//家谱递归输出
				stack<string>stas;
				stas.push(sta[0]);
				while(!stas.empty()){//DFS
					string tmp=stas.top();stas.pop();
					for(int i=0;i<cnt[tmp];i++)putchar('.');
					printf("%s\n",tmp.c_str());
					for(set<string>::reverse_iterator it=son[tmp].rbegin();it!=son[tmp].rend();it++){
						//cout<<*it<<endl;
						stas.push(*it);//set从大到小排序，这里dfs输出时就从小到大
					}
				}
			}
			else if(str[0]=='b'){//兄弟个数
				char name1[500];
				scanf("%s",name1);
				if(name1==sta[0]){
					printf("1\n");continue;
				}
				printf("%d\n",son[father[name1]].size());
			}else {//最近公共祖先
				char name1[500],name2[500];
				string n1,n2;
				scanf("%s%s",name1,name2);
				n1=father[name1],n2=father[name2];
				while(cnt[n1]!=cnt[n2]){//相同层数
					if(cnt[n1]>cnt[n2]){
						n1=father[n1];
					}
					else n2=father[n2];
				}
				while(n1!=n2){
					n1=father[n1];
					n2=father[n2];
				}
				printf("%s\n",n1.c_str());
				}
			}
		}
		return 0;
	}
