### Day1

#### [剑指 Offer 09. 用两个栈实现队列](https://leetcode.cn/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

```python
class CQueue:

    def __init__(self):
        self.res = []
        self.stack = []

    def appendTail(self, value: int) -> None:
        self.stack.append(value)

    def deleteHead(self) -> int:
        if self.res: return self.res.pop()
        if not self.stack: return -1
        while self.stack:
            self.res.apppend(self.stack.pop())
        return self.res.pop()
```

#### [剑指 Offer 30. 包含min函数的栈](https://leetcode.cn/problems/bao-han-minhan-shu-de-zhan-lcof/)

##### 1. 使用一个栈

时间5%， 空间80%。

```python
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack = []


    def push(self, x: int) -> None:
        self.stack.append(x)

    def pop(self) -> None:
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def min(self) -> int:
        return min(self.stack)
```

##### 2. 多使用一个Min_stack维护最小值

时间85%，空间33%。

```python
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack = []
        self.min_stack = [math.inf]


    def push(self, x: int) -> None:
        self.stack.append(x)
        self.min_stack.append(min(x, self.min_stack[-1]))

    def pop(self) -> None:
        self.stack.pop()
        self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def min(self) -> int:
        return self.min_stack[-1]
```

### Day2

#### [剑指 Offer 06. 从尾到头打印链表](https://leetcode.cn/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

```python
class Solution:
    def reversePrint(self, head: ListNode) -> List[int]:
        res = []
        if not head:
            return []
        while head:
            res.append(head.val)
            head = head.next
        res.reverse()
        return res
```

#### [剑指 Offer 24. 反转链表](https://leetcode.cn/problems/fan-zhuan-lian-biao-lcof/)

1.存储pre.next

2.利用cur作为中间值

3.交换pre和pre.next

4.pre前进为next

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        pre = head
        cur = None
        while pre != None:
            next = pre.next
            pre.next = cur
            cur = pre
            pre = next
        return cur
```

#### [剑指 Offer 35. 复杂链表的复制](https://leetcode.cn/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

1.复制简单链表

```python
class Node:
    def __init__(self, x: int, next: 'Node' = None, random: 'Node' = None):
        self.val = int(x)
        self.next = next
        self.random = random
        
class Solution:
	def dupSimple(self, head:'Node') -> 'Node':
        if not head: return None
        cur = head
        copyHead = pre = Node(0)
        while cur:
            node = Node(cur.val)
            pre.next = node
            cur = cur.next
            pre = node
        return copyHead.next
```

2.复制复杂链表

```python
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        if not head: return None
        cur = head
        dic = {}
        while cur:
            dic[cur] = Node(cur.val)
            cur = cur.next
        cur = head
        while cur:
            dic[cur].next = dic.get(cur.next)
            dic[cur].random = dic.get(cur.random)
            cur = cur.next
        return dic[head]
```

### Day3

#### [剑指 Offer 05. 替换空格](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/)

```python
class Solution:
    def replaceSpace(self, s: str) -> str:
        res = ''
        for item in s:
            if item == ' ':
                item = '%20'
            res = res + item
        return res
```

#### [剑指 Offer 58 - II. 左旋转字符串](https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

```python
class Solution:
    def reverseLeftWords(self, s: str, n: int) -> str:
        return s[n:] + s[0:n]
```

### Day4

#### [剑指 Offer 03. 数组中重复的数字](https://leetcode.cn/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

```python
class Solution:
    def findRepeatNumber(self, nums: List[int]) -> int:
        dic = {}
        for item in nums:
            if item in dic:
                return item
            dic[item] = 1
```

#### [剑指 Offer 53 - I. 在排序数组中查找数字 I](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

1.遍历法

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        count = 0
        for item in nums:
            if item == target:
                count = count + 1
        return count
```

2.二分查找：有序的数组

target的index - target-1的index为重复数字的个数。

```python
class Solution:
    def search(self, nums: [int], target: int) -> int:
        def helper(tar):
            i, j = 0, len(nums) - 1
            while i <= j:
                m = (i + j) // 2
                if nums[m] <= tar: i = m + 1
                else: j = m - 1
            return i
        return helper(target) - helper(target - 1)
```



