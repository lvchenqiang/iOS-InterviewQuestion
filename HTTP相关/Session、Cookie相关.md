### Session、Cookie相关

**HTTP协议无状态的补偿**

#### Cookie

Cookie主要是用来记录用户状态,区分用户; **状态保存在客户端**

![](./img/Snip20190310_95.png)

客户端发送的Cookie在http请求报文的Cookie首部字段中

服务器设置Http响应报文的Set-Cookie首部字段

##### 修改Cookie

 ![](./img/Snip20190310_96.png)
 
##### 删除Cookie
 
  ![](./img/Snip20190310_97.png)
  
##### 保证Cookie安全

![](./img/Snip20190310_98.png)

#### Session

  Cookie也是用来记录用户状态,区分用户; **状态保存在服务器端**
  
  
#####  Session的工作流程
  
![](./img/Snip20190310_99.png)
