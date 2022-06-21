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

