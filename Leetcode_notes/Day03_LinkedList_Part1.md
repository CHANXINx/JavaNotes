# 203.移除链表元素
## 题目:
给你一个链表的头节点 `head` 和一个整数 `val` ，请你删除链表中所有满足 `Node.val == val` 的节点，并返回 **新的头节点** 。
**输入:head = `[1,2,6,3,4,5,6]`, val = 6 输出：**`[1,2,3,4,5]`
## 信息:
- **难度**: easy
- **重要性:** #★★★☆☆
## 思路:
- 这题难度不大,但是引入一个新方法:虚拟头节点.虚拟头节点可以使得在删除头节点时无需额外判断.
## 关键点:
- **虚拟头节点的引入!**
## 代码:
```java
class Solution {  
    public ListNode removeElements(ListNode head, int val) {  
        // 虚拟头节点,指向头节点,值任意.
        ListNode dummyNode = new ListNode(0, head);  
        
        ListNode curr = dummyNode;  
        while (curr.next != null) {  
            if (curr.next.val == val) {  
                curr.next = curr.next.next;  
            }else {  
                curr = curr.next;  
            }  
        }  
        return dummyNode.next;  
    }  
}

```
# 707.设计链表
## 题目:
你可以选择使用单链表或者双链表，设计并实现自己的链表。
单链表中的节点应该具备两个属性：`val` 和 `next` 。`val` 是当前节点的值，`next` 是指向下一个节点的指针/引用。
如果是双向链表，则还需要属性 `prev` 以指示链表中的上一个节点。假设链表中的所有节点下标从 **0** 开始。
实现 `MyLinkedList` 类：
- `MyLinkedList()` 初始化 `MyLinkedList` 对象。
- `int get(int index)` 获取链表中下标为 `index` 的节点的值。如果下标无效，则返回 `-1` 。
- `void addAtHead(int val)` 将一个值为 `val` 的节点插入到链表中第一个元素之前。在插入完成后，新节点会成为链表的第一个节点。
- `void addAtTail(int val)` 将一个值为 `val` 的节点追加到链表中作为链表的最后一个元素。
- `void addAtIndex(int index, int val)` 将一个值为 `val` 的节点插入到链表中下标为 `index` 的节点之前。如果 `index` 等于链表的长度，那么该节点会被追加到链表的末尾。如果 `index` 比长度更大，该节点将 **不会插入** 到链表中。
- `void deleteAtIndex(int index)` 如果下标有效，则删除链表中下标为 `index` 的节点。
## 信息:
- **难度**: Medium
- **重要性:** #★★★☆☆
## 思路:
- 设计自己的链表,定义如下方法:1)`get(int index)`:获取节点的值; 2)`addAtHead()`:插入新的头节点; 3)`addAtTail(int val)`:在尾节点后插入节点; 4)`addAtIndex(index,val)`:在index节点后插入新节点; 5)`deleteAtIndex(index)`:删除index节点.
- 要注意,我们只有头节点,要获取第index个节点,或者尾节点,必须通过遍历n次得到.
- 定义虚拟头节点,使得插入新的头节点更方便.
- 增/删节点,无非就是获取当前节点,并更改next指向!
## 关键点:
- 有对头节点的增删,定义虚拟头节点,操作头节点更方便!
## 代码:
```java
class ListNode{  
    int val;  
    ListNode next;  
    public ListNode(){}  
    public ListNode(int val){  
        this.val = val;  
    }  
}  
  
//leetcode submit region begin(Prohibit modification and deletion)  
class MyLinkedList {  
    int size;  
//    虚拟头节点.  
    ListNode head;  
    public MyLinkedList() {  
        size = 0;  
        head = new ListNode(0);  
    }  
      
    public int get(int index) {  
        if (index < 0 || index >= size){  
            return -1;  
        }  
        ListNode temp = head;  
        for (int i = 0; i <= index; i++){  
            temp = temp.next;  
        }  
        return temp.val;  
    }  
      
    public void addAtHead(int val) {  
        ListNode newHead = new ListNode(val);  
        newHead.next = head.next;  
        head.next = newHead;  
        size++;  
    }  
      
    public void addAtTail(int val) {  
        ListNode curr = head;  
        ListNode finalNode = new ListNode(val);  
        for (int i = 0; i < size; i++){  
            curr = curr.next;  
        }  
        curr.next = finalNode;  
        size++;  
  
    }  
      
    public void addAtIndex(int index, int val) {  
        ListNode insertNode = new ListNode(val);  
        if (index > size) {  
            return;  
        } else if (index == size) {  
            addAtTail(val);  
            return;  
        } else {  
            ListNode temp = head;  
            for (int i = 0; i <= index; i++) {  
                if (i == index){  
                    insertNode.next = temp.next;  
                    temp.next = insertNode;  
                    size++;  
                    return;  
                }  
                temp = temp.next;  
            }  
        }  
  
    }  
      
    public void deleteAtIndex(int index) {  
        if (index >= size || index < 0){  
            return;  
        }else {  
            ListNode temp = head;  
            for (int i = 0; i <= index; i++){  
                if (i == index){  
                    temp.next = temp.next.next;  
                    size--;  
                    return;  
                }  
                temp = temp.next;  
            }  
        }  
    }  
}
```
# 206.反转链表
## 题目:
给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。
## 信息:
- **难度**: easy
- **重要性:** #★★★★☆
## 思路:
- 单链表的操作,定义三个节点.prev,用于表示前驱节点;curr,表示当前节点;next,用于表示后继节点.next用于保存curr.next,因为反转节点意味着更新curr的指向,所以需要先保存`curr.next`!同时,prev需要一直保存前驱节点,使得能实现反转!
- **1)先保存curr.next; 2)更新curr的指向; 3)更新前驱prev和curr.**
## 关键点:
- **定义next或temp用于保存curr.next!!!!** 
- 操作curr.next时,需要判断curr是否为空.所以循环条件为curr!=null.
## 代码:
```java
class Solution {  
    public ListNode reverseList(ListNode head) {  
        ListNode prev = null;  
        ListNode cur = head;  
        ListNode next = null;  
        while (cur != null){  
            next = cur.next;  
            cur.next = prev;  
            prev = cur;  
            cur = next;  
        }  
        return prev;  
    }  
}
```
