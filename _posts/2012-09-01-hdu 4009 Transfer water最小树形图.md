---
layout: post
title: "hdu 4009 Transfer water最小树形图"
description: "hdu 4009 Transfer water最小树形图"
category: "图论"
tags: []
---




题意：

有**n个地方需要供水，每个地方都可以选择
是自己挖井，还是从别的地方引水，根据方法不同
和每个地方的坐标不同，花费也不同.**

现在给出**每个地方的坐标，花费的计算方法，以及每个地方
可以给哪些地方供水**(对方可以从这里引水）
，求给**所有地方供水的最小花费**。

思路：显然对于每个地方，只有一种供水方式
就足够了，这样也能保证花费最小,为了方便思考
，我们引入一个**虚拟点，把所有自己挖井的
都连到这个点，边权为挖井的花费，而如果i能
从j处引水，则从j向i连边，边权为引水的花费
，然后对这个有向图，以虚拟点为根**，求最小树形
图即可（**最小树形图即为有向图的最小生成树**）。


算法步骤（固定根）：

* 1：求出让除根外的所有点的最短弧，并让pre[v]
记录弧的前端点（u -> v 是弧)。并让ans
加上最短弧集合的权值（in[i] 为点i 的最短弧的权值）。

* 2：判断有无解（无解的情况就多于0个点的in为无穷）

* 3：判断有没有环（环很自然就没有水到达这个环
里面的任何节点）。如果没自环，可以直接输入ans。

* 4：在有环的情况下，我们对图进行缩点。当弧
(id[u] - > id[v])的前端点不是环里面的点
，那么应该将该弧的权值变成 w(u -> v) - in[v]。
再回到步骤1



		#include <iostream>
		#include <cstdio>
		#include <cstring>
		#include <cmath>
		#define maxn 1005
		#define ll long long
		#define inf (1LL<<61)
		using namespace std;
		int pre[maxn],cnct,siz;
		int mincost[maxn],id[maxn],vis[maxn];
		ll in[maxn];
	
		struct point{
			int x,y,z;
		}p[maxn];
	
		struct EDGE{
			int u,v;
			ll cost;
		}ee[maxn*maxn+5];
		int n,x,y,z;
	
		ll zhuliu(int root,int nv,int ne){
			ll ret=0;
			while(1){
				int i,j,k;
				for(int i=0;i<nv;i++)in[i]=inf;
				for(int i=0;i<ne;i++){
					int u=ee[i].u,v=ee[i].v;
					if(ee[i].cost<in[v]&&u!=v){//这一步可以把自环	切掉，找出每个点的最小入边
						pre[v]=u;
						in[v]=ee[i].cost;
					}
				}
				for(int i=0;i<nv;i++){
					if(i==root)continue;
					if(in[i]==inf)return -1;//除了根以外有点没有入边,则根无法到达
				}
				int cntnode=0;
				memset(id,-1,sizeof id);
				memset(vis,-1,sizeof vis);
				in[root]=0;
	
				for(int i=0;i<nv;i++){
					ret+=in[i];
					int v=i;
					while(vis[v]!=i&&id[v]==-1&&v!=root){
						vis[v]=i;//这个点是从哪个始点遍历而来,做好标记
						v=pre[v];
					}
					if(v!=root&&id[v]==-1){//当这两个条件都满足时，	这说	明v被遍历两次，因此是在一个环当中
						for(int u=pre[v];u!=v;u=pre[u]){
							id[u]=cntnode;
						}
						id[v]=cntnode++;
					}
				}
				if(cntnode==0)return ret;//无环
				for(int i=0;i<nv;i++){
					if(id[i]==-1)id[i]=cntnode++;
				}
				for(int i=0;i<ne;i++){//重新建图
					int v=ee[i].v;
					ee[i].u=id[ee[i].u];
					ee[i].v=id[ee[i].v];
					if(ee[i].u!=ee[i].v)
						ee[i].cost-=in[v];
				}
				nv=cntnode;
				root=id[root];
			}
		}
	
		ll dis(point a,point b){
			ll ans =(abs(a.x-b.x)+abs(a.y-b.y)+abs(a.z-b.z))*y;
			if(a.z<b.z)
				ans+=z;
			return ans;
		}
	
		int main(){
			while(scanf("%d%d%d%d",&n,&x,&y,&z),x||y||z||n){
				for(int i=1;i<=n;i++)
					scanf("%d%d%d",&p[i].x,&p[i].y,&p[i].z);
				int cnt=0;
				for(int i=1;i<=n;i++){
					ee[cnt].u=0,ee[cnt].v=i,ee[cnt].cost=p[i].z*x;//虚拟一个root点
					cnt++;
					int m,a;
					scanf("%d",&m);
					while(m--){
						scanf("%d",&a);
						ee[cnt].u=i,ee[cnt].v=a;
						if(i==a)
							ee[cnt].cost=inf;//边无效
						else
							ee[cnt].cost=dis(p[i],p[a]);
						cnt++;
					}
				}
				ll ans=zhuliu(0,n+1,cnt);
				if(ans==-1)
					puts("poor XiaoA");
				else
				printf("%I64d\n",ans);
			}
		return 0;
		}
	
