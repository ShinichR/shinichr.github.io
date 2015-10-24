---
layout: post
title: "hdu 4016 dfs&剪枝"
description: "hdu 4016 dfs&剪枝"
category: "图论"
tags: []
---


题意：

给出一个**2×n的矩形（n<=8）**，每次给一个子矩形染色，之后的染色覆盖以前的颜色，问到达目标状态需要多少次

因为每一次涂色都有一个被涂，所以用**二进制来表示状态再好不过，总过最多有有16个，即16位的二进制数便可以保存**

**搜索顺序是每块被染色顺序，相同则标记。扩展就是每块向前向后搜，有单行双行两种情况**.


	#include<iostream>
	#include<cstdio>
	#include<cstring>
	#include<queue>
	#include<string>
	using namespace std;
	struct Node{
		int val,step;
		//当前情况与步数
	}s,e;
	char str[20];
	bool vis[1<<16];
	//16位二进制数表示，是否与目标颜色一致
	int n;
	int bfs(){
		queue<Node>que;
		while(!que.empty())
		que.pop();
		que.push(s);
		memset(vis,false,sizeof(vis));
		vis[0]=true;
		while(!que.empty()){
			Node q=que.front();
			que.pop();
			int u=q.val;
			for(int i=0;i<2*n;i++){
				int v=u;
				if(v&(1<<i))continue;
				for(int j=i;j<(i/n+1)*n;j++){//单行向右扩展
					if(v&(1<<j))break;//
					if(str[i]==str[j])v|=(1<<j);
				}
				for(int j=i-1;j>=(i/n)*n;j--){
					if(v&(1<<j))break;
					if(str[i]==str[j])v|=(1<<j);
				}
				if(!vis[v]){
					vis[v]=true;
					Node tt;tt.val=v,tt.step=q.step+1;
					if(v==(1<<(n<<1))-1)return tt.step;
					//printf("%d,%d-----%d,%d,%d\n",q.val,q.step,tt.val,tt.step,i);
					que.push(tt);
				}
				if(i>=n)continue;//画两行的矩阵
				v=u;
				if(v&(1<<(i+n)))continue;
				for(int j=i;j<n;j++){
					if(v&(1<<j)|v&(1<<(j+n)))break;//当前点或当前点的下面相同
					if(str[i]==str[j])v|=(1<<j);
					if(str[i]==str[j+n])v|=(1<<(j+n));
				}
				for(int j=i-1;j>=0;j--){
					if(v&(1<<j)|v&(1<<(j+n)))break;//当前点或当前点的下面相同
					if(str[i]==str[j])v|=(1<<j);
					if(str[i]==str[j+n])v|=(1<<(j+n));
				}
				if(!vis[v]){
					vis[v]=true;
					Node tt;tt.val=v,tt.step=q.step+1;
					if(v==(1<<(n<<1))-1)return tt.step;
					//printf("%d,%d-----%d,%d,%d\n",q.val,q.step,tt.val,tt.step,i);
					que.push(tt);
				}
			}
		}
	}
	int main(){
		int t,cas=0;
		scanf("%d",&t);
		while(t--){
			scanf("%d",&n);
			scanf("%s%s",str,str+n);
			s.val=s.step=0;
			printf("Case #%d: %d\n",++cas,bfs());
		}
		return 0;
	}


