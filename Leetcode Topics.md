### Topics and templates

[主题目录](https://github.com/SharingSource/LogicStack-LeetCode/wiki) 

#### Topic1 - 前缀和 Prefix Sum

##### k-sum系列

###### 两数之和

用map数据结构存储前缀和，如果符合关系diff = target - num[i]的前缀和diff曾经出现在map中，说明满足要求。

```java
public int[] twoSum(int[] nums, int target){
    Map<Integer, Integer> map = new HashMap<>();
    for(int i = 0; i < nums.length; i++){
        int diff = target - num[i];
        if(map.containsKey(diff)) return new int[]{map.get(diff),i};
        map.put(nums[i], i);
    }
    return null;
}
```

###### [560] 等于K的连续子序列

参考： [this link](https://leetcode-cn.com/problems/subarray-sum-equals-k/solution/python3-by-wu-qiong-sheng-gao-de-qia-non-w6jw/).

思路与 路径总和 III 一致。

**[AC]**

```python
import collections
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        n = len(nums)
        res = 0
        preSums = collections.defaultdict(int)
        preSums[0] = 1
        presum = 0
        for i in range(n):
            presum += nums[i]
            res += preSums.get(presum - k,0)
            preSums[presum] += 1
        return res
```

**[AC]**

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
		Map<Integer, Integer> map = new HashMap<>();
        int sum = 0, res = 0;
        map.put(0,1);
        for (int num : nums){
            sum+=num;
           	if(map.containsKey(sum-k)) res += map.get(sum-k);
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return res;
    }
}
```

#### Topic2 - 二分查找

##### 二分查找工具包

Credit to this [link](https://www.liujiangblog.com/course/python/57).

```python
"""Bisection algorithms."""

def insort_right(a, x, lo=0, hi=None):

    if lo < 0:
        raise ValueError('lo must be non-negative')
    if hi is None:
        hi = len(a)
    while lo < hi:
        mid = (lo+hi)//2
        if x < a[mid]: hi = mid
        else: lo = mid+1
    a.insert(lo, x)

insort = insort_right   # backward compatibility

def bisect_right(a, x, lo=0, hi=None):

    if lo < 0:
        raise ValueError('lo must be non-negative')
    if hi is None:
        hi = len(a)
    while lo < hi:
        mid = (lo+hi)//2
        if x < a[mid]: hi = mid
        else: lo = mid+1
    return lo

bisect = bisect_right   # backward compatibility

def insort_left(a, x, lo=0, hi=None):

    if lo < 0:
        raise ValueError('lo must be non-negative')
    if hi is None:
        hi = len(a)
    while lo < hi:
        mid = (lo+hi)//2
        if a[mid] < x: lo = mid+1
        else: hi = mid
    a.insert(lo, x)


def bisect_left(a, x, lo=0, hi=None):

    if lo < 0:
        raise ValueError('lo must be non-negative')
    if hi is None:
        hi = len(a)
    while lo < hi:
        mid = (lo+hi)//2
        if a[mid] < x: lo = mid+1
        else: hi = mid
    return lo

# Overwrite above definitions with a fast C implementation
try:
    from _bisect import *
except ImportError:
    pass
```

#### Topic3 - 线段树

对于存储了数值3，5的线段树，结果如下。[（引用来源）](https://www.bilibili.com/video/BV1QT4y1Z7rR?p=1)

<img src="/pics/1649403442358.png" alt="1649403442358" style="zoom:50%;" />

```python
class segmentTree:
	def __init__(self, len_a):
        # 一个用来存储位置信息的数组，设输入范围x，该数组长度为2*x+2
        self.a = [0 for _ in range(len_a)]
        
	def tree_insert(self, pos: int, left: int, right: int, num: int):
        self.a[pos] += 1
        # 到达单位区间num
        if num == right and num == left:
            return
        # 计算区间的中点
        mid = (left + right) / 2
        # 计算左右孩子区间对应的数组下标
        left_child = pos * 2 + 1
        right_child = pos * 2 + 2
        if num <= mid:
            # 插入左孩子
            self.tree_insert(left_child, left, mid, num)
        else:
            # 插入右孩子
            self.tree_insert(right_child, mid + 1, right, num)
    
    def tree_search(self, pos: int, left: int, right: int, num: int):
        if num == right and num == left:
            return self.a[pos]
        mid = (left + right) / 2
        left_child = pos * 2 + 1
        right_child = pos * 2 + 2
        if num <= mid:
            return self.tree_search(left_child, left, mid, num)
       	else:
            return self.tree_search(right_child, mid+1, right, num)
    
    def print_tree(self, pos: int, left: int, right: int):
        print('left, right, pos and self.a[pos]',left, right, pos, self.a[pos])
        if left == right: return
        mid = (left + right) / 2
        self.print_tree(pos*2+1, left, mid)
        self.print_tree(pos*2+2, mid+1, right)

# 保存线段树数据的数组a长度
segTree = segmentTree(100)
left = 0
right = 5
segTree.tree_insert(0, left, right, 3)
segTree.tree_insert(0, left, right, 5)
segTree.tree_insert(0, left, right, 2)
segTree.print_tree(0, left, right)

for i in range(5):
	if tree_search(0, left, right, i):
        print(i,' is in tree.')
```

#### Topic4 - 字典树（前缀树）

字典树的构建与使用。

###### 标准版

```python
class Trie(object):
    class TrieNode(object):
        def __init__(self):
            self.is_word = Flase
            self.children = [None] * 26
    def __init__(self):
        self.root = Trie.TrieNode()
    
    def insert(self, word):
        """
        Insert a word into trie,
        type: 
        	word: str
        	rtype: void
        """
        p = self.root
        for c in word:
            index = ord(c) - ord('a')
            if not p.children[index]:
                # 这个字符不在子节点当中，新建一个节点
                p.children[index] = Trie.TrieNode()
            # 移动p到子节点
            p = p.children[index]
        # 已经达到结尾，在这个节点对象标注为True
        p.is_word = True
    
    def find(self, prefix):
        """
        Returns the trie node that start with prefix，一个个字符地移动
        , if no, return None
        type:
        	prefix: str
        	rtype: TrieNode
        """
        p = self.root
        for c in prefix:
            index = ord(c) - ord('a')
            if not p.children[index]:
                return None
            p = p.children[index]
        return p
    
    def search(self, word):
        """
        Returns if the word is in the trie.
        type:
            word: str
            rtype: bool
        """
        node = self.find(word)
        return node is not None and node.is_word
    
    def startWith(self, prefix):
        return self.find(prefix) is not None
        
```

###### Python字典版

使用Python自带的数据结构，速度更快。

```python
class Trie:

    def __init__(self):
        self.root = {}

    def insert(self, word: str) -> None:
        p = self.root
        for c in word:
            if c not in p:
                # 创建一个新的节点
                p[c] = {}
            p = p[c]
        # 为这个节点添加一个标记证明已经结束一个词
        p['#'] = True

    def find(self, prefix: str) -> dict:
        p = self.root
        for c in prefix:
            if c not in p:
                return None
            p = p[c]
        return p


    def search(self, word: str) -> bool:
        node = self.find(word)
        return node is not None and '#' in node

    def startsWith(self, prefix: str) -> bool:
        node = self.find(prefix)
        return node is not None
```

#### Topic 5 子序列问题

##### 974. 和可被K整除的子数组

使用同余定理，如果两个整数a - b mod k == 0, 则a  mod k == b mod k, 反之也成立。因此简化了过程，不用计算前缀和之间的两两只差，可以直接统计 item1 **mod** k == item2 **mod** k 的个数，利用哈希表（字典）实现。

```python
class Solution:
    def subarraysDivByK(self, nums: List[int], k: int) -> int:
        record = {0:1}
        total, ans = 0, 0
        for item in nums:
            total += item
            modulus = total % k
            # 如果没有添加过，identical计数为0， 如果添加过，返回之前的记录
            identical = record.get(modulus, 0)
            ans += identical
            # 为这一个具有相同模结果下的Key添加一个计数
            record[modulus] = identical + 1
        return ans
```

##### 560. 等于K的连续子序列

见 前缀和主题。

##### 两数之和 - 变体

给定一个序列nums, 两数之和为target的子序列的个数。

##### 674. 最长连续递增子序列

```python
class Solution:
    def findLengthOfLCIS(self, nums: List[int]) -> int:
        ans = 0
        start = 0
        for i in range(len(nums)):
            if i > 0 and nums[i] <= nums[i-1]:
                start = i
            ans = max(ans, i - start + 1)
        return ans
```

##### 1027. 最长等差子序列



##### 845. 数组中的最长山脉

##### 978. 最长湍流子数组

#### Topic6 排序问题

