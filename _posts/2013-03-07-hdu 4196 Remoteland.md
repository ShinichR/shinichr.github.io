---
layout: post
title: "hdu 4196 Remoteland"
description: "hdu 4196 Remoteland"
category: "数论"
tags: []
---




a题意即为:给一个n，求用不大与n的数组成一个最大的完全平方数


我们知道任意一个大于1的数n都可以分解为p1^k1*p2^k2...pi为n的素因子,ki即为相应的素因子个数。一个数要是完全平方数，它的每个素因子个数都要为偶数。至此思路为求n!这个数的所有素因子及个素,若素因子m个数为奇数,则减少一个，偶数就全要.但数据太大.


考虑把n!除掉那些奇数个因子的乘积，即求a=c/b，由于是取模的，直接除必然不行


c%mod=(a%mod)*(b%mod) 令A=a%mod，B=b%mod,C=c%mod;
A=a%mod=(a*1)%mod=(a%mod)*1%mod=(a%mod)*b^(mod-1)%mod(费马小定理,下面有证明)
=(a*b%mod)*(b^(mod-2))%mod = (c%mod)*(b%mod)^(mod-2)%mod=C*B^(mod-2)

利用上面的,先打素数表,从每个素数表枚举,分解n!的之质因子个数,若为奇数,就存入


	#include<cstdio>
	#include<iostream>
	#include<cstring>
	#include<cmath>
	                                                                 
	using namespace std;
	                                                                 
	#define N 10000000
	#define MOD 1000000007
	#define ll long long
	                                                                 
	bool flag[N+5]={0};
	ll fac[N+5];
	int prime[1000000],cnt=0;
	                                                                 
	void Prime(){
	    for(int i=2;i<sqrt(N+1.0);i++){
	        if(flag[i])continue;
	        for(int j=2;j*i<=N;j++){
	            flag[j*i]=true;
	        }
	                                                                 
	    }
	    for(int i=2;i<=N;i++){
	       if(!flag[i])prime[cnt++]=i;
	    }
	                                                                 
	}
	                                                                 
	ll pows(ll a,ll b){
	    ll ans=1;
	    while(b){
	        if(b&1)
	            ans=(ans*a)%MOD;
	        a=(a*a)%MOD;
	        b>>=1;
	    }
	    return ans;
	                                                                 
	}
	                                                                 
	int getsum(int n,int pri){
	    int ans=0;
	    while(n){
	        ans+=n/pri;
	        n=n/pri;
	    }
	    return ans;
	}
	                                                                 
	void getfac(){
	    fac[1]=1;
	    for(int i=2;i<=N;i++){
	        fac[i]=(fac[i-1]*i)%MOD;
	    }
	}
	                                                                 
	int main(){
	    Prime();
	    getfac();
	    int n;
	    while(scanf("%d",&n),n){
	            ll ret=1;
	            for(int i=0;i<cnt&&prime[i]<=n;i++){
	            int  c = getsum(n,prime[i]);
	            if(c&1)ret=(ret*prime[i])%MOD;
	        }
	      //  printf("ret=%d\n",ret);
	        printf("%I64d\n",(fac[n]*pows(ret,MOD-2))%MOD);
	    }
	                                                                 
	                                                                 
	    return 0;
	}


n!分解质因子:
N! = p1^k1*p2^k2*…
把N!分成两部分，即奇偶两部分
假设N是偶数
N！=1*2*3*4*5……N=(2*4*6……) * (1*3*5……)
因为有N/2个偶数，所以偶数部分可以提出N/2个2，
=2^(N/2) * (1*2*3*……N/2) * (1*3*5*……)
=2^(N/2) * (N/2)! * (1*3*5*……)


 
对比等式左右,N规模的问题可以转化成N/2的问题。上面假设了N是偶数，当然N是奇数时也是一样的
于是有递推公式 f(n,2) = f(n/2,2) + n/2，f()表示n!中2的个数。
用同样的方法可以推出 f(n,p) = f(n/p) + n/p，表示n!中素数p的个数。


 
费马小定理证明：


1)   假设p是素数,并且a是任意整数且a!≡0(mod p),则数
    a,2a,3a.....(p-1)a （mod p） 与数列
   1,2,3...(p-1) （mod p）相同，当然次序不保证相同
   证明: a,2a,3a...这p-1个数不被p整除，假设有ja≡ka(mod p)
肯定有p | (j-k)a ,又因 p不整除a，所以p | (j-k) 。
1<j,k<p，j-k<p，纵观一下,能被p整除的只有0，所以j=k
这表明 a,2a,3a.....(p-1)a （mod p）有p-1个不同的数,但（mod p）的结果只有p-1个，所以
a,2a,3a.....(p-1)a （mod p） 与数列 1,2,3...(p-1) （mod p）相同，当然次序不保证相同
 
2） 由1）得 a*2a*3a...*(p-1)a  ≡1*2*3...(p-1) (mod p)
a^(p-1)*(p-1)!≡(p-1)! (mod p)
得a^(p-1) ≡ 1(mod p)