---
layout: post
title: "2012 ACM/ICPC Asia Regional Hangzhou Online Finding crosses搜索"
description: "2012 ACM/ICPC Asia Regional Hangzhou Online Finding crosses搜索"
category: "图论"
tags: []
---


	
	#include<cstdio>
	#include<cstring>
	#include<iostream>
	#include<vector>
	using namespace std;
	int n,map[100][100];
	struct point{
	int x,y;
	}p;
	int vis[100][100];
	bool zhong(int x,int y){
		int lefts=0,rights=0;
		for(int i=y-1;i>0;i--){
			if(map[x][i]==1){
				if(map[x-1][i]||map[x+1][i])return false;
				else lefts++;
			}
			else break;
		}
		for(int i=y+1;i<=n;i++){
			if(map[x][i]==1){
				if(map[x-1][i]||map[x+1][i])return false;
				else rights++;
			}
			else break;
		}
		
		if(lefts==rights)return true;
		else return false;
	}
	bool heng(int x,int y){
		int lefts=0,rights=0;
		for(int i=x-1;i>0;i--){
			if(map[i][y]==1){
				if(map[i][y-1]||map[i][y+1])return false;
					else lefts++;
			}else break;
		}
		for(int i=x+1;i<=n;i++){
			if(map[i][y]==1){
				if(map[i][y-1]||map[i][y+1])return false;
				else rights++;
			}
			else break;
		}
		if(lefts==rights)return true;
		else return false;
	}
	int main(){
		while(scanf("%d",&n),n){
			char str[100];
			memset(map,0,sizeof map);
			for(int i=1;i<=n;i++){
				scanf("%s",str);
				for(int j=0;j<n;j++){
					if(str[j]=='#')map[i][j+1]=1;
				}
			}
			int ans=0;
	
			for(int i=2;i<n;i++)
				for(int j=2;j<n;j++){
					if(map[i][j]&&map[i][j-1]&&map[i-1][j]&&map[i][j+1]&&map[i+1][j]){
			
					if(heng(i,j)&&zhong(i,j))
					ans++;
				}
			}
			printf("%d\n",ans);
		}
		return 0;
	}