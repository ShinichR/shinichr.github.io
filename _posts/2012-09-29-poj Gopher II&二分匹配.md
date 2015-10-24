---
layout: post
title: "poj Gopher II&二分匹配"
description: "poj Gopher II&二分匹配"
category: "图论"
tags: []
---


题意:


给地鼠的坐标和洞的坐标,每个洞只能进一只地鼠.地鼠有奔跑速度,如果地鼠暴露在外面的时间大于等于s秒,地鼠
就有危险。


求出最小的有危险的地鼠的个数。
首先枚举地鼠和洞，如果第i个地鼠到第j个洞的时间<=s，则建边;
最后就求最大匹配ans即可.答案即为n-ans

	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<vector>
	#include<cmath>
	using namespace std;
	struct point{
		double x,y;
	}gopher[110],hole[110];
	int links[250],map[250][250];
	bool visit[250];
	int n,m;
	double limit,speed;
	bool dfs(int a){
		for(int i=1;i<=n;i++){
			if(map[a][i]&&!visit[i]){
				visit[i]=true;
				if(links[i]==0||dfs(links[i])){
					links[i]=a;
				return true;
				}
			}
		}
		return false;
	}
	bool judge(int i,int j){
		double xx=(gopher[i].x-hole[j].x)*(gopher[i].x-hole[j].x)+(gopher[i].y-hole[j].y)*(gopher[i].y-hole[j].y);
		//printf("xx=%.2lf\n",xx);
		xx=sqrt(xx);
		xx/=(speed*1.0);
		if(limit>xx)return true;
		return false;
	}
	int find(){
		int count=0;
		for(int i=1;i<=n;i++){
		memset(visit,false,sizeof(visit));
			if(dfs(i))
			count++;
		}
		return count;
	}
	int main(){
		while(scanf("%d%d%lf%lf",&n,&m,&limit,&speed)!=EOF){
		
			memset(links,0,sizeof links);
			memset(map,0,sizeof map);
			for(int i=0;i<n;i++)
			scanf("%lf%lf",&gopher[i].x,&gopher[i].y);
			for(int i=0;i<m;i++)
			scanf("%lf%lf",&hole[i].x,&hole[i].y);
			for(int i=1;i<=n;i++){
				for(int j=1;j<=m;j++)
					if(judge(i-1,j-1))
					map[i][j]=1;
			}
			
			printf("%d\n",n-find());
		}
		return 0;
	}


