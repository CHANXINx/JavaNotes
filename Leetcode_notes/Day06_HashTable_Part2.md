# 454. 四数相加II

## 题目:
- 给你四个整数数组 `nums1`、`nums2`、`nums3` 和 `nums4` ，数组长度都是 `n` ，请你计算有多少个元组 `(i, j, k, l)` 能满足：
	- `0 <= i, j, k, l < n`
	- `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`
## 信息:
- **难度**: medium
- **重要性:** #★★★☆☆
## 思路:
**别害怕遍历所有数组!**
- 首先是有四个数组,需要统计一共多少个满足条件的元组(即无需返回数组索引).利用Map的特性,将nums1和nums2的两数和作为key,将出现次数作为value.之后再遍历nums3和nums4,得到其和c+d,并利用containsKey判断满足`a+b=c+d`的key值是否存在.
## 关键点:
- 利用Map的key-value特性保存两数和及其出现次数,其实`int[]`也可以实现这个,但是数组需要先定义长度,这里的数组长度是不确定的,所以无法使用,故选用集合Map来实现!
- Map有两个方法很常用:
	- `containsKey(Object key)`:用于判断是否存在该key的映射;其实用`get(Object) == null`也可以替代,但是containsKey更高效,也更直观.
	- `getOrDefault(Object Key,V defaultValue)`:返回指定key映射到的值，如果此map不包含键的映射，则返回传入的`defaultValue`. 这个操作用来替代`nums[i]++;`:因为在Map中,若key对应的映射不存在时,无法进行自增操作;而利用`getOrDefault`可以在映射不存在时,赋值为0再进行自增.
## 代码:
```java
class Solution {  
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {  
        int count = 0;  
        HashMap<Integer, Integer> map1 = new HashMap<>();  
        for (int i : nums1) {  
            for (int j : nums2) {  
                map1.put(i+j,map1.getOrDefault(i+j, 0) + 1);  
            }  
        }  
//        ArrayList<Integer> sum2 = new ArrayList<>();  
        for(int i :nums3) {  
            for (int j : nums4) {  
//                a+b+c+d=0  
//                sum1存储了a+b,判断是0-(c+d)的值是否存在  
                if (map1.containsKey(-(i+j))) {  
                    count += map1.get(-(i+j));  
                }  
            }  
        }  
        return count;  
    }  
}
```

# 383. 赎金信
## 题目:
- 给你两个字符串：`ransomNote` 和 `magazine` ，判断 `ransomNote` 能不能由 `magazine` 里面的字符构成。如果可以，返回 `true` ；否则返回 `false` 。`magazine` 中的每个字符只能在 `ransomNote` 中使用一次。
## 信息:
- **难度**: easy
- **重要性:** #★★☆☆☆
## 思路:
- 利用数组作为哈希表,其中**索引**为字母的ASCII码,UNICODE码或者直接利用与'a'相减得到相对位置,即'a'的索引为0,'b'的索引为1...,**值**为出现次数.
## 关键点:
- 利用数组作为哈希表,思路更清晰,内存空间占用更少!
## 代码:
```java
class Solution {  
    public boolean canConstruct(String ransomNote, String magazine) {  
        if (ransomNote.length() > magazine.length())  
            return false;  
        int[] nums = new int[26];  
        for (int i = 0; i < magazine.length(); i++){  
            nums[magazine.charAt(i) - 'a']++;  
        }  
        for (int i = 0; i < ransomNote.length(); i++) {  
            if (nums[ransomNote.charAt(i) - 'a'] != 0) {  
                    nums[ransomNote.charAt(i) - 'a']--;  
            }else {  
                return false;  
            }  
        }  
        return true;  
    }  
}

```
# 15. 三数之和 
## 题目:
- 给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请你返回所有和为 `0` 且不重复的三元组。
**注意：**答案中不可以包含重复的三元组。
## 信息:
- **难度**: medium
- **重要性:** #★★★★★
## 思路:
- 将数组进行**排序**,利用双指针法!之后就是定义三个指针:i,j和k.i和j由左向右移动,k由右向左移动,移动逻辑如下:
	- 遍历i: `for(int i=0;i<nums.length-2;i++)`,判断`sum = nums[i]+nums[j]+nums[k]`: 1)`>0`,则此时k需要向左移动; 2)`<0`,则此时j需要向右移动(即增大sum); 3)`==0`,满足条件,将sum加入list中,并且j++,k--(`nums[j]`增大和`nums[k]`减小),并且需要进行去重操作:`nums[k/j] == nums[k-1/j+1]`,则多执行一次`k--/j++`.
## 关键点:
- 利用`Arrays.sort(nums)`将数组进行排序!
- **双指针法:对sum的值进行判断,然后决定移动哪个指针;**
- 去重操作,若`nums[i] == nums[i+1]`,则需额外移动一次指针.
## 代码:
```java
class Solution {  
    public List<List<Integer>> threeSum(int[] nums) {  
        List<List<Integer>> list = new ArrayList<>();  
        Arrays.sort(nums);  
//      若首个元素都为空,则直接返回!  
        if (nums[0] > 0 || nums.length < 3) return list;  
        int j = 0;  
        int k = nums.length - 1;  
        int sum = 0;  
        for (int i = 0; i < nums.length - 2; i++) {  
            j = i + 1;  
            k = nums.length - 1;  
  
            if (i > 0 && nums[i] == nums[i - 1]) {  // 去重a  
                continue;  
            }  
            while (j < k) {  
                sum = nums[i] + nums[j] + nums[k];  
                if (sum < 0) j++;  
                else if (sum > 0) k--;  
                else {  
                    list.add(Arrays.asList(nums[i], nums[j], nums[k]));  
                    while (k > j && nums[k] == nums[k - 1]) k--;  
                    while (k > j && nums[j] == nums[j + 1]) j++;  
                    j++;  
                    k--;  
                }  
            }  
        }  
        return list;  
    }  
}
```

# 18. 四数之和

## 题目:
给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：
- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` **互不相同**
- `nums[a] + nums[b] + nums[c] + nums[d] == target`
你可以按 **任意顺序** 返回答案 。
## 信息:
- **难度**: medium
- **重要性:** #★★★★★
## 思路:
- 这题其实是和三数之和一种类型的题目,都是利用多指针法.其中i,j,m三个指针在左,k指针在右.先判断sum值:1)若大于target,则移动指针k; 2)若小于target,则移动指针m; 3)若等于,则`list.add`,并同时移动m和k.当`m>=k`时,此时就需要更新一次j,重复上述步骤;若遍历完所有j,则需要更新i了.即i是最外层循环,j是内层循环,而m和k是内内层循环.
- 去重操作,对于i的去重,`if (i > 0 && nums[i] == nums[i - 1]);`,总是迷惑是用`nums[i]==nums[i-1]`还是`nums[i]==nums[i+1]`,这样理解:**i是向右移动的**,所以先对i进行遍历,结束后i++,此时的`nums[i]和nums[i-1]`有可能重复,所以判断条件是`nums[i]==nums[i-1]`,若重复了,则跳过当前的i!
- 同理,对于k的去重,k是向左移动的,先使用当前的k,当k--后,判断`nums[k]==nums[k+1]`,若重复,则跳过当前k(即执行k--).
## 关键点:
- 多指针法!特别是指针k需要放在最右边!然后就是根据sum值的不同移动指针.
- **去重操作**是重点,要注意不同的条件,指针往右移动时,是使用`num[i]==nums[i-1]`;向左移动时,是使用`num[i]==nums[i+1]`,即判断当前值和前面使用过的值是否相等,相等则跳过!
- **剪枝操作**,即当满足一定条件时直接跳出循环,不执行多余的操作:
	- i循环的剪枝,当满足`nums[i]>0且nums[i]>target`时,此时往后的均是`大于0且大于nums[i]`的数,只会越来越大于target,故可直接返回list.
	- j循环的剪枝,当`nums[i]+nums[j] > 0 && nums[i]+nums[j] > target`时,
```
i循环的剪枝:

// nums[i] > target 直接返回, 剪枝操作
if (nums[i] > 0 && nums[i] > target) {
	return result;
}

j循环的剪枝:
//
if (nums[i]+nums[j] > 0 && nums[i]+nums[j] > target) {
	break;
}
```
## 代码:
```java
class Solution {  
    public List<List<Integer>> fourSum(int[] nums, int target) {  
        List<List<Integer>> list = new ArrayList<>();  
        Arrays.sort(nums);  
  
        if (nums.length < 4) return list;  
  
        int m = 0;  
        int k = nums.length - 1;  
//        int sum = 0;  
        for (int i = 0; i < nums.length - 3; i++) {  
            // nums[i] > target 直接返回, 剪枝操作  
            if (nums[i] > 0 && nums[i] > target) {  
                return list;  
            }  
            if (i > 0 && nums[i] == nums[i - 1]) {  // 去重a  
                continue;  
            }  
            for (int j = i + 1; j < nums.length - 2; j++){  
                if (nums[i]+nums[j] > 0 && nums[i]+nums[j] > target) {  
                    break;  
                }  
                if (j > i + 1 && nums[j] == nums[j - 1]) continue; // 去重b  
                m = j + 1;  
                k = nums.length - 1;  
  
               while (m < k) {  
                   long sum =(long) nums[i] + nums[j] + nums[m] + nums[k];  
                   if (sum > target) k--;  
                   else if (sum < target) m++;  
                   else {  
                       list.add(Arrays.asList(nums[i], nums[j],nums[m],nums[k]));  
                       while (m < k && nums[k] == nums[k-1]) k--;  
                       while (m < k && nums[m] == nums[m+1]) m++;  
                       m++;  
                       k--;  
                   }  
               }  
           }  
        }  
        return list;  
    }  
}  
```
