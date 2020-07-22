## 栈、队列和递归

### 1.[有效的括号](https://leetcode-cn.com/problems/valid-parentheses/) 




```python

      mapping = {'(': ')', '{': '}', '[': ']'}
            stack = []
            for ss in s:
             
                if len(stack) > 0 and stack[-1] in mapping  and ss == mapping[stack[-1]]:
                    stack.pop()

                else:
                    stack.append(ss)


            return  len(stack) == 0
            
                  
```



### 2. [最长有效括号](https://leetcode-cn.com/problems/longest-valid-parentheses/) 



``` python

   def longestValidParentheses(self, s: str) -> int:
            stack = []
            stack.append(-1)
            maxans = 0
            for index,ss in enumerate(s):
                if ss == '(':
                    stack.append(index)
                else:
                    stack.pop()
                    if len(stack) == 0:
                        stack.append(index)
                    else:
                        maxans = max(maxans,index - stack[-1])

            return maxans
            
            
```


### 3. [设计循环双端队列 ](https://leetcode-cn.com/problems/design-circular-deque/)



```python3


class MyCircularDeque:
    def __init__(self, k: int):
        """
        Initialize your data structure here. Set the size of the deque to be k.
        """
        self.maxLen = k
        self.queue = []

    def insertFront(self, value: int) -> bool:
        """
        Adds an item at the front of Deque. Return true if the operation is successful.
        """
        if len(self.queue) < self.maxLen:
            self.queue.insert(0,value)
            return  True
        else:
            return  False

    def insertLast(self, value: int) -> bool:
        """
        Adds an item at the rear of Deque. Return true if the operation is successful.
        """
        if len(self.queue) < self.maxLen:
            self.queue.append(value)
            return  True
        else:
            return  False


    def deleteFront(self) -> bool:
        """
        Deletes an item from the front of Deque. Return true if the operation is successful.
        """
        if len(self.queue) == 0:
            return False
        else:
            self.queue.pop(0)
            return True

    def deleteLast(self) -> bool:
        """
        Deletes an item from the rear of Deque. Return true if the operation is successful.
        """
        if len(self.queue) == 0:
                return False
        else:
                self.queue.pop()
                return True
    def getFront(self) -> int:
        """
        Get the front item from the deque.
        """
        if len(self.queue) == 0:
            return -1
        else:
            return self.queue[0]

    def getRear(self) -> int:
        """
        Get the last item from the deque.
        """
        if len(self.queue) == 0:
            return -1
        else:
            return self.queue[-1]


    def isEmpty(self) -> bool:
        """
        Checks whether the circular deque is empty or not.
        """
        return  len(self.queue) == 0
    def isFull(self) -> bool:
        """
        Checks whether the circular deque is full or not.
        """
        return  len(self.queue) == self.maxLen

# Your MyCircularDeque object will be instantiated and called as such:
# obj = MyCircularDeque(k)
# param_1 = obj.insertFront(value)
# param_2 = obj.insertLast(value)
# param_3 = obj.deleteFront()
# param_4 = obj.deleteLast()
# param_5 = obj.getFront()
# param_6 = obj.getRear()
# param_7 = obj.isEmpty()
# param_8 = obj.isFull()


```

### 4. [滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/) 

https://leetcode-cn.com/problems/sliding-window-maximum/solution/hua-dong-chuang-kou-zui-da-zhi-by-leetcode-3/


```
    if len(nums) * k == 0 :
        return  []

    if len(nums) == 1:
        return nums

    n = len(nums)
    left = [0] * n
    left[0] = nums[0]
    right = [0] * n
    right[n-1] = nums[n-1]
    for i in range(1,n):
        if i % k == 0:
            left[i] = nums[i]
        else:
            left[i] = max(left[i-1],nums[i])


        j = n - i - 1
        if (j+1) % k == 0:
            right[j] = nums[j]
        else:
            right[j] = max(right[j+1],nums[j])

    output = []
    for i in range(n - k + 1):
      output.append(max(left[i+k-1],right[i]))


    return  output
    
```

### 5. [爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/) 
https://leetcode-cn.com/problems/climbing-stairs/solution/pa-lou-ti-by-leetcode/



```python3


    
    def climbStairs(self, n: int) -> int:
        if n == 1:
            return 1
        elif n == 2:
            return 2
        else:
          d = [0] * n
          d[1] = 1
          d[2] = 2
          for i in range(3,n):
              d[i] = d[i-1] + d[i-2]

        return d[n-1] + d[n-2]  
        
        
```





## 基础练习

### 栈


用数组实现一个顺序栈

用链表实现一个链式栈

编程模拟实现一个浏览器的前进、后退功能

### 队列

用数组实现一个顺序队列

用链表实现一个链式队列

实现一个循环队列

### 递归


编程实现斐波那契数列求值 f(n)=f(n-1)+f(n-2)

编程实现求阶乘 n!

编程实现一组数据集合的全排列