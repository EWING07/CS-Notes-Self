# LeetCode 2nd Round For Reveiwing and Improving

## Plan

- [腾讯tag](https://leetcode-cn.com/leetbook/read/tencent/xxqfy5/)
- [字节tag](https://leetcode-cn.com/explore/interview/card/bytedance/?utm_campaign=bytedance_2020fall_explore&utm_medium=leetcode_leetbook_banner&utm_source=explore&gio_link_id=nP2lpDV9)
- [剑指offer](https://leetcode-cn.com/problemset/lcof/)
- [程序员面试金典](https://leetcode-cn.com/problemset/lcci/)
- reviewing problems solved in the 1st round
- PS: think and do it, but think first.

## Tencent tag

1. 寻找两个正序数组的中位-hard

  [参考思路：解法三](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/): 求第k小的数。 

2. 最长回文子串-medium

     中心扩散法和动态规划：`dp[l][r]`表示字符串从l到r这段是否为回文。初始状态，`l=r` 时，此时 `dp[l][r]=true`。状态转移方程，`dp[l][r]=true` 并且`(l-1)`和`(r+1)`两个位置为相同的字符，此时`dp[l-1][r+1]=true`。

3. atoi-medium：注意边界条件的处理，对正负数分情况处理，正数：当转换之后的值比INT_MAX大的时候，返回INT_MAX。负数：先不加符号，此时转化之后的值比INT_MAX大时，返回INT_MIN.

4. 最长公共前缀：巧用substring

5. 3Sum和3Sum Closest: 利用双指针，并且注意重复结果的情况。

6. 有效括号：利用栈的性质。
  
7. [盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/solution/container-with-most-water-shuang-zhi-zhen-fa-yi-do/)：利用双指针，向中间移动，比较面积

8. [字符串相乘优化](https://leetcode-cn.com/problems/multiply-strings/solution/you-hua-ban-shu-shi-da-bai-994-by-breezean/)：
![优化](优化竖式.png)

9. 反转字符串：双指针，反转字符串中的单词，可以使用java自带的函数strip和trim，以及利用stringbuilder对其进行操作。

10. 除自身之外相乘，考虑两部分，对角线的左上部分和右下部分。

11. 存在重复的元素，可以用set的特性，或者使用hashmap，除此之外还可以通过先对数组进行排序，比较相邻元素是否相等来进行判断。

12. 环形链表：使用快慢指针，面试喜欢考。关于其他链表的面试问题总结，可以参考：[一文搞定常见的链表问题](https://leetcode-cn.com/problems/linked-list-cycle/solution/yi-wen-gao-ding-chang-jian-de-lian-biao-wen-ti-h-2/)，follow up：如果存在环，如何判断环的长度呢？方法是：快慢指针相遇后继续移动，知道第二次相遇。两次相遇的移动次数即为环的长度。

13. [环形链表2](https://leetcode-cn.com/problems/linked-list-cycle-ii/solution/linked-list-cycle-ii-kuai-man-zhi-zhen-shuang-zhi-/): 快慢指针，两次相遇，第一次相遇后慢指针走了a+nb，要想找到环的入口，需要让慢指针再走a步，但是我们不知道a的指是多少，因此需要将快指针重新指向head，slow和fast同时每轮向前走一步，当第二次相遇的时候，这时候同时指向链表环的入口（a指链表头部到链表环入口的距离，b指链表环的长度） 

14. 相交链表：拼接两链表，设定两个指针分别指向两个链表头部，一起向前走直到其中一个到达末端，另一个与末端距离则是两链表的长度差，最终两链表即可同时走到相交点。

15. 回文数：只需要revert一半，然后进行比较，中间的数不用管。

16. 只出现一次的数，使用异或运算。

17. 多数元素：三种思路：hashmap、排序思路、摩尔投票法：因为只需要考虑出现次数大于n/2次，所以多数元素和其他元素互相抵消，抵消到最后至少剩余1个多数元素。

18. 2的幂：使用位运算，判断 `n & (n-1) == 0`， 说明n中只有一个1。

19. 排序链表：归并排序（递归），先分割（使用快慢指针分割），再合并（双指针法合并）

20. 搜索旋转排序数组：考虑到时间复杂度，使用二分查找，考虑两类情况：

      第一类 2 3 4 5 6 7 1 这种，也就是`nums[start] <= nums[mid]`。此例子中就是 2 <= 5。
      这种情况下，前半部分有序。因此如果`nums[start] <=target<nums[mid]`，则在前半部分找，否则去后半部分找。
      第二类 6 7 1 2 3 4 5 这种，也就是`nums[start] > nums[mid]`。此例子中就是 6 > 2。
      这种情况下，后半部分有序。因此如果`nums[mid] <target<=nums[end]`，则在后半部分找，否则去前半部分找。

21. 数组中第K个最大元素：常规思路sort之后返回第k大个。还可以使用优先队列，维护一个size为K的最小堆。除此之外，还可以使用快排。参考：[Java中PriorityQueue详解](https://www.cnblogs.com/Elliott-Su-Faith-change-our-life/p/7472265.html)

22. BST中第K小的元素：使用中in order遍历

23. 二叉树的最大深度，递归

24. 二叉树中的最大路径和：递归+dp：`maxsum = Math.max(maxsum, leftGain + rightGain + node.val)`，需要注意的是，返回的是`node.val + Math.max(leftGain, rightGain)`

25. [公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/solution/236-er-cha-shu-de-zui-jin-gong-gong-zu-xian-hou-xu/)：参考链接思路，但是对于bst的最近公共祖先，可以利用其性质根据`(root.val - p.val) * (root.val - q.val)`大于0和小于0的情况来进行遍历。

26. 回溯算法专题参考：[什么叫回溯算法，一看就会，一写就废](https://mp.weixin.qq.com/s?__biz=MzU0ODMyNDk0Mw==&mid=2247488558&idx=1&sn=bb600c06c773960b3f4536c4c6c8d948&chksm=fb41870ecc360e18db1ca13783050d1a2efb19579407587baeea9b258a92e4c90c7ad12cbc1a&scene=21#wechat_redirect)【里面的子集问题也可以用dp来做】

27. 格雷编码：[镜像反射法](https://leetcode-cn.com/problems/gray-code/solution/gray-code-jing-xiang-fan-she-fa-by-jyd/), 设n阶格雷码集合为G(n)，那么G(n+1)的格雷码是G(n)与其倒序集合中每个元素二进制形式前面添加1的集合R'(n)的拼接。

28. 爬楼梯：经典dp问题，最好从i=3开始，因为题目已经说了n为正整数，所以不太好确定dp[0]的大小

29. 最大子序和：状态转移方程：`dp[i] = Math.max(dp[i-1] + nums[i], nums[i]);`

30. [买卖股票的最佳时机](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/solution/mian-shi-ti-63-gu-piao-de-zui-da-li-run-dong-tai-2/)

31. 不同路径：注意初始化边界为1，状态转移方程：`dp[i][j] = dp[i-1][j] + dp[i][j-1];`

32. [LRU缓存设计](https://leetcode-cn.com/problems/lru-cache/solution/lru-ce-lue-xiang-jie-he-shi-xian-by-labuladong/)：HashMap+DoubleList

33. 最小栈：使用两个栈，其中一个栈一直维护最小值，将其放在栈顶【注意使用ArrayDeque实现stack】

34. Nim游戏：当为4的倍数的时候，对手总有办法最后一个拿

## ByteDance Tag

### 挑战字符串

- 无重复字符的最长子串：使用滑动窗口，当遇到重复的元素的时候，将队列左边的元素移出即可
- 最长公共前缀：使用indexOf函数和substring函数来实现，取字符串数组中一个字符串来进行匹配。
- 字符串的排列：使用了回溯法，对每一位进行固定，并维护一个set对重复出现的字符进行剪枝。
- 字符串相乘：模拟手算乘法，使用stringbuilder，并且反着来进行算，记得对中间乘值进行补零操作。
- 翻转字符串里的单词：使用trim去除首位空格，然后使用split函数，对字符串进行分割，碰到空字符跳过，然后从后连接。
- 简化路径：同样使用split函数进行分割，使用stack，遇到`..`时需要pop（stack非空的话），遇到非空和非`.`的情况需要push入栈，最后从stack底部开始读取，使用join函数拼接起来。
- 复原IP地址：使用回溯，考虑的边界条件。

### 数组与排序

- 三数之和：使用双指针
- 岛屿最大面积：使用递归（注意边界条件），并且遍历后需将节点标记。
- 搜索选择排序数组，使用二分法，分条件进行判断。
- 最长连续递增序列
- 数组中的第k个最大元素：priorityqueue，维持一个只含有k个节点的最小堆
- 最长连续序列：使用hashset，或者排序法。
- 第k个排序：[数学方法](https://leetcode-cn.com/problems/permutation-sequence/solution/chao-guo-10000-pai-lie-zu-he-wu-di-gui-dai-ma-by-c/)，需要求出n-1的阶乘，选取数字
- 朋友圈，使用dfs或者bfs，优先dfs。补充：学习[union find](https://leetcode-cn.com/problems/friend-circles/solution/union-find-suan-fa-xiang-jie-by-labuladong/)
- [合并区间](https://leetcode-cn.com/problems/merge-intervals/solution/chi-jing-ran-yi-yan-miao-dong-by-sweetiee/), 先排序再比较区间起始位置
- 接雨水：双指针，如果不理解可以自己模拟一遍。还可以考虑[单调栈](https://leetcode-cn.com/problems/trapping-rain-water/solution/dan-diao-zhan-jie-jue-jie-yu-shui-wen-ti-by-sweeti/)做法

### 链表与树

- 合并两个有序链表：递归
- 反转链表：画图比较清晰
- 两数相加：dfs
- 排序链表：见上边Tencent tag
- 环形链表二：双指针，两次相遇，第一次相遇后fast指向head/
- 相交链表：见上边Tencent tag
- 合并k个升序链表：使用递归，利用合并两个list的函数，来进行合并
- 二叉树的最近公共祖先：使用递归，变形：BST的最近公共祖先比较大小
- 二叉树的锯齿形层次遍历：利用add()的性质，使用队列进行bfs

### 动态或贪心

- 股票问题见前面：维护一个profit和minprice
- [最大正方形](https://leetcode-cn.com/problems/maximal-square/solution/li-jie-san-zhe-qu-zui-xiao-1-by-lzhlyle/)`dp[i][j] = min(dp[i-1][j],dp[i-1][j-1],dp[i][j-1])+1`
- 最大子序和：`dp[i] = Math.max(dp[i-1] + nums[i], nums[i]); max = Math.max(dp[i], max);`
- 三角形最小序列和：由底向上. `dp[i][j] = Math.min(dp[i+1][j],dp[i+1][j]) + triangle.get(i).get(j); # dp[i][j]表示点(i,j)到底的最小路径`
- [俄罗斯套娃信封](https://leetcode-cn.com/problems/russian-doll-envelopes/solution/zui-chang-di-zeng-zi-xu-lie-kuo-zhan-dao-er-wei-er/)：二分法+LIS：**先对宽度 `w` 进行升序排序，如果遇到 `w` 相同的情况，则按照高度 `h` 降序排序。之后把所有的 `h` 作为一个数组，在这个数组上计算 LIS 的长度就是答案。**

### 数据结构

- 最小栈：维护两个stack，一个stack，一个min_stack。
- LRU缓存机制：DoubleLinkedList + HashMap
- [全O(1)的数据结构](https://leetcode-cn.com/problems/all-oone-data-structure/solution/java-mapshuang-lian-biao-by-gyz147/)：使用set

### 拓展练习

- x的平方根：二分法和牛顿
- UTF-8编码验证：主要是题意理解，然后进行位运算。
- 第二高的薪水：使用`IFNULL`函数、`LIMIT、OFFSET`关键字

## 剑指OFFER

- 面试题3: 数组中重复的数字：使用原地交换法。
- 面试题5：替换空格：遍历添加，遇到空格时替换称为%20加入结果string中
- 面试题6：从尾到头打印链表：使用回溯，到head==null的时候，new一个数组
- 面试题7：重建二叉树：使用回溯，先构建根节点，再通过前序遍历和终须遍历数组来构建左右子树。
- 面试题9：用两个栈来实现队列：画图解决
- 面试题10-1:斐波那契数列，注意：不要用递归，会内存爆炸。
- 面试题10-2:青蛙跳台阶：与前面类似，到那时起始值不一样。
- [面试题11](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/solution/mian-shi-ti-11-xuan-zhuan-shu-zu-de-zui-xiao-shu-3/)：选择数组的最小数字：使用二分法，注意考虑numbers[mid]与right比，而且还有`numbers[mid] == numbers[right]`的情况。
- 面试题12:使用dfs，有上下左右四个方向。
- 面试题13:机器人的运动范围：主要考虑`(i+1)%10 != 0? s1 + 1: s1 - 8`
- 面试题14-1：剪绳子：三种情况。
- 面试题14-2：使用快速幂求余
- 面试题15: 二进制中1的个数：两种方法，特殊的 `n&=(n-1)`
- 面试题16: 数值的整数次发：快速幂
- 面试题17: 打印从1到最大的n位数，递归，并且考虑0-9前0的处理。
- 面试题18： 删除链表的节点：遍历删除。
- 面试题19：正则匹配表达式，[解题思路](https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/solution/zhu-xing-xiang-xi-jiang-jie-you-qian-ru-shen-by-je/)
- 面试题20:  表示数字的字符串。

## 按照Tag

### String

- Day 1: 14/58/387/383/415/215
  - HashMap可以解决字符串次数方面的问题，例如找到字符串中的第一唯一字符
  - java中`trim()`方法：用于删除字符串的头尾空白符

