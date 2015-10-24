---
layout: post
title: "A very hard mathematic problem&暴力"
description: "A very hard mathematic problem&暴力"
category:
tags: []
---


题意:

求满足X^Z + Y^Z + XYZ = K
K (0 < K < 2^31)， (X < Y, Z > 1)
z的值最大为30（x<y所以y>=2）


当z==2时，直接利用完全平方公式判断即可，可变成(x+y)^2 = k
当z>2时，直接枚举z,x,y可以水过



	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<cmath>
	using namespace std;
	#define ll long long
	ll pows(ll a,int z){
		ll tmp=a;
		for(int i=1;i<z;i++)
		a*=tmp;
		return a;
	}
	int main(){
		int k;
		while(scanf("%d",&k),k){
			int ans=0;
			int temp = sqrt(k);
			if(temp*temp==k)
			ans += (temp-1)>>1;//超级优化
			for(int z=3;z<31;z++){
				for(ll x=1;;x++){
					ll u=pows(x,z);
					if(u>=k/2)break;
					for(ll y=x+1;;y++){
						ll v=pows(y,z);
						// cout<<x<<" "<<y<<" "<<z<<" "<<v<<endl;
						if(z*x*y+u+v>k)break;
						if(z*x*y+u+v==k){
							ans++;
							break;
						}
					}
				}
			}
			printf("%d\n",ans);
		}
		return 0;
	}

