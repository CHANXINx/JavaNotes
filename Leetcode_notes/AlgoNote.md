# 双指针法:

# 虚拟头结点:
- 虚拟头结点主要用于简化空指针时的边界条件处理.
	- 例如当`head==null`时,需要判断curr是否为空,才能使用边界条件`curr.next == null`进行判断.
	- 若使用`dummyHead`,则可统一写成`curr.next == null`进行边界条件的判断.因为在初始化时,不会出现`curr==null`的情况.