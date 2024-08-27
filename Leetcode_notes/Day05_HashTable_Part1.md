# 242. 有效的字母异位词
## 题目:
- 给定两个字符串 `_s_` 和 `_t_` ，编写一个函数来判断 `_t_` 是否是 `_s_` 的字母异位词。
- **字母异位词** 是通过重新排列不同单词或短语的字母而形成的单词或短语，通常只使用所有原始字母一次。
## 信息:
- **难度**:Easy
- **重要性:** #★★★☆☆
## 思路:
- 对题目进行简化:其实就是统计s中出现的次数及其次数,要求t中的字符均在s中使用过,并且使用次数要一致.
- 利用集合/数组存储字符串`s`的**出现的字符**及其**出现次数**,然后遍历字符`t`,判断t中的字符**是否均在存储s中的字符数组中**以及**t中的字符出现次数是否超出****.
- 因为小写字母一共只有26个,所以定义一个长度为26的数组一定满足条件!
## 关键点:
- 定义**长度为26**的数组存储**出现的字符及其使用次数.**
- 
## 代码:
```java
class Solution {  
    public boolean isAnagram(String s, String t) {  
        int[] record = new int[26];  
        if (s.length() != t.length())  
            return false;  
        for (int i = 0; i < s.length(); i++){  
            record[s.charAt(i) - 'a']++;  
        }  
        for (int j = 0; j < t.length(); j++){  
            record[t.charAt(j) - 'a']--;  
            if (record[t.charAt(j) - 'a'] < 0){  
                return false;  
            }  
        }  
        return true;  
    }  
}
```
# 349. 两个数组的交集
## 题目:
- 给定两个数组 `nums1` 和 `nums2`,返回它们的交集。输出结果中的每个元素一定是**唯一**的。我们可以**不考虑输出结果的顺序** 。
## 信息:
- **难度**:
- **重要性:** #★☆☆☆☆
## 思路:
### 思路一:
- 利用Map存储nums1中的所有元素,其中key为nums1中的值,value任意(作为flag,之后在判断交集时使用).遍历nums2,判断nums2中的元素是否在Map出现过,若出现过,则将value置0,并且利用slow指针将该元素放置在nums2中.(slow指针肯定<=遍历nums2的指针)
### 思路二:
- 利用set存储元素,set的特点是不会存储相同的元素,并且可以利用contains方法判断元素是否在set集合中.
## 关键点:
- 利用set集合不存储相同元素的特点.
## 代码:
### 解法一:
- 利用Map来求解,可以但不是最优解.
```java
class Solution {  
    public int[] intersection(int[] nums1, int[] nums2) {  
        HashMap<Integer,Integer> map1 = new HashMap<>();  
        for (int i = 0; i < nums1.length; i++){  
            map1.put(nums1[i],1);  
        }  
        int slow = 0;  
        for (int j = 0; j < nums2.length; j++){  
            if (map1.get(nums2[j])== null){  
                continue;  
            }  
            if (map1.get(nums2[j]) != 0) {  
                nums2[slow] = nums2[j];  
                slow++;  
                map1.remove(nums2[j]);  
            }  
        }  
        if (slow != 0) {  
            nums1 = Arrays.copyOfRange(nums2,0,slow);  
            return nums1;  
        }else  
            return new int[0];  
    }  
}
```

# 202. 快乐数
## 题目:
- 编写一个算法来判断一个数 `n` 是不是快乐数。
**「快乐数」** 定义为：
- 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
- 然后重复这个过程直到这个数变为 1，也可能是 **无限循环** 但始终变不到 1。
- 如果这个过程 **结果为** 1，那么这个数就是快乐数。
如果 `n` 是快乐数就返回 `true` ；不是，则返回 `false` 。
## 信息:
- **难度**: easy
- **重要性:** #★★★★☆
## 思路:
>难点在于 1)如何取出个,十,百位的数字; 2)在不是快乐数时,如何跳出循环.
- 1)取出各位数,可以定义函数(通用),i存储余数(i=n%10),并更新n的值(n=n/10),由于n是int类型,所以当**n<10时,n/10的结果为0**,所以可以利用这点作为循环条件,代码如下:
```java
	while (n != 0){  
//            保存余数  
		i = n % 10;  
//            利用集合/数组存储余数.
		map.put(count,i);  
		count++;  
//            保存整除结果  
		n = n / 10;  
	}  
```
- 2)跳出快乐数的循环,这里就与代码无关了,而是快乐数的定义.非快乐数,最终一定会出现相同的值,而快乐数最终结果会为1.但是如何判断是否重复,这也是个重点!利用HashSet<>不存储重复元素的特点以及contains方法实现.
## 关键点:
- 利用非快乐数的"平方和"的结果会重复出现作为跳出无限循环的条件.
## 代码:
### 我的代码:
```java
class Solution {  
    public boolean isHappy(int n) {  
//    marked as function.  
        int res = n;  
        HashSet<Integer> set = new HashSet<>();  
        while (true) {  
            res = getResult(res);  
            if (res == 1) {  
                return true;  
            }  
            if (set.contains(res)) {  
                return false;  
            }  
            set.add(res);  
        }  
    }  
    public int getResult(int n){  
        HashMap<Integer,Integer> map = new HashMap<>();  
        int res = 0;  
        int i = 1;  
        int j = 1;  
        int count = 0;  
        //        取出个,十,百...  
        while (n != 0){  
//            保存余数  
            i = n % 10;  
            map.put(count,i);  
            count++;  
//            保存整除结果  
            n = n / 10;  
        }  
        for (int m = 0; m < count; m++){  
            res += (map.get(m) * map.get(m)) ;  
        }  
        return res;  
    }  
}
```

### 官方解答
```java
class Solution {
    public boolean isHappy(int n) {
        Set<Integer> record = new HashSet<>();
        while (n != 1 && !record.contains(n)) {
            record.add(n);
            n = getNextNumber(n);
        }
        return n == 1;
    }

    private int getNextNumber(int n) {
        int res = 0;
        while (n > 0) {
            int temp = n % 10;
            res += temp * temp;
            n = n / 10;
        }
        return res;
    }
}
```

# 1. 两数之和
## 题目:
- 给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** _`target`_ 的那 **两个** 整数，并返回它们的数组下标。你可以假设每种输入只会对应一个答案，并且你不能使用两次相同的元素。你可以按任意顺序返回答案。
## 信息:
- **难度**: easy
- **重要性:** #★☆☆☆☆
## 思路:
### 思路1:
- 这题最简单的方法就是利用两个for循环实现!
### 思路2:
- 
## 关键点:
- 
## 代码:
```java
class Solution {  
    public int[] twoSum(int[] nums, int target) {  
//        解法1: 使用数组遍历  
        int sum = 0;  
        for (int i = 0; i < nums.length - 1; i++)  
            for (int j = i+1; j < nums.length; j++){  
                sum = nums[i] + nums[j];  
                if (sum == target)  
                    return new int[]{i,j};  
            }  
        return null;  
```
