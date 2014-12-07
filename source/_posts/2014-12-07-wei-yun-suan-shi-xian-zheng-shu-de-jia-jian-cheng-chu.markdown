---
layout: post
title: "位运算实现整数的加减乘除"
date: 2014-12-07 17:57:53 +0800
comments: true
categories: ['算法']
---

```cpp
#include <iostream>
#include <cstdio>
using namespace std;

int AddUonRecursive(int a,int b){ // 非递归加法 
	for(int i=1; i; i<<=1)
		if(b&i)
			for(int j=i; j; j<<=1)
				if(a&j) a &= ~j;
				else { a |= j;break; }
	return a;
} 

int AddRecursive(int a,int b){ //递归加法 
	return b ? AddRecursive( a^b, (a&b)<<1 ) : a;
}

int GetReverse(int a){ // 取补码
	int i;
	for(i=1; i&&((a&i)==0); i<<=1);
	for(i<<=1; i; i<<=1) a ^= i;
	return a;
}

int Minus(int a,int b){ //减法 
	//取减数补码 
	b = GetReverse(b);
	return  AddRecursive(a,b);
}

int Multiple(int a,int b){ //乘法 
	int ans = 0;
	for(int i=1; i; i<<=1,a<<=1)
		if(b&i)
			ans = AddRecursive(ans,a);
		return ans;
} 

int Division(int a,int b){ //除法(正负都可以)

	int IsPositive = 1;

	if(a & (1<<31)) { a = GetReverse(a); IsPositive ^= 1;}
	if(b & (1<<31)) { b = GetReverse(b); IsPositive ^= 1;}

	int ans = 0;
	for(int i=31; i>=0; i--){
		if((a>>i) >= b){ //不能这样比较a >= (b << i) 会溢出 
			ans = AddUonRecursive(ans,1<<i);
			a = Minus(a,b<<i);
		}
	}
	return IsPositive ? ans : GetReverse(ans);
} 

int main(){
	
	int a,b;
	while(scanf("%d%d",&a,&b) != EOF){
		printf("非递归加法：%d\n",AddUonRecursive(a,b));
		printf("递归加法：%d\n",AddRecursive(a,b));
		printf("减法：%d\n",Minus(a,b));
		printf("乘法：%d\n",Multiple(a,b));
		printf("除法：%d\n",Division(a,b));
	}
	return 0;
}

/*
6 -2
非递归加法：4
递归加法：4
减法：8
乘法：-12
除法：-3                                                                    
*/

```

参考：http://blog.csdn.net/walkinginthewind/article/details/6886489
