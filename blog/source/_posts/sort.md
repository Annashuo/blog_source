---
title: sort method
date: 2017-01-04 21:39:49
categories: Introduction to algorithms
tags: Introduction to algorithms
---

各种排序算法的简单汇总。

<!--more-->
#### 插入排序
基本思想是假定待插入的元素前面的序列已经排序，将待插入的元素与前面序列元素一一进行比较直到找到合适的位置插入即可。

时间复杂度 $T(n)=\sum_{j=1}^{n-1}\vartheta (j)=\vartheta (n^{2})$

##### 升序

```c
	void insert_sort_ascend(int *A,int length)
	{
	    int i,j;
	    for(j=1;j<length;j++){
	        int key=A[j];
	        i=j-1;
	        while(i>=0&&A[i]>key){
	            A[i+1]=A[i];
	            i=i-1;
	        }
	        A[i+1]=key;
	    }
	}
```

##### 降序

```c
	void insert_sort_descend(int *A，int length)
	{
	    int i,j;
	    for(j=1;j<length;j++){
	        int key=A[j];
	        i=j-1;
	        while(i>=0&&A[i]<key){
	            A[i+1]=A[i];
	            i=i-1;
	        }
	        A[i+1]=key;
	    }
	}
```

#### 归并排序
merge函数是将两个已经排序的序列$A[p...q]$和$A[(q+1)...r]$归并为一个，merge_sort函数运用divide-conquer-combine的思想，递归的分割序列，再用merge函数合并序列。

时间复杂度 

$$T(n)=\left\{\begin{matrix}
\vartheta (1) \quad \quad \quad \quad \quad if\ n=1 & \\2T(n/2)+\vartheta(n) \quad if\ n>1 &
\end{matrix}\right.=\vartheta(nlgn)$$

(the master method case 2)

```
	void merge(int *A, int p, int q, int r){
	    int i,j,k;
	    int n1=q-p+1;
	    int n2=r-q;
	    int L[n1];
	    int R[n2];
	    for(i=1;i<=n1;i++)
	        L[i-1]=A[p+i-1];
	    for(j=1;j<=n2;j++)
	        R[j-1]=A[q+j];
	    i=0;
	    j=0;
	    for(k=p;k<=r;k++){
	        if((L[i]<=R[j]&&i<n1)||(j>=n2)){
	            A[k]=L[i];
	            i=i+1;
	        }
	        else{
	            A[k]=R[j];
	            j=j+1;
	        }
	    }
	}
	
	void merge_sort(int * A, int p, int r){
	    if(p<r){
	        int q=(p+r)/2;
	        merge_sort(A, p, q);
	        merge_sort(A, q+1, r);
	        merge(A, p, q, r);
	    }
	}
```

#### 二分排序
用二分查找代替遍历已排序的序列

时间复杂度 $T(n)=\vartheta(nlgn)$，其中二分查找的时间复杂度为$T(n)=\vartheta(lgn)$


```c
	void insert_sort_ascend(int *A，int length)
	{
	    int i,j;
	    for(j=1;j<length;j++){
	        int key=A[j];
	        i=j-1;
	        int k=binary_find(A, j, key);
	        if(A[k]<key)
	            k++;
	        while(i>=k){
	            A[i+1]=A[i];
	            i=i-1;
	        }
	        A[k]=key;
	    }
	}
	
	int binary_find(int *A, int num, int value)
	{
	    int start=0;
	    int end=num-1;
	    int mid=(start+end)/2;
	    while (A[mid]!=value&&start<end) {
	        if(A[mid]<value)
	            start=mid+1;
	        else end=mid-1;
	        mid=(start+end)/2;
	    }
	    return mid;
	}
```

#### 快速排序

Partition函数是将待排序序列$A[p...r]$分成两个$A[p...q-1]$和$A[q+1...r]$，前一个序列每一个元素都小于A[q],后一个序列每一个元素都大于A[q]。quick_sort函数运用divide-conquer-combine的思想，递归的分割序列。

时间复杂度为$T(n)=\vartheta(nlgn)$

```
	QuickSort(int* A, int p, int r){
		if(p<r){
			int q = Partition(A, p, r);
			QuickSort(A, p, q-1);
			QuickSort(A, q+1, r);
		}
	}
	
	int Partition(int* A, int p, int r){
		int i=p-1;
		int tmp;
		for(int j=p; j<r; j++){
			if(A[j]<A[r]){
				i++;
				tmp = A[i];
				A[i]=A[j];
				A[j]=tmp;
			}
		}
		tmp=A[i+1];
		A[i+1]=A[r];
		A[r]=tmp;
		return i+1;
	}
```
