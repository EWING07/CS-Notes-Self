# Notes for Learning Alogrithm

[Reference Book: Alogrithm 4](https://book.douban.com/subject/19952400//), [Reference Notes](https://labuladong.gitbook.io/algo/di-ling-zhang-bi-du-xi-lie/xue-xi-shu-ju-jie-gou-he-suan-fa-de-gao-xiao-fang-fa)

## 框架思维

1. 数据结构的存储方式

   - 数据结构的存储方式只有两种：数组（顺序存储）和链表（链式存储）。
   - hash表、栈、队列、堆、树、图等各种数据结构是上层建筑，都是在链表或数组上的特殊操作。
   - 「队列」、「栈」这两种数据结构既可以使用链表也可以使用数组实现。用数组实现，就要处理扩容缩容的问题；用链表实现，没有这个问题，但需要更多的内存空间存储节点
   - 「图」的两种表示方法，邻接表就是链表，邻接矩阵就是二维数组。邻接矩阵判断连通性迅速，并可以进行矩阵运算解决一些问题，但是如果图比较稀疏的话很耗费空间。邻接表比较节省空间，但是很多操作的效率上肯定比不过邻接矩阵。
   - 「散列表」就是通过散列函数把键映射到一个大数组里。而且对于解决散列冲突的方法，拉链法需要链表特性，操作简单，但需要额外的空间存储指针；线性探查法就需要数组特性，以便连续寻址，不需要指针的存储空间，但操作稍微复杂些。
   - 「树」，用数组实现就是「堆」，因为「堆」是一个完全二叉树，用数组存储不需要节点指针，操作也比较简单；用链表实现就是很常见的那种「树」，因为不一定是完全二叉树，所以不适合用数组存储。为此，在这种链表「树」结构之上，又衍生出各种巧妙的设计，比如二叉搜索树、AVL 树、红黑树、区间树、B 树等等，以应对不同的问题。
   - **优缺点如下**：
     - **数组**由于是紧凑连续存储,可以随机访问，通过索引快速找到对应元素，而且相对节约存储空间。但正因为连续存储，内存空间必须一次性分配够，所以说数组如果要扩容，需要重新分配一块更大的空间，再把数据全部复制过去，时间复杂度 O(N)；而且你如果想在数组中间进行插入和删除，每次必须搬移后面的所有数据以保持连续，时间复杂度 O(N)。
     - **链表**因为元素不连续，而是靠指针指向下一个元素的位置，所以不存在数组的扩容问题；如果知道某一元素的前驱和后驱，操作指针即可删除该元素或者插入新元素，时间复杂度 O(1)。但是正因为存储空间不连续，你无法根据一个索引算出对应元素的地址，所以不能随机访问；而且由于每个元素必须存储指向前后元素位置的指针，会消耗相对更多的储存空间。

2. 数据结构的基本操作

    - 基本操作无非遍历+访问，具体一点就是：增删查改，CRUD，非为两种：线性和非线性的。
    - 线性是以for/while为代表，非线性是以递归为代表。
      - 数组遍历框架，典型的线性迭代结构：
        
            ```java
            void traverse(int[] arr){
                for(int i = 0; i < arr.length; i++){
                    //迭代访问arr[i]
                }
            }
            ```
      - 链表遍历框架，兼具迭代和递归结构：

            ```java
            /* 基本的单链表节点 */
            class ListNode {
                int val;
                ListNode next;
            }

            void traverse(ListNode head) {
                for (ListNode p = head; p != null; p = p.next) {
                    // 迭代访问 p.val
                }
            }

            void traverse(ListNode head) {
                // 递归访问 head.val
                traverse(head.next)
            }
            ```
    - 二叉树遍历，典型的非线性递归遍历结构：

        ```java
            /*基本的二叉树节点*/
            class TreeNode{
                int val;
                TreeNode left, right;
            }
            void traverse(TreeNode root){
                traverse(root.left);
                traverse(root.right);
            }
        ```
    
    - 由二叉树框架，可以推出n叉树遍历框架：

        ```java
            /*基本的n叉树节点*/
            class TreeNode{
                int val;
                TreeNode[] children;
            }
            void traverse(TreeNode root){
                for(TreeNode child: children){
                    traverse(child);
                }
            }
        ```
    
3. 先刷二叉树，培养框架思维。