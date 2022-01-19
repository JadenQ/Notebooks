### 1.链表

#### 1.1单链表

##### [206] 反转链表

###### 1.迭代

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        # 使用迭代
        pre = head
        cur = None
        while pre != None:
        	# 保存Pre.next
            next = pre.next
            # 反转
            pre.next = cur
            cur = pre
            pre = next
        return cur
```

O(n)

###### 2.递归

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        # 使用递归
        # 这个列表长度小于等于1：
        if head == None or head.next == None:
            return head
        # 列表长度大于1
        new_head = self.reverseList(head.next)
        # 从head.next开始，head.next的下一个为head,并且new_head到最后一个
        head.next.next = head
        # head.next为None, head变成tail
        head.next = None
        return new_head

```

##### [92] 反转链表 II

###### Mistakes

```python
class Solution:
    def reverseBetween(self, head: ListNode, left: int, right: int) -> ListNode:
        pre = head
        cur = None
        count = 0
        if head.next = None:
            return head
        while pre != None:
            count = count + 1
            if count >= left and count <= right:
                next = pre.next
                pre.next = cur
                cur = pre
                pre = next
            else:
                cur = pre
                pre = pre.next
        return cur

```

```python
class Solution:
    def reverseBetween(self, head: ListNode, left: int, right: int) -> ListNode:
        if head.next is None:
            return head
        # 使用一个dummy指针，头指针推送到要反转的部分
        dummy = ListNode(-1)
        # 从head前开始
        dummy.next = head
        # 记录为pre，向前推
        pre = dummy.next
        cur = dummy
        # 推dummy指针
        for _ in range(left - 2):
            cur = pre
            pre = pre.next
        # 推到要反转的部分
        for _ in range(left-1, right-1):
            temp = pre.next
            pre.next = cur
            cur = pre
            pre = temp
        return cur

```

```python
class Solution:
    def reverseBetween(self, head: ListNode, left: int, right: int) -> ListNode:
        if head.next is None:
            return head
        # 使用一个dummy指针，头指针推送到要反转的部分
        dummy = ListNode(-1)
        # 从head前开始
        dummy.next = head
        # 记录为pre，向前推
        pre = dummy
        # 推dummy指针
        for _ in range(left - 1):
            pre = pre.next
        cur = pre.next
        # 推到要反转的部分
        for _ in range(right - left):
            temp = cur.next
            cur.next = temp.next
            temp.next = pre.next
            cur.next = temp
        return cur
```

###### 1.插头指针

```python
class Solution:
    def reverseBetween(self, head: ListNode, left: int, right: int) -> ListNode:
        if head.next is None:
            return head
        # 使用一个dummy指针，头指针推送到要反转的部分
        dummy = ListNode(-1)
        # 从head前开始
        dummy.next = head
        # 记录为pre，向前推
        pre = dummy
        # 推dummy指针
        for _ in range(left - 1):
            pre = pre.next
        cur = pre.next
        # 推到要反转的部分
        for _ in range(right - left):
            temp = cur.next
            cur.next = temp.next
            temp.next = pre.next
            pre.next = temp
        return dummy.next
```

###### 2.双指针-头插法 :+1:

Guard: g, point: p

g指针锁定反转开始的位置，将p后的(right - left)个node移动到g和p之间。

- g,p移动到相应位置
- p.next是待拆除的temp
- 将p与后面的节点连接
- temp代替g连接后面
- temp连接到g后面

```python
class Solution:
    def reverseBetween(self, head: ListNode, left: int, right: int) -> ListNode:
        if head.next is None:
            return head
        # 使用一个dummy指针，头指针推送到要反转的部分
        dummy = ListNode(-1)
        # 从head前开始
        dummy.next = head
        # 记录为pre，向前推
        g = dummy
        # 推dummy指针
        for _ in range(left - 1):
            g = g.next
        p = g.next
        # 推到要反转的部分
        for _ in range(right - left):
            temp = p.next
            p.next = p.next.next
            temp.next = g.next
            g.next = temp
        return dummy.next
```

##### [141] 循环链表

###### 1. 存入集合 / 哈希

Python中的set数据结构，元素是非重复的。

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        element_set = set()
        while head:
            if head in element_set:
                return True
            element_set.add(head)
            head = head.next
        return False
```

O(n) 时间，O(n) 空间

###### 2.快慢双指针:+1:

- 如果有环，快慢两个指针会相遇，因此不用使用哈希数据结构。

- fast = 2*slow 时，从头指针到入环点 = 相遇点到入环点

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if head is None:
            return False
        fast, slow = head, head
        while fast != None:
            slow = slow.next
            if fast.next != None:
                fast = fast.next.next
            else: return False
            if fast == slow:
                return True
```

##### [142] 循环链表 II

###### 1.存入集合 / 哈希

```python
class Solution:
    def detectCycle(self, head: ListNode) -> ListNode:
        elements = set()
        while head:
            if head in elements:
                return head
            elements.add(head)
            head = head.next
        return None
```

###### 2.快慢双指针

- 如果有环，快慢两个指针会相遇，因此不用使用哈希数据结构。

- fast = 2*slow 时，从头指针到入环点 = 相遇点到入环点

  ![fig1](https://assets.leetcode-cn.com/solution-static/142/142_fig1.png)

  根据题意，任意时刻，$\textit{fast}$ 指针走过的距离都为 $\textit{slow}$ 指针的 22 倍。因此，我们有

  a+(n+1)b+nc=2(a+b) $\implies$ a=c+(n-1)(b+c)
  a+(n+1)b+nc=2(a+b)⟹a=c+(n−1)(b+c)

  有了 a=c+(n-1)(b+c)a=c+(n−1)(b+c) 的等量关系，我们会发现：从相遇点到入环点的距离加上 n-1圈的环长，恰好等于从链表头部到入环点的距离。

  因此，当发现 slow 与 fast 相遇时，我们再额外使用一个指针 ptr。起始，它指向链表头部；随后，它和 slow 每次向后移动一个位置。最终，它们会在入环点相遇。

  

```python
class Solution:
    def detectCycle(self, head: ListNode) -> ListNode:
        if head is None:
            return None
        # 从head 开始出发
        fast, slow = head, head
        while fast is not None:
            slow = slow.next
            if fast.next is not None:
                fast = fast.next.next
            else: return None
            if fast == slow:
                # 如果有相遇点，说明有环
                # a pointer start from head 来捕捉入环点
                pnt = head
                while pnt != slow:
                    pnt = pnt.next
                    slow = slow.next
                return pnt
        return None
```

##### [83] 删除重复元素

###### Mistakes

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if head is None or head.next is None:
            return head
        pnt = head
        pnt2 = ListNode(-1)
        pnt2.next = head
        while pnt1 != None:
            if pnt1 == pnt2: # 我们需要比较链表的值
                pnt1 = pnt1.next.next
                pnt2 = pnt2.next.next
            else:
                pnt1 = pnt1.next
                pnt2 = pnt2.next
        return head
```

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if head is None or head.next is None:
            return head
        pnt = head
        while pnt.next:
            if pnt.val == pnt.next.val:
                pnt = pnt.next.next # mistake
            else:
                pnt = pnt.next
        return head
```

Solution:

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if head is None or head.next is None:
            return head
        pnt = head
        while pnt.next:
            if pnt.val == pnt.next.val:
                pnt.next = pnt.next.next #指针跳转
            else:
                pnt = pnt.next
        return head
```

##### [82] 删除重复元素II

###### Mistake

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if not head or not head.next:
            return head
        pnt1 = ListNode(-1)
        pnt1.next = head
        pnt2 = pnt1.next
        dummy = pnt1
        dummy.next = pnt1.next
        while pnt2.next:
            if pnt1.next.val == pnt2.next.val:
                while pnt2.val == pnt2.next.val:
                    pnt2 = pnt2.next
                pnt1.next = pnt2.next
                pnt2 = pnt2.next
            else:
                pnt1 = pnt1.next
                pnt2 = pnt2.next
        return head # what to return: dummy.next
```

###### Solution

双指针，pnt1在重复数据前等待，pnt2向前探索

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if not head or not head.next:
            return head
        pnt1 = ListNode(-1)
        pnt1.next = head
        pnt2 = pnt1.next
        dummy = pnt1
        dummy.next = pnt1.next

        while pnt2 and pnt2.next:
            if pnt1.next.val == pnt2.next.val:
                while pnt2.next and pnt2.val == pnt2.next.val:
                    pnt2 = pnt2.next
                pnt1.next = pnt2.next
                pnt2 = pnt2.next
            else:
                pnt1 = pnt1.next
                pnt2 = pnt2.next
        return dummy.next
```

###### Better Solution :+1:

##to be continued##



##### [61] 旋转链表

###### 超时

```python
class Solution:
    def rotateRight(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if not head or not head.next or k < 1:
            return head
        # only two elements
        if not head.next.next:
            if k % 2 == 0:
                return head
            else:
                tail = head.next
                tail.next = head
                head.next = None
                return tail
        for _ in range(k):
            pnt = head
            dummy = ListNode(-1)
            dummy.next = head
            while pnt.next.next:
                pnt = pnt.next
            dummy.next = pnt.next
            pnt.next.next = head
            pnt.next = None
            head = dummy.next
        return dummy.next
```

可以模仿链表长度为2时，成环操作。

###### Mistake

旋转方向错误

```python
class Solution:
    def rotateRight(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if not head or not head.next or k < 1:
            return head
        tail, pnt = head, head
        count = 0
        while tail.next:
            tail = tail.next
            count = count + 1
        tail.next = head
        for _ in range(k):
            pnt = pnt.next
        pnt2 = pnt
        for _ in range(count):
            pnt2 = pnt2.next
        pnt2.next = None
        return pnt
```

###### Solution

```python
class Solution:
    def rotateRight(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if not head or not head.next or k < 1:
            return head
        pnt, tail = head, head
        count = 1
        while tail.next:
            tail = tail.next
            count = count + 1
        tail.next = head
        print(count)
        for _ in range(count - 1 - k % count):
            pnt = pnt.next
        result = pnt.next
        pnt.next = None
        return result
```

1.正确计算count, 初始化count = 1 而不是0

2.找到想要切割的位置和移动次数的关系 count -1 - k mod count

##### [234] 回文链表

###### 1.存储到数组中

O(n), O(n). 将链表存到数组里面进行验证。

```python
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        if not head.next:
            return True
        pnt = head
        list = []
        while pnt:
            list.append(pnt.val)
            pnt = pnt.next
        return list == lst[::-1]
```

###### 2.快慢指针

1. fast = 2 * slow, fast走到链表尾部时，slow走到中间
2. slow停在中间，反转后面部分的链表
3. pnt从head开始，slow从中间同时出发，验证是否一致

```python
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        if not head.next:
            return True
        if not head.next.next:
            if head.val == head.next.val:
                return True
            else: return False
        fast, slow, pnt2 = head, head, head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
        pnt1 = slow
        # 反转后半部分
        pnt1 = self.reverseList(pnt1)
        while pnt1:
            if pnt1.val != pnt2.val:
                return False
            pnt1 = pnt1.next
            pnt2 = pnt2.next
        return True
    def reverseList(self, head: ListNode) -> ListNode:
        # 使用迭代
        pre = head
        cur = None
        while pre != None:
            # 保存Pre.next
            next = pre.next
            # 反转
            pre.next = cur
            cur = pre
            pre = next
        return cur
```

[]