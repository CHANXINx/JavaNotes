# 977.有序数组的平方
## 题目:
给你一个按 **非递减顺序** 排序的整数数组 `nums`，返回 **每个数字的平方** 组成的新数组，要求也按 **非递减顺序** 排序。
## 信息:
- **难度**:easy
- **重要性:** #★★★☆☆
## 思路:
### 思路一:
- 直接暴力解法,将数组元素平方后使用Arrays.sort()方法.
### 思路二:
- 数组为非递减顺序,即平方后的最大值一定在最左侧或最右侧!所以可以定义双指针i和j,判断两端元素平方后的大小,将较大的放在结果数组`res[nums.length]`中,同时存放到res数组后,需要移动key,保证将结果存放到res数组的空位.如此循环,直到指针i和j相遇.
## 关键点:
- 观察到数组为非递减序列,所以平方后的最大值一定在两侧,数组大小会呈现凹型(有负值时).
## 代码:
```java
class Solution {  
    public int[] sortedSquares(int[] nums) {  
//      1. 双指针.  
        int[] res = new int[nums.length];
        // 用于控制res数组的存放.  
        int key = nums.length - 1;  
        for (int i = 0, j = nums.length - 1; i <= j;) {  
        // 判断平方后较大的,存放到res中,并移动指针key和i/j.
            if ((nums[i] * nums[i]) < (nums[j] * nums[j])){  
                res[key] = nums[j] * nums[j];  
                j--;  
            }else {  
                res[key] = nums[i] * nums[i];  
                i++;  
            }  
            key--;  
        }  
        return res;  
    }  
}
```
### 优化:
- 可以定义left和right用于存放平方后的结果,避免重复计算,能稍稍提高效率.
```java
int leftSquare = nums[i] * nums[i];
int rightSquare = nums[j] * nums[j];
if (leftSquare < rightSquare) {
    res[key] = rightSquare;
    j--;
} else {
    res[key] = leftSquare;
    i++;
}
key--;

```
# 209.长度最小的子数组
## 题目:
给定一个含有 `n` 个正整数的数组和一个正整数 `target`.
找出该数组中满足其总和大于等于 `target` 的长度最小的**子数组** `[numsl, numsl+1, ..., numsr-1, numsr]` ,并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。
## 信息:
- **难度**:easy
- **重要性:** #★☆☆☆☆
## 思路:
### 思路一:
- 暴力解法,但是超时.
### 思路二:
- 对于子数组问题,可以采用**滑动窗口法**(可理解会双指针法变体): 数组是无序的,不过全是整数,所以当存在满足的条件的子数组时,逐渐缩小数组,能保证子数组的值会变小,此时就可以找到最小子数组.
- 定义指针i和j,用于括住一个窗口.固定i,往右移动j,当存在满足条件的窗口(子数组)时,往右移动i,使得窗口减小,据此找到符合条件的最小子数组!
## 关键点:
- 使用滑动窗口使得遍历次数减少,降低时间复杂度.
## 代码:
```java
class Solution {  
    public int minSubArrayLen(int target, int[] nums) {  
        // 用于存放窗口的总和.
        int sum = 0;  
        int i = 0;  
        // 用于存放结果:子数组长度
        int res = nums.length+1;
        // 存放当前子数组长度.  
        int subL = 0;  
        for (int j = 0; j < nums.length; j++) { 
        // 窗口总和.
            sum += nums[j];  
            while (sum >= target) {  
			// 满足条件,取当前窗口长度.
                subL = j - i + 1;  
			// 若当前子数组长度和目前最小子数组长度比较
                res = res < subL ? res : subL;  
           //  左指针向右移动, 缩小窗口.  
                sum -= nums[i];  
                i++;  
            }  
        }  
        // 若res的值不为nums.length+1,代表修改过res,即存在最小子数组;否则返回0.
        return res == nums.length+1? 0 : res;  
    }  
}
```
### 优化:
- `int res = Integer.MAX_VALUE;`:使用Integer.MAX_VALUE,使程序可读性更高.
- `res = Math.min(res,j-i+1)`:取最小子数组,可读性更高,同时效率好像也更高!
# 59.螺旋矩阵II
## 题目:
- 给你一个正整数 `n` ，生成一个包含 `1` 到 `n2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 。
- ![[ArrayPart2_img1.png]]
## 信息:
- **难度**: Medium
- **重要性:** #★★★★★
## 思路:
>这道题目可以说在面试中出现频率较高的题目，**本题并不涉及到什么算法，就是模拟过程，但却十分考察对代码的掌控能力。**
- 确认每次画一条线,然后明确边界条件.建议以4×4矩阵为例子.
- 循环不变量:遵循一个规则来处理每一条边.
## 关键点:

## 代码:
```java
class Solution {  
    public int[][] generateMatrix(int n) {  
        int[][] matrix = new int[n][n];  
        int start = 0;  
        int end = n-1;  
        int count = 1;  
        while (start <= end) {  
            for (int i = start; i < end; i++) {  
                matrix[start][i] = count;  
                count++;  
            }  
            for (int i = start; i < end; i++) {  
                matrix[i][end] = count;  
                count++;  
            }  
            for (int i = end; i > start; i--){  
                matrix[end][i] = count;  
                count++;  
            }  
            for (int i = end; i > start; i--){  
                matrix[i][start] = count;  
                count++;  
            }  
            if (start == end)  
                matrix[start][end] = count;  
            start++;  
            end--;  
        }  
        return matrix;  
    }  
}  
```
