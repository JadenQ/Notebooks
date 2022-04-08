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

思路与 路径总和 III 一致。

**[超出时间限制]**

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        prefix = collections.defaultdict(int)
        sum_, res = 0, 0
        prefix[0] = 1
        for i in range(len(nums)):
            sum_ += nums[i]
            if sum_ - k in list(prefix.keys()):
                res += prefix[sum_ - k]
            prefix[sum_] += 1
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

