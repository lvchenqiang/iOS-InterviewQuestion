## 排序和二分查找

###   [x 的平方根](https://leetcode-cn.com/problems/sqrtx/)


https://leetcode-cn.com/problems/sqrtx/solution/x-de-ping-fang-gen-by-leetcode-solution/


```python3
/// 二分法求平方根
def mySqrt(x: int) -> int:

    if x < 0:
        return -1
    elif x == 0:
        return 0
    else:
        l, r, ans = 0, x, -1
        
        while l <= r:
            mid =  int((r+l)/2)
            print(l, r,mid)
            if mid*mid <= x:
                ans = mid
                l = mid + 1
            else:
                r = mid - 1

        return  ans 
        
        
        
    /// 收敛式二分查找法    
def mySqrt(x: int) -> int:
    c,x0 = float(x),float(x)
    while 1:
        x1 = 0.5 * (x0+c/x0)
        if abs(x0 - x1) < 1e-7:
            break
        else:
          x0 = x1


    return int(x0)
    
    
```

## 基础

### 排序

编程实现 O(n) 时间复杂度内找到一组数据的第 K 大元素


### 二分查找

实现一个有序数组的二分查找算法

实现模糊二分查找算法（比如大于等于给定值的第一个元素）