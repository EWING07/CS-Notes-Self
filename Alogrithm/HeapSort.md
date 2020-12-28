# Heap Sort

- **堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆；或者每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆。如下图：**

  ![img](https://images2015.cnblogs.com/blog/1024555/201612/1024555-20161217182750011-675658660.png)

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

java版：

```java
public class HeapSort {
    public int[] sortArray(int[] nums){
        int len = nums.length;
        //将数组整理成堆
        heapify(nums);

        for(int i = len - 1; i >= 1; ){
            swap(nums, 0, i);
            i--;
            siftDown(nums,0,i);
        }
        return nums;
    }
    private  void heapify(int[] nums){
        int len = nums.length;
        for(int i = (len - 1)/2; i>=0; i--){
            // 只需要从 i = (len - 1) / 2 这个位置开始逐层下移
            siftDown(nums,i,len-1);
        }
    }
    private void siftDown(int[] nums, int k, int end){
        while (2 * k + 1 <= end){
            int j = 2 * k + 1;
            if(j + 1<= end && nums[j + 1] > nums[j]){
                j++;
            }
            if(nums[j] > nums[k]){
                swap(nums,j,k);
            }else{
                break;
            }
            k = j;
        }
    }
    private void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```