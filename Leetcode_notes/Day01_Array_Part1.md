# 704. 二分查找
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
## 27. 移除元素

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
- **重要性:** <font color="#ff0000">★★★★☆</font>
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
- **重要性:** <font color="#ff0000">★★★★☆</font>
## 思路:
- 注意,原地操作,故不能定义新数组.定义快慢指针,当出现非0元素时,则赋值给慢指针处. 最后多定义一个循环,将`slow+1-nums.leng-1`索引处的所有元素都赋0!
## 关键点:
- 
## 代码:
```

```
