---
title: KMP算法代码
tags:
  - [数据结构]
categories:
  - gallery
date: 2022-12-02 22:08:19
---

```cpp
#include<iostream>
#include<vector>
#include<string>

using namespace std;

//未优化版本求next数组
void get_next(vector<int> &next, string s) {
    int i=0, j=-1;
    next[0] = -1;
    while(i<s.length()-1) {
        if(j ==-1 || s[i] == s[j]){
            ++i, ++j;
            next[i] = j;
        }
        else {
            j = next[j];
        }
    }
}

//优化版本求next数组
void get_nextval(vector<int> &next, string s) {
    int i=0,j=-1;
    next[0] = -1;
    while(i<s.length() - 1) {
        if(j == -1 || s[i] == s[j]) {
            ++i, ++j;
            if(s[i] != s[j]) next[i] = j;
            else next[i] = next[j];
        }
        else {
            j = next[j];
        }
    }
} 

int Index_KMP(string s1, string s2, vector<int> next) {
    int i=0, j=0;
    int len1 = s1.length();
    int len2 = s2.length();
    while(i < len1 && j < len2) {
        if(j==-1 || s1[i] == s2[j]) {
            ++i, ++j;
        }
        else {
            j = next[j];
        }
    }
    if(j>=s2.length()) {
        return i-s2.length();
    }
    else {
        return 0;
    }
}

int main() {
    string s1 = "aabbccddeeff";
    string s2 = "ccdd";
    vector<int> next(4, 0);
    // get_next(next, s2);
    get_nextval(next, s2);
    // for(int i=0;i<next.size();i++){
    //     cout << next[i] << " " << endl;
    // }
    int ind = Index_KMP(s1, s2, next);
    cout << ind << endl;
    return 0;
}

```
