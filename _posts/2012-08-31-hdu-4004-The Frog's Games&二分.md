---
layout: post
title: "hdu 4004 The Frog's Games&二分"
description: "hdu 4004 The Frog's Games&二分"
category: "图论"
tags: []
---


题意为一只青蛙，在m步内（包括m）从起始点0跳到终点L，中间有n个石头，青蛙必须跳在石头上。要求青蛙的跳跃方案中最长的那次跳跃距离最小.

针对**50万个点，处理的话就必须是nlog（n）的复杂度。二分就很明显了**，二分第一个石头的距离和总距离L,针对每个mid看在m步内能否走到L。

代码如下:

	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<algorithm>
	using namespace std;
	int arr[510000];
	int len,n,m;

	bool OK(int mid,int m){
		int cnt =0 ;
		int curDis =0 ;
		for(int i=0;i<=n;i++){
			if(curDis+mid<arr[i+1]&&curDis+mid>=arr[i]){
				curDis=arr[i];
				cnt++;
			}
			else if(curDis+mid<arr[i]){
				return false;
			}
		}
		return cnt>m?false:true;
	}

	int main(){
		while(scanf("%d%d%d",&len,&n,&m)!=EOF){
			for(int i=0;i<n;i++){
				scanf("%d",&arr[i]);
			}
			arr[n]=len;
			arr[n+1]=1000000000;
			sort(arr,arr+n);
			
			int l=arr[0],r=len;
			int mid;
			int kk=0;
			while(l<=r){
				mid=(l+r)/2;
				bool flag=OK(mid,m);
				if(flag){
					r=mid-1;kk=mid;
				}
				else l=mid+1;
			}
			printf("%d\n",kk);
		}
		return 0;
	}


