# 704. 二分查找
## 信息:
- **难度**:easy
- **重要性:** #★☆☆☆☆ 
## 思路: 
- **定义`head`和`end`节点**, 即二分中的头和尾. **将`index`定义为`(head+end/2)`;
- 此时即判断`nums[index]`与`target`的关系, 若小于`target`, 则说明在二分中的右半部分, 此时将head置为`index+1`. 此时继续在右半部分重复上述判断, 直到找到`target`退出循环或数组中没有等于`target`的值而退出.
## 代码:
```
class Solution {  
    public int search(int[] nums, int target) {  
        int head = 0;  
        int end = nums.length - 1;  
        while (head <= end){  
            int index = (head + end) / 2;  
            if (nums[index] == target)  
                return index;  
            if (nums[index] < target){  
                head = index + 1;  
            }else if (nums[index] > target)  
                end = index - 1;  
        }  
        return -1;  
    }  
}
```
# 27. 移除元素
## 信息:
- **难度**:easy
- **重要性:** #★★★☆☆ 
## 思路:
- 因为是原地移除, 所以双指针方法进行解答: 一个快指针,一个慢指针.**快指针用来遍历数组中的所有元素, 慢指针用来存放数组中不等于target的元素(即需要存放的元素).** 用双指针进行求解,能在原数组上进行变化,而无需多定义新数组.
## 关键点:
- 原地移除,所以不能定义新数组,要在原数组上保存,故用双指针很合适.
## 代码:
```
class Solution {  
    public int removeElement(int[] nums, int val) {  
        int slow = 0;  
        int fast = 0;  
        while (fast < nums.length){  
            if (nums[fast] != val){  
                nums[slow] = nums[fast];  
                slow++;  
            }  
            fast++;  
        }  
        return slow;  
    }  
}
```
# 26. 删除有序数组中的重复项
## 信息:
- **难度**:easy
- **重要性:** #★★★★☆
## 思路:
- 非严格递增数组,原地删除. 这里要注意: 非严格递增数组中的重复项一定是连续出现的!并且原地删除, 所以不能定义新数组.综上,定义快慢指针,当出现重复元素时,去除重复项,即`nums[slow++] = nums[fast]` .
## 关键点:
- 去除重复元素,指的是仅保留单个,而不是全部去除,所以这里应该是`nums[++slow] = nums[fast];`→ 先自增,再赋值!
## 代码:
```
class Solution {  
    public int removeDuplicates(int[] nums) {  
        int slow = 0;  
        int fast = 1;  
        while (fast < nums.length) {  
            if (nums[slow] != nums[fast]) {  
                nums[++slow] = nums[fast];  
            }  
            fast++;  
        }  
        return slow + 1;  
    }  
}
```

# 283. 移动零
## 题目:
- 给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。请注意, 必须在不复制数组的情况下原地对数组进行操作。
## 信息:
- **难度**: easy
- **重要性:** #★★☆☆☆
## 思路:
- 注意,原地操作,故不能定义新数组.定义快慢指针,当出现非0元素时,则赋值给慢指针处. 最后多定义一个循环,将`slow+1-nums.leng-1`索引处的所有元素都赋0!
## 关键点:
- 较简单,略.
## 代码:
```
class Solution {  
    public void moveZeroes(int[] nums) {  
        int slow = 0,fast = 0;  
  
        while (fast < nums.length){  
            if (nums[fast] != 0){  
                nums[slow] = nums[fast];  
                slow++;  
            }  
            fast++;  
        }  
        for (int i = slow;i < nums.length;i++)  
            nums[i] = 0;  
    }  
}
```
# 844. 比较含退格的字符串
## 题目:
- 给定 `s` 和 `t` 两个字符串，当它们分别被输入到空白的文本编辑器后，如果两者相等，返回 `true` 。`#` 代表退格字符。注意：如果对空文本输入退格字符，文本继续为空。
## 信息:
- **难度**: easy
- **重要性:** #★★★★★
## 思路:
- 难度较高.这题的重点在于利用`skipS`和`skipT`存放`#`的个数!然后利用指针从右往左遍历字符串:当出现`#`,则`skip++`并且指针左移;当出现非`#`,且`skip==0`,则将当前字符存放到新数组中;当出现非`#`,且`skip>0`,则指针左移.
- 这里对于我的思路,有个优化的方法,即不需要使用新数组进行额外的存放,而是在遍历每个元素时同时进行判断,这样节省了空间! 同时,利用charAt(index)来获取字符串的索引元素,而不是利用`toCharArray()`
## 关键点:
- 利用`skip`变量存放`#`的个数.
- 各种情况的判断!!!这是我觉得复杂的点.
- 避免使用`i = i - (skipS); skipS = 0;`!这样会导致在出现`a#j##`这种字符串的时候出错,因为会有一个#被忽略!
## 代码:
```
class Solution {  
    public boolean backspaceCompare(String s, String t) {  
  
        int i = s.length() - 1;  
        int j = t.length() - 1;  
        int skipS = 0, skipT = 0;  
        while (i >= 0 || j >= 0){  
            while (i >= 0) {  
                if (s.charAt(i) == '#'){  
                    i--;  
                    skipS++;  
                }else if (s.charAt(i) != '#' && skipS != 0){  
                    skipS--;  
                    i--;  
                }else {  
                    break;  
                }  
            }  
            while (j >= 0) {  
                if (t.charAt(j) == '#'){  
                    j--;  
                    skipT++;  
                }else if (t.charAt(j) != '#' && skipT != 0){  
                    skipT--;  
                    j--;  
                }else{  
                    break;  
                }  
            }  
            // Ensure both indices are valid before comparison  
            if (i >= 0 && j >= 0 && s.charAt(i) != t.charAt(j)) {  
                return false;  
            }  
  
            // If only one string is exhausted, they are not equal  
            if ((i >= 0) != (j >= 0)) {  
                return false;  
            }  
            i--;  
            j--;  
        }  
        return true;  
    }
}
```
# 977. 有序数组的平方
## 题目:
- 给你一个按 **非递减顺序** 排序的整数数组 `nums`，返回 **每个数字的平方** 组成的新数组，要求也按 **非递减顺序** 排序。
## 信息:
- **难度**:easy
- **重要性:** ★★★★☆
## 思路:
- 直接使用暴力解法,固然可行,但是时间复杂度较高,无法通过力扣测试!
- 给定的是非递减数组,即已经排好序,但是小于0的数字平方后数值较大,所以利用双指针进行判断,同时**定义新数组`res[key]`存放平方后的排序**:左指针指向head,右指针指向end, 比较`nums[head]`和`nums[end]`的平方后的大小,谁大谁存到数组里,并且移动key与head/end的指针!当`head>end`时,停止判断.
## 关键点:
- 利用左右指针比较平方后的元素大小,并存放到新数组里,更新指针.
## 代码:
```
class Solution {  
    public int[] sortedSquares(int[] nums) {  
//      1. 双指针.  
        int[] res = new int[nums.length];  
        int key = nums.length - 1;  
        for (int i = 0, j = nums.length - 1; i <= j;) {  
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
