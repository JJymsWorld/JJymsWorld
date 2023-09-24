---
title: Dijkstra单源最短路径算法
tags:
  - [数据结构]
categories:
  - gallery
date: 2022-11-28 20:59:00
---
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/20221128210110.png)

使用三个数组，解法类似Prim求解最小生成树算法。

代码示例如下：

```cpp
#include<iostream>
#include<vector>

using namespace std;

void Dijkstra(Graph G, int v0, int path[]) {
    bool final_s[G.vexnum] = {false};
    int dis[G.vexnum];
    for(int i=0;i<G.vexnum;i++){
        final_s[i] = false;
        dis[i] = G.arcs[v0][i];
        if(dis[i] < INFINITY) {
            path[i] = v0;
        }
        else {
            path[i] = -1;
        } 
    }
    dis[v0] = 0;
    final_s[v0] = true;
    int v;
    int min;
    for(int i=0;i<G.vexnum;i++) {
        min = INFINITY;
        if(final_s[i] == false) {
            if(dis[i] < min) {
                min = dis[i];
                v = i;
            }
            final_s[v] = true;
            for(int w = 0;w<G.vexnum;w++) {
                if(final_s[w] == false && (min+G.arcs[v][w]) < dis[w])  {
                    dis[w] = min+G.arcs[v][w];
                    path[w] = v;
                }
            }

        }
    }

}

```
