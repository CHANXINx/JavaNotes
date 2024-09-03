# 151. 反转字符串中的单词
## 题目:
给你一个字符串 `s` ，请你反转字符串中 **单词** 的顺序。

**单词** 是由非空格字符组成的字符串。`s` 中使用至少一个空格将字符串中的 **单词** 分隔开。

返回 **单词** 顺序颠倒且 **单词** 之间用单个空格连接的结果字符串。

**注意：** 输入字符串 `s`中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。
## 信息:
- **难度**: medium
- **重要性:** #★★★★☆
## 思路:
### 思路一:
- 最简单的想法,利用java的String库函数,例如trim,split函数.这样使得这题过于简单,无意义.
### 思路二:
- 定义双指针,碰到非空格,就定住慢指针,快指针寻找空格,找到了,就取之间的单词,使用append函数添加到sb中.同时,为了保证能找到开头的单词,额外添加了" ", 这样就能保证快指针一定能寻找到开头的空格了.
### 思路三:
- 
## 关键点:
- 
## 代码:
```java
class Solution {  
    public String reverseWords(String s) {  
        StringBuilder sb = new StringBuilder();  
        // 保证能取到开头的单词!
        s = " ".concat(s);  
        char[] c1 = s.toCharArray();  
  
        int fast = 0;  
  
        for (int i = c1.length - 1; i >= 0; i--){  
            if (c1[i] != ' ') {  
                for (int j = i; j >= 0; j--) {  
                    if (c1[j-1] == ' ') {  
                    // 取非' '和' '之间的单词. 
                        sb.append(s.substring(j, i + 1)+' ');  
                        i = j;  
                        break;  
                    }  
                }  
            }  
        }  
        return sb.substring(0,sb.length()-1);  
    }  
}
```
# 55. 右旋字符串
## 题目:
字符串的右旋转操作是把字符串尾部的若干个字符转移到字符串的前面。给定一个字符串 s 和一个正整数 k，请编写一个函数，将字符串中的后面 k 个字符移到字符串的前面，实现字符串的右旋转操作。 

例如，对于输入字符串 "abcdefg" 和整数 2，函数应该将其转换为 "fgabcde"。
## 信息:
- **难度**: null
- **重要性:** #★★☆☆☆
## 思路:
- 利用subString获得后k位和前k位,再利用+来拼接字符串.
## 关键点:
- 
## 代码:
```java
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int k = scanner.nextInt();
        String s = scanner.next();
        
        int sLen = s.length();
        String sub1 = s.substring(sLen-k,sLen);
        String sub2 = s.substring(0,sLen-k);
        System.out.println(sub1+sub2);
    }
}
```
# 28. 找出字符串中第一个匹配项的下标
## 题目:
给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 0 开始）。如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。
## 信息:
- **难度**: easy
- **重要性:** #★★★☆☆
## 思路:
### 思路一:
- 暴力解法,判断所有与needle长度相同的字符串是否与needle匹配,是则返回下标.
### 思路二:
- 思路类似,不过通过增加限制来减少匹配次数,即降低了时间复杂度. 1)若needle长度>haystack长度,不可能匹配,直接返回; 2)只有当haystack中的某个字母与needle的首字母相同时,才开始匹配操作; 3)循环i的判断不用到`<haystack.length()`,小于`haystack.length()-needle.length()`即可.易理解,在这之后的匹配并无意义,因为剩余字符串的长度小于needle的长度.
### 思路三:
- KMP算法?
## 关键点:
- 多添加进行循环匹配的限制条件!
## 代码:
```java
class Solution {  
    public int strStr(String haystack, String needle) {  
        int hLen = haystack.length();  
        int nLen = needle.length();  
        // needle字符串更长,不可能匹配,直接返回!
        if (nLen > hLen) return -1;  
        for (int i = 0; i <= hLen-nLen;i++){ 
        // 首字母相同才进行匹配! 
            if (haystack.charAt(i) == needle.charAt(0)) {  
                    int end = nLen;  
                    String str = haystack.substring(i,end+i);  
                    if (str.equals(needle))  
                        return i;  
            }  
        }  
        return -1;  
    }  
}
```
### 优化:
- 使用滑动窗口优化subString.当`charAt(i+j)==charAt(j)`,即代表当前首字母相同,开始滑动,即j++.此时窗口逐步增大,即while中逐个判断字符是否相等,当`j==nLen`,代表窗口长度等于needle字符串长度,即此时二者匹配!
```java
for (int i = 0; i <= hLen - nLen; i++) { 
	// 检查 haystack 从 i 开始的子串是否与 needle 相等
	int j = 0; 
	while (j < nLen && haystack.charAt(i + j) == needle.charAt(j)) {
		j++; 
	} 
	if (j == nLen) return i; // 找到匹配 
	}
```
# 459. 重复的子字符串
## 题目:
给定一个非空的字符串 `s` ，检查是否可以通过由它的一个子串重复多次构成。
## 信息:
- **难度**: easy
- **重要性:** #★★★☆☆
## 思路:
- 能由子串重复构成,代表必定存在`s.charAt(i)==s.charAt(0)`.取该子串,并对剩余字符串进行判断即可.
## 关键点:
- **能由子串重复构成,代表必定存在`s.charAt(i)==s.charAt(0)`.**
## 代码:
```java
class Solution {  
    public boolean repeatedSubstringPattern(String s) {  
        int sLen = s.length();
        // i循环条件: 剩余字符串长度应大于等于重复子串长度->i <= sLen/2
        for (int i = 1;(sLen - (i)) >= (i) ; i++){
	        // 判断是否存在首字母相同,即可能重复的子字符串.
            if (s.charAt(i) == s.charAt(0)){  
	            // 取重复子串.
                String sub = s.substring(0,i);  
                int subLen = i;  
                int j = i;  
				// 先判断是否剩余字符串长度大于重复子串.
				// 若大于,则判断接下来相同长度的子串是否跟重复子串匹配.
                while ((sLen - j + 1) >= subLen && s.substring(j,j+subLen).equals(sub)){  
                    j += subLen;  
                    // j能等于sLen,说明一直是匹配的.
                    if (j == sLen) return true;  
                }  
            }  
        }  
        return false;  
    }  
}
```
