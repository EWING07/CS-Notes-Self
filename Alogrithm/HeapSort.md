# Heap Sort

- complete Binary Tree: 从上往下，从左往右
- parent > children

- heapify
- 用数组构建，parent_index = (child - 1)/2

```c
#include <stdio.h>

void swap(int arr[], int i, int j){
	int temp = arr[i];
	arr[i] = arr[j];
	arr[j] = temp;
}

//关键操作，构建小堆
void heapify(int tree[], int n, int i){
	if(i >= n) return;
	int c1 = 2 * i + 1;
	int c2 = 2 * i + 2;
	int max = i;
	if(c1 < n && tree[c1] > tree[max]){
		max = c1;
	}
	if(c2 < n && tree[c2] > tree[max]){
		max = c2;
	}
	if(max != i){
		swap(tree, max, i);
		heapify(tree, n, max);
	}
}

//从倒数第二层开始heapify
void build_heap(int tree[], int n){
	int last_node = n - 1;
	int parent = (last_node - 1) / 2;
	int i;
	for(i = parent; i >= 0; i--){
		heapify(tree, n, i);
	}
}

//排序的话，先建立将root和最后一个
void heap_sort(int tree[], int n){
	build_heap(tree, n);
	int i;
	for(i = n - 1; i >= 0; i--){
		swap(tree,i,0);
		heapify(tree,i,0);
	}
}

int main(){
	int tree[] = {2, 5, 3, 1, 10, 4};
	int n = 6;
	heap_sort(tree,n);
	
  int i;
	for(i=0;i<n;i++){
		printf("%d ", tree[i]);
	}

	return 0;
}

```

