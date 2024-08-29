# 344. 反转字符串

## 题目:
编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 `s` 的形式给出。

不要给另外的数组分配额外的空间，你必须**原地修改输入数组**、使用 O(1) 的额外空间解决这一问题。
## 信息:
- **难度**: easy
- **重要性:** #★☆☆☆☆
## 思路:
- 双指针交换数组元素,利用中间变量temp保存交换元素.
## 关键点:
- 
## 代码:
```java
class Solution {  
    public void reverseString(char[] s) {  
        int left = 0;  
        int right = s.length - 1;  
        char temp;  
        while (left < right){  
            temp = s[right];  
            s[right] = s[left];  
            s[left] = temp;  
            left++;  
            right--;  
        }  
    }  
}
```

# 541. 反转字符串II
## 题目:
给定一个字符串 `s` 和一个整数 `k`，从字符串开头算起，每计数至 `2k` 个字符，就反转这 `2k` 字符中的前 `k` 个字符。

- 如果剩余字符少于 `k` 个，则将剩余字符全部反转。
- 如果剩余字符小于 `2k` 但大于或等于 `k` 个，则反转前 `k` 个字符，其余字符保持原样。
## 信息:
- **难度**:
- **重要性:** #★★★☆☆
## 思路:
- **理解题目:** 每2k个,反转前k个;若当出现剩下的字符长度<k,则全部反转;若剩下的字符>=k,但<2k,则依旧反转前k个.
- 定义index变量,用于判断剩余字符串属于上述哪种类型,不同类型执行不同的反转操作.最终代码如下.每次执行反转操作后,都需要更新index!
## 关键点:
- 
## 代码:
### 版本一:(冗余度高)
```java
class Solution {  
    public String reverseStr(String s, int k) {  
        int index = 0;  
        char temp;  
        char[] s1 = s.toCharArray();  
        while ((s.length()-index) > 0){  
            if ((s.length() - index) > 2*k) {  
                for (int i = index, j = index+k-1; i < j; i++,j--){  
                    temp = s1[j];  
                    s1[j] = s1[i];  
                    s1[i] = temp;  
                }  
            }else if ((s.length() - index > k)){  
                for (int i = index, j = index+k-1; i < j;i++,j--){  
                    temp = s1[j];  
                    s1[j] = s1[i];  
                    s1[i] = temp;  
                }  
            }else {  
                for (int i = index, j = s.length()-1; i < j;i++,j--){  
                    temp = s1[j];  
                    s1[j] = s1[i];  
                    s1[i] = temp;  
                }  
            }  
            index = index + 2*k;  
        }  
        return String.valueOf(s1);  
    }  
}  
```
### 版本二:(优化后)
#### 优化思路:
1. 首先,上面的if和else-if是一样的逻辑,所以首先合并了;
2. if和else的逻辑交换逻辑也是一样的,只是右指针不同,所以这里可以利用`Math.min()`函数来判断j取`index+k-1`还是`s.length()-1`,即**若剩余字符小于k时,此时`s.length()-1`肯定小于`index+k-1`**,故取min值是正确且合理的.
```java
class Solution {  
    public String reverseStr(String s, int k) {  
        int index = 0;  
        char[] s1 = s.toCharArray();  
        while ((s.length()-index) > 0){  
            int i = index;  
            int j = Math.min(index + k - 1, s.length() - 1);  
            while(i<j){  
                char temp = s1[j];  
                s1[j] = s1[i];  
                s1[i] = temp;  
                i++;
                j--;
            }  
            index = index + 2*k;  
        }  
        return String.valueOf(s1);  
    }  
} 
```

# 54. 替换数字（卡码网）
## 题目:
给定一个字符串 s，它包含小写字母和数字字符，请编写一个函数，将字符串中的字母字符保持不变，而将每个数字字符替换为number。 例如，对于输入字符串 "a1b2c3"，函数应该将其转换为 "anumberbnumbercnumber"。
###### 输入描述
输入一个字符串 s,s 仅包含小写字母和数字字符。
###### 输出描述
打印一个新的字符串，其中每个数字字符都被替换为了number
## 信息:
- **难度**: null
- **重要性:** #★★★☆☆
## 思路:
- 由于String的不可变性,所以此处应该使用StringBuilder来输出结果.
- 利用`Character.isDigit(char c)`来判断当前字符是否为数字,若为数字,则`res.append("number")`;若非数字,则`res.append("currChar")`
- **在不需要获取索引时,可以使用增强for循环!**
## 关键点:
- 1)利用好StringBuilder的可变性; 2)利用append函数在可变字符串尾部添加字符(串); 3)利用`Character.isDigit()`判断是否为数字.
## 代码:
```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
		// 获取输入.
        Scanner scanner = new Scanner(System.in);
        String s = scanner.next();
        // 操作逻辑.
        StringBuilder result = new StringBuilder();
        for(char c : s.toCharArray()){
            if (Character.isDigit(c)){
                result.append("number");
            }else{
                result.append(c);
            } 
        }
        //输出结果.
        System.out.println(result.toString());
    }
}

```
