# LeetCode 2nd Round For Reveiwing and Improving

## Plan

- [腾讯tag](https://leetcode-cn.com/leetbook/read/tencent/xxqfy5/)
- [字节tag](https://leetcode-cn.com/explore/interview/card/bytedance/?utm_campaign=bytedance_2020fall_explore&utm_medium=leetcode_leetbook_banner&utm_source=explore&gio_link_id=nP2lpDV9)
- [剑指offer](https://leetcode-cn.com/problemset/lcof/)
- [程序员面试金典](https://leetcode-cn.com/problemset/lcci/)
- reviewing problems solved in the 1st round
- PS: think and do it, but think first.

## 腾讯tag
  
  1. 寻找两个正序数组的中位-hard
    
    [参考思路：解法三](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/): 求第k小的数。
  
  2. 最长回文子串-medium 
    
    中心扩散法和动态规划：`dp[l][r]`表示字符串从l到r这段是否为回文。初始状态，`l=r` 时，此时 `dp[l][r]=true`。状态转移方程，`dp[l][r]=true` 并且`(l-1)`和`(r+1)`两个位置为相同的字符，此时`dp[l-1][r+1]=true`。

  3. atoi-medium：注意边界条件的处理，对正负数分情况处理，正数：当转换之后的值比INT_MAX大的时候，返回INT_MAX。负数：先不加符号，此时转化之后的值比INT_MAX大时，返回INT_MIN.



## String

- Day 1: 14/58/387/383/415
  - HashMap可以解决字符串次数方面的问题，例如找到字符串中的第一唯一字符
  - java中`trim()`方法：用于删除字符串的头尾空白符