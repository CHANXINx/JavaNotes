# 232. 用栈实现队列
## 题目:
请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（`push`、`pop`、`peek`、`empty`）：

实现 `MyQueue` 类：

- `void push(int x)` 将元素 x 推到队列的末尾
- `int pop()` 从队列的开头移除并返回元素
- `int peek()` 返回队列开头的元素
- `boolean empty()` 如果队列为空，返回 `true` ；否则，返回 `false`

**说明：**

- 你 **只能** 使用标准的栈操作 —— 也就是只有 `push to top`, `peek/pop from top`, `size`, 和 `is empty` 操作是合法的。
- 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。
## 信息:
- **难度**: easy
- **重要性:** #★★★★☆
## 思路:
- 用栈实现队列,即通过栈的"先进后出"实现队列的"先进先出".利用两个栈`stackIn`和`stackOut`,`stackOut`用于存储元素的倒序版本,从而正确模拟队列出队操作.
- ~~实现队列/栈,只要保证能实现基本操作即可.例如栈实现队列,保证能符合队列的进出,获取元素即可,无需过分关注内部如何存储.~~
- `dumpStackIn()`函数,是用于将stackIn的元素放入StackOut,因为栈是先进后出,所以将stackIn的元素放入stackOut,此时后进的元素就变为了先进的元素!
>[!GPTANSWER]
>**如果每次调用 `dumpStackIn` 时都将 `stackIn` 中的元素倒入 `stackOut`，会导致 `stackOut` 中的元素顺序被打乱，无法保证队列的顺序性。**
- 例如,先将1放入了stackOut,此时再将3,2也放入了stackOut,此时就变成了`[2,3,1]`,此时就变成了2先出栈,故会错误! 保证stackOut即保证了"先进先出"!
## 关键点:
- 运用两个队列来保证队列的出队操作.
- 需要理解好模拟的出队操作,尤其是当stackOut为空时才往里面添加元素.
## 代码:
```java
class MyQueue {  
    Stack<Integer> stackIn;  
    Stack<Integer> stackOut;  
  
    public MyQueue() {  
        stackIn = new Stack<>();  
        stackOut = new Stack<>();  
  
    }  
  
    public void push(int x) {  
        stackIn.push(x);  
    }  
  
    public int pop() {  
        dumpStackIn();  
        return stackOut.pop();  
    }  
  
    public int peek() {  
        dumpStackIn();  
        return stackOut.peek();  
    }  
  
    public boolean empty() {  
        return stackIn.isEmpty() && stackOut.isEmpty();  
    }  
  
    private void dumpStackIn(){  
        if (stackOut.isEmpty()){  
            while (!stackIn.isEmpty()){  
                stackOut.push(stackIn.pop());  
            }  
        }  
    }  
}  
```
# 225. 用队列实现栈
## 题目:
请你仅使用两个队列实现一个后入先出（LIFO）的栈，并支持普通栈的全部四种操作（`push`、`top`、`pop` 和 `empty`）。
实现 `MyStack` 类：
- `void push(int x)` 将元素 x 压入栈顶。
- `int pop()` 移除并返回栈顶元素。
- `int top()` 返回栈顶元素。
- `boolean empty()` 如果栈是空的，返回 `true` ；否则，返回 `false` 。
**注意：**
- 你只能使用队列的标准操作 —— 也就是 `push to back`、`peek/pop from front`、`size` 和 `is empty` 这些操作。
- 你所使用的语言也许不支持队列。 你可以使用 list （列表）或者 deque（双端队列）来模拟一个队列 , 只要是标准的队列操作即可。
## 信息:
- **难度**: easy
- **重要性:** #★★★☆☆
## 思路:
- 可以使用单向队列或双向队列来实现,使用双向队列则更简单.
- 可以使用"循环队列"来实现先进后出.
- 使用队列实现栈的出栈操作: 因为出栈是末尾弹出,所以当队列有size个元素,即**将前size-1个元素先出队,再入队**.就能在**保证末尾元素被出队,同时保存前面元素顺序不变!**
- 
## 关键点:
- 
## 代码:
```java
class MyStack {  
//    使用Queue实现,不使用末尾弹出.  
    Queue<Integer> queue1;  
  
    public MyStack() {  
        queue1 = new LinkedList<>();  
    }  
  
    public void push(int x) {  
        queue1.add(x);  
    }  
  
    public int pop() {  
        if (!empty()) {  
            int size = queue1.size();  
            while (size > 1) {  
                size--;  
                queue1.add(queue1.poll());  
            }  
            return queue1.poll();  
        }  
        return -1;  
    }  
  
    public int top() {  
        int res = pop();  
        push(res);  
        return res;  
    }  
  
    public boolean empty() {  
        return queue1.isEmpty();  
    }  
}

```
# 20. 有效的括号
## 题目:

## 信息:
- **难度**: easy
- **重要性:** #★☆☆☆☆
## 思路:
- 
## 关键点:
- 
## 代码:
```java


```
# 1047. 删除字符串中的所有相邻重复项
## 题目:

## 信息:
- **难度**: easy
- **重要性:** #★☆☆☆☆
## 思路:
- 
## 关键点:
- 
## 代码:
```java


```
