# 150. 逆波兰表达式求值
## 题目:
给你一个字符串数组 `tokens` ，表示一个根据 [逆波兰表示法](https://baike.baidu.com/item/%E9%80%86%E6%B3%A2%E5%85%B0%E5%BC%8F/128437) 表示的算术表达式。
请你计算该表达式。返回一个表示表达式值的整数。
**注意：**
- 有效的算符为 `'+'`、`'-'`、`'*'` 和 `'/'` 。
- 每个操作数（运算对象）都可以是一个整数或者另一个表达式。
- 两个整数之间的除法总是 **向零截断** 。
- 表达式中不含除零运算。
- 输入是一个根据逆波兰表示法表示的算术表达式。
- 答案及所有中间计算结果可以用 **32 位** 整数表示。
## 信息:
- **难度**: Medium
- **重要性:** #★★★☆☆
## 思路:
- 遇到这种需要返回前面的两个元素的,可以采用栈来实现.
- 若采用队列,则无法返回刚入栈的两个元素,而是返回最先入栈的两个元素.看似队列没问题,实则仔细思考,在`["4","13","5","/","+"]`时,此时采用队列的话,就会是4/13,无法获得正确答案!
## 关键点:
- 需要获得当前字符的前n个元素,可采用栈来实现!!!
## 代码:
```java
class Solution {  
    public int evalRPN(String[] tokens) {  
        Deque<Integer> stack = new LinkedList<>();  
        for (String s : tokens){  
	        // 判断当前字符数s是+,-,或/.
            if ("+-*/".contains(s)){  
                int a = stack.pop();  
                int b = stack.pop();  
                switch (s) {  
                // 弹出后两个计算,并再次入栈.
                    case "+" -> stack.push(b+a);  
                    case "-" -> stack.push(b-a);  
                    case "*" -> stack.push(b*a);  
                    case "/" -> stack.push(b/a);  
                }  
            }else  
	            // Integer.valueOf将字符串转换为Integer型.
                stack.push(Integer.valueOf(s));  
        }  
        return stack.pop();  
    }  
}
```
# 239. 滑动窗口最大值
## 题目:
给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

返回滑动窗口中的最大值.
## 信息:
- **难度**: hard
- **重要性:** #★★★★★
## 思路:
- 这题是单调队列的题目!获得窗口内的最值.
- 单调队列,用于获取队列/窗口内的最值,所以队顶必须为当前窗口内的最值.作为处理这类题目的特殊的队列,**与常规队列不同,无需维护窗口内的所有元素,只需要维护窗口内的最值即可**!
## 关键点:
- 单调队列的构造,无需维护窗口内所有元素.
## 代码:
```java
class Solution {  
    public int[] maxSlidingWindow(int[] nums, int k) {  
        MyQueue queue = new MyQueue();  
        int n = nums.length;  
        int[] res = new int[n-k+1];  
        if (n == 1 || k == 1) return nums;  
		// 先入队k-1个.
        for (int i = 0;i < k-1; i++){  
            queue.push(nums[i]);  
        }  
        // 入队,获得当前窗口最大值,出队.
        for (int i = 0; i <= (n-k);i++){  
            queue.push(nums[i+k-1]);  
            res[i] = queue.max();  
            queue.pop(nums[i]);  
        }  
        return res;  
    }  
}  
  
class MyQueue{  
	// 频繁增删,用LinkedList.
    Deque<Integer> que = new LinkedList<>();  
	// 为空则入队;或队底的元素小于当前元素,则弹出小的,推入大的.
    public void push(int n){  
        while (!que.isEmpty() && que.peekLast() < n){  
            que.pollLast();  
        }  
        que.add(n);  
    }  
	// 队顶始终为最大值,故返回peek()即可.
    public int max(){  
        return que.peek();  
    }  
	// 若移出窗口的元素在队顶,则移出.
    public void pop(int n){  
        if (!que.isEmpty() && n == que.peek()) que.poll();  
    }  
}
```

# 347. 前 K 个高频元素

## 题目:
给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。
## 信息:
- **难度**: medium
- **重要性:** #★★★☆☆
## 思路:
- 回想到之前存放字母出现的频次,因为小写字母只有26个,故可以用数组来存储.但对于数字,只能采用Map来存储,其中key为数字值,value为出现频次.
- 返回高频元素,可以采用优先级队列`PriorityQueue`.优先级队列是将入队元素按Comparator的顺序排列(即出现频次排列,默认为小顶堆).
>[!函数式接口Comparator中的方法compare(T o1,T o2)]
>当返回值为负数时,代表o1小于o2,返回值为正数时,代表o1>o2.
>**return o1-o2 ,代表升序排列.**

>[!小顶堆和大顶堆]
>小顶堆即队首元素最小,按升序排列;大顶堆则反过来,队顶元素最大,按降序排列. 对于此题,采用小顶堆较方便,因为可以更好的弹出频次较低的元素.
## 关键点:
- 采用Map存储数字的出现频次.
- 选取优先级队列存储,便于获得出现频次前k的元素.
## 代码:
此处的Comparator.comparingInt(map::get)不太好理解,可以转换成:
`(a,b) -> Integer.compare(map.get(a)-map.get(b))`
```java
class Solution {  
    public int[] topKFrequent(int[] nums, int k) {  
        HashMap<Integer,Integer> map = new HashMap<>();  
        // 存储数字出现的频次.
        for (int i = 0; i < nums.length;i++){  
            map.put(nums[i],map.getOrDefault(nums[i],0)+1);  
        }  
	//优先级队列:指定队列大小为k,按Comparator.comparingInt(map::get)排序.
        Queue<Integer> queue1 = new PriorityQueue<>(k,Comparator.comparingInt(map::get));  
        // 入队,
        for (Integer key : map.keySet()){  
            queue1.add(key);  
            if (queue1.size() > k) queue1.poll();  
        }  
        int[] res = new int[k];  
        // 存储前k个高频元素.
        for (int i = 0; i < k; i++){  
            res[i] = queue1.poll();  
        }  
        return res;  
    }  
}
```
