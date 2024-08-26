# 24.两两交换链表中的节点
## 题目:
- 给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。
## 信息:
- **难度**: medium
- **重要性:** #★★★★☆
## 思路:
- 定义虚拟头节点,指向头节点.有了虚拟头节点后,方便对头节点与第二个节点进行交换操作.交换操作遵循如下步骤:![[LinkedListPart2_img1.png]]
	1. 步骤1: `cur.next = cur.next.next;`
	2. 步骤2: `cur.next.next = (cur.next);` 此时由于`cur.next`已在步骤1中被替换掉,所以此时在步骤1之前应该使用临时变量`temp1`来保存`cur.next`的值;
	3. 步骤3:`cur.next.next.next = (cur.next.next.next);`,此时由于`cur.next.next.next`已在步骤2中被斩断,所以在步骤2前应该使用`temp2`进行临时保存.
	4. cur节点更新,cur节点的更新逻辑应该是待交换的两个节点的前一个节点,所以有`cur = cur.next.next`
- 循环条件:因为是对cur的后两个节点进行替换操作,所以必须保证`cur.next`和`cur.next.next`不为空.当`cur.next`为空时,此时是偶数个节点,且cur已是链表的最后一个元素;当`cur.next`不为空,而`cur.next.next`为空时,此时是奇数个节点,并且cur已是倒数第二个元素.
- 特殊情况的处理: 1)链表只有单个元素,此时`cur.next.next == null`,所以不会进入循环,故直接返回`dummyHead.next`; 2)链表为空,此时`cur.next == null`,直接返回`dummyHead.next`
## 关键点:
- 定义虚拟头节点,对后两个节点进行交换操作;
- 循环条件的处理,因为是对curr的后两个节点操作,所以保证的是`curr.next, curr.next.next != null`.
## 代码:
```
class Solution {  
    public ListNode swapPairs(ListNode head) {  
        ListNode dummyHead = new ListNode(0);  
        dummyHead.next = head;  
        ListNode curr = dummyHead;  
        if (head == null || head.next == null)  
            return head;  
        ListNode temp1;  
        ListNode temp2;  
  
        while (curr.next != null && curr.next.next != null){  
            temp1 = curr.next;  
            temp2 = curr.next.next.next;  
  
            curr.next = curr.next.next;  
            curr.next.next = temp1;  
            curr.next.next.next = temp2;  
  
            curr = curr.next.next;  
        }  
        return dummyHead.next;  
    }  
}
```

# 19.删除链表的倒数第N个节点 
## 题目:
- 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。
## 信息:
- **难度**:medium
- **重要性:** #★★★☆☆
## 思路:
- 通过快慢指针来判断是否到达了倒数第n个节点.具体如下:快,慢指针都指向头节点.快节点先移动n次,然后快慢节点同时移动,此时当快节点到达末尾时,慢节点到达倒数第n个节点!
## 关键点:
- 利用快指针先移动n个单位,使得快慢指针距离n个单位.
## 代码:
```java
class Solution {  
    public ListNode removeNthFromEnd(ListNode head, int n) {  
        ListNode dummyHead = new ListNode(0);  
        dummyHead.next = head;  
  
        ListNode slow = dummyHead;  
        ListNode fast = dummyHead;  
        int count = 0;  
        while (fast != null) {  
            %% 快指针先移动n个单位 %%
            if (count <= n) {  
                fast = fast.next;  
                count++;  
                continue;  
            }  
            fast = fast.next;  
            slow = slow.next;  
        }  
        slow.next = slow.next.next;  
        return dummyHead.next;  
    }  
}
```

# 面试题 02.07. 链表相交
## 题目:
- 给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 `null` 。
## 信息:
- **难度**: easy
- **重要性:** #★★★☆☆
## 思路:
- 拼接两个链表n1+n2,此时就能保证能在经过长度n1+n2后,到达同一起点,即形成了相同长度的路径,在遍历完这个长度后,会在相交点相遇,或者同时到达null.
## 关键点:
- 
## 代码:
```java
public class Solution {  
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {  
        ListNode currA = headA;  
        ListNode currB = headB;  
        while (currA != currB){  
            if (currA != null) {  
                currA = currA.next;  
            }else {  
                currA = headB;  
            }  
            if (currB != null) {  
                currB = currB.next;  
            }else {  
                currB = headA;  
            }  
        }  
        return currA;  
    }  
}
```

# 142. 环形链表
## 题目:
- 给定一个链表的头节点 `head` ，返回链表开始入环的第一个节点。如果链表无环，则返回 `null`。如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。**不允许修改链表。** 
## 信息:
- **难度**:
- **重要性:** #★★★★★
## 思路:
- 带有考察数学知识的题目.需要进行细心的推理!**首先**,明确一点,当有环时,可以转化为一个追赶问题,即**步长为2的快指针一定会追赶上步长为1的慢指针**,因为他们之间的距离是以不断减1的!据此,是否有环的逻辑完成,接下来就是判断环的起点!这里用一个图和公式进行说明:![[LinkedListPart2_img2.png]]
当**快慢指针相遇**时,有以下情况:
- 慢指针走过的节点数为$x+y$,快指针走过的节点数为$x+y+n(y+z)$,并且由于慢指针的步长为1,快指针的步长为2,所以有$(x+y)\times2=x+y+n(y+z)$
- 据此可推出→ $x+y=n(y+z)$.即头节点到环形入口节点的节点数目$x=n(y+z)-y$
- 理解一下,当n=1时,此时$x=z$;当n≠1时,此时$x=n(y+z)-y$,即走过了n个环的节点数$n(y+z)$-环形入口到相遇节点的节点数y.
- 
## 关键点:
- 
## 代码:
```java
public class Solution {  
    public ListNode detectCycle(ListNode head) {  
        if (head == null || head.next == null) {  
            return null;  
        }  
        ListNode fast = head;  
        ListNode slow = head;  
  
        while (fast != null && fast.next != null){  
            fast = fast.next.next;  
            slow = slow.next;  
  
            if (fast == slow) {  
                slow = head;  
  
                while (fast != slow) {  
                    fast = fast.next;  
                    slow = slow.next;  
                }  
                return fast;  
            }  
        }  
        return null;  
    }  
}

```
