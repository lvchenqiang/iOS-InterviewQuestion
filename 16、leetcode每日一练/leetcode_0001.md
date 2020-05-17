## 数组和链表
### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。


```python
    def twoSum(self, nums: List[int], target: int) -> List[int]:
         hashmap = {}
         for i,num in enumerate(nums):
             if hashmap.get(target - num) is not None:
                 return [i,hashmap.get(target - num)]
             hashmap[num] = i  
```

### [2.三数之和](https://leetcode-cn.com/problems/3sum/)
> 给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组


```python

def threeSum(nums: List[int]) -> List[List[int]]:

    length = len(nums)
    if length < 3:
        return []
    nums.sort()
    res = []
    print(nums)
    if nums[length-1] == 0 and nums[0] == 0 and length >=3:
        return [[0,0,0]]
    for i,num in enumerate(nums):
        if num > 0 :
            break

        left = i + 1
        right = length-1
        if i == 0 or nums[i] != nums[i-1]:

            while left < right:

                tmp = num + nums[left] + nums[right]
                print("==i",i,"===left",left,"===right",right)
                if tmp == 0:
                    res.append([num,nums[left],nums[right]])
                    print(res)

                    while left < right  and nums[left] == nums[left+1]:
                        left += 1

                    while nums[right] == nums[right-1] and left < right  :
                        right -= 1

                    left += 1
                    right -= 1

                elif tmp < 0:
                    left += 1
                else:
                    right -= 1
    return res


```



### [3.求众数](https://leetcode-cn.com/problems/majority-element/)


方案1：hashmap
```python

def majorityElement(nums: List[int]) -> int:
      hashMap = {}
      maxC = 1
      maxN = nums[0]
      length = len(nums)
      for index, num in enumerate(nums):
          if hashMap.get(num) is not  None:
              count = hashMap.get(num)
              count += 1
              hashMap[num] = count
              if count > maxC :
                  maxC = count
                  maxN = num
                  if count > int(length/2):
                      return maxN

          else:
              hashMap[num] = 1



      return  maxN



```

方案2：摩尔投票法
适用于众数大于n/2

```python

    count,majority = 1, nums[0]

    for num in nums[1:]:
        if count == 0:
            majority = num

        elif majority == num:
            count += 1
        else:
            count -= 1

    return  majority

```

方案3.使用Counter计数器

```python


def majorityElement(nums: List[int]) -> int:
    
    return [k for k, v in collections.Counter(nums).items() if v > len(nums) // 2][0]


```


### [4.缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)

``` python

def firstMissingPositive(nums: List[int]) -> int:

    if 1 not in nums:
        return 1

    length = len(nums)
    if length == 1:
        return 2

    nums.sort()
    minN = 1

    for i,num in enumerate(nums):
        if num == minN:
            minN+=1

        if num > minN:
            return minN

    return minN

```


### [5.环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)


```python

class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
def hasCycle(self, head: ListNode) -> bool:
    if head == None or head.next == None:
        return False

    p1 = head.next
    p2 = head.next
    while p1.next:

        p1 = p1.next
        if p1.next == None:
            return False

        p2 = p2.next

        if p2.next == None:
            return  False

        p2 = p2.next
        if p2.next == None:
            return  False

        if p1.val == p2.val:
            return True
            
            
```

### [6.合并k个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)




```python

def merge(lists:[ListNode]):
    length = len(lists)
    if length == 0:
        return None
    elif length == 1:
        return lists[0]
    mid = int(length/2)
    return merge2Lists(merge(lists[mid:]),merge(lists[:mid]))

def merge2Lists(l1:ListNode, l2:ListNode):

    if l1 == None:
        return l2
    if l2 == None:
        return l1

    head = ListNode(-1)
    p = head
    while l1  or l2 :

          if l1 == None:
              p.next = l2
              break

          if l2 == None:
              p.next = l1
              break

          if l1.val < l2.val:
              p.next = ListNode(l1.val)
              l1 = l1.next
          else:
              p.next = ListNode(l2.val)
              l2 = l2.next

          p = p.next

    return head.next

def mergeKLists( lists: List[ListNode]) -> ListNode:
        length = len(lists)
        if lists == None or length == 0:
            return None


        return merge(lists)



def logNode(n:ListNode):
    while n:
        print("n.val===",n.val)
        n = n.next

```


### [7.寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)



```python

    def findMedianSortedArrays(nums1: List[int], nums2: List[int]) -> float:
        m = len(nums1)
        n = len(nums2)
        if m > n:
            m,n,nums1,nums2 = n,m,nums2,nums1
        if n == 0:
            return  ValueError
        imin, imax, half_len = 0, m, int((m + n + 1) / 2)
        while imin <= imax:
            i = int((imin + imax) / 2)
            j = half_len - i
           
            if i < m and nums2[j-1] > nums1[i]:
                # i is too small, must increase it
                imin = i + 1
            elif i > 0 and nums1[i-1] > nums2[j]:
                # i is too big, must decrease it
                imax = i - 1
            else:
                # i is perfect
                if i == 0: max_of_left = nums2[j-1]
                elif j == 0: max_of_left = nums1[i-1]
                else: max_of_left = max(nums1[i-1], nums2[j-1])

                if (m + n) % 2 == 1:
                    return max_of_left

                if i == m: min_of_right = nums2[j]
                elif j == n: min_of_right = nums1[i]
                else: min_of_right = min(nums1[i], nums2[j])

                return (max_of_left + min_of_right) / 2.0



```

### [8.最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)



```python

def longestPalindrome(s: str) -> str:
    print(s)
    if s == None or len(s) == 0 :
        return ""

    start,end = 0,0

    for index,ss in enumerate(s):

        len1 = expandAroundCenter(s,index,index)
        len2 = expandAroundCenter(s,index,index+1)
        length = max(len1,len2)

        if length > end - start:
            start = index - int((length-1)/2)
            end = index + int((length)/2)


    return  s[start:(end+1):1]
    
    
def expandAroundCenter(s:str,left:int,right:int):
     while left >= 0 and right < len(s) and s[left] == s[right]:
         left -=1
         right += 1

     return right - left - 1



```

## 基础
### 数组
实现一个支持动态扩容的数组 

实现一个大小固定的有序数组，支持动态增删改操作

实现两个有序数组合并为一个有序数组
### 链表
实现单链表、循环链表、双向链表，支持增删操作

实现单链表反转 
 
实现两个有序的链表合并为一个有序链表


实现求链表的中间结点