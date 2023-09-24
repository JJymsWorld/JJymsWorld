---
title: Floyd各顶点之间最短路径
tags:
  - [数据结构]
categories:
  - gallery
date: 2022-11-28 21:07:50
---
两个矩阵，一个记录权值，一个记录路径。
![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/20221128210837.png)

![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/20221128211034.png)

![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/20221128211146.png)

![](https://cdn.jsdelivr.net/gh/JJymsWorld/Jymscloudiimmgg@main/img/20221128211308.png)

代码实现示例：
```cpp
#include<iostream>
#include<vector>

using namespace std;

void Floyd(Graph G, int path[][]) {
  int d[G.vexnum][G.vexnum];
  int v, w;
  for(v = 0;v<G.vexnum;v++) {
    for(w=0;w<G.vexnum;w++) {
      d[v][w] = G.arcs[v][w];
      path[v][w] = -1;
    }
  }
  int u;
  for(u=0;u<G.vexnum;u++) {
    for(v=0;v<G.vexnum;v++){
      for(w=0;w<G.vexnum;w++) {
        if((d[v][u] + d[u][w]) < d[v][w] && v!=w) {
          d[v][w] = d[v][u] + d[u][w];
          path[v][w] = u;
        } 
      }
    }
  }
}
```