# 前中后序遍历的迭代写法
## 题目:

## 信息:
- **难度**: easy
- **重要性:** #★★★★☆
## 思路:

#![[二叉树中序遍历（迭代法）.gif|400]]
# 关键点:
- 
## 代码:
```java


```


# 102. 二叉树的层序遍历
## 题目:
给你二叉树的根节点 `root` ，返回其节点值的 **层序遍历** 。 （即逐层地，从左到右访问所有节点）。
## 信息:
- **难度**: medium
- **重要性:** #★★★☆☆
## 思路:
- 
## 关键点:
- 
## 代码:
```java
class Solution {  
    public List<List<Integer>> levelOrder(TreeNode root) {  
  
        List<List<Integer>> res = new ArrayList<>();  
        Queue<TreeNode> que = new LinkedList<>();  
  
        if (root != null) que.offer(root);  
        while (!que.isEmpty()) {  
            int size = que.size();  
            List<Integer> temp = new ArrayList<>();  
//                 以size循环,即对本层节点进行遍历.  
            for (int i = 0; i < size; i++){  
//                 队顶出队,并存储到该层结果中.  
                TreeNode tempNode = que.poll();  
                temp.add(tempNode.val);  
//                存储子节点,由于队列是先进先出,所以子节点会在本层节点遍历完后才遍历.  
                if (tempNode.left != null) que.offer(tempNode.left);  
                if (tempNode.right != null) que.offer(tempNode.right);  
            }  
//            一层结束,存储该层结果.  
            res.add(temp);  
        }  
        return res;  
    }  
}
```
