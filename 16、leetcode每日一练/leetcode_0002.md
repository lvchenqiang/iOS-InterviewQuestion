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


### 4. [滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/) 


### 5. [爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/) 




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