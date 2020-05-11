## 数组和链表
### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。


```python3
    def twoSum(self, nums: List[int], target: int) -> List[int]:
         hashmap = {}
         for i,num in enumerate(nums):
             if hashmap.get(target - num) is not None:
                 return [i,hashmap.get(target - num)]
             hashmap[num] = i  
```

### [2.三数之和](https://leetcode-cn.com/problems/3sum/)
> 给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组


```python3

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



### [4.缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)



### [5.环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)



### [6.合并k个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)