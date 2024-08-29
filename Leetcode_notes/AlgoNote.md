# 双指针法:

- 也叫快慢指针法,**通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。**
## 定义快慢指针
- 快指针：寻找新数组的元素,新数组就是不含有目标元素的数组
- 慢指针：指向**更新新数组**下标的位置
## 细分分类:
### 滑动窗口:
[209. 长度最小的子数组 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)
- 参考本题.利用fast指针遍历到最远处,取得满足sum>val的长度的数组,再移动slow指针,逐步缩小"窗口",以取得最小窗口.
- 
# 虚拟头结点:
- 虚拟头结点主要用于简化空指针时的边界条件处理.
	- 例如当`head==null`时,需要判断curr是否为空,才能使用边界条件`curr.next == null`进行判断.
	- 若使用`dummyHead`,则可统一写成`curr.next == null`进行边界条件的判断.因为在初始化时,不会出现`curr==null`的情况.


# ACM模式
