### NSOperation 相关

#### NSOperation使用优势

![](./img/Snip20190306_27.png)

#### 任务的执行状态

![](./img/Snip20190306_28.png)

#### 状态控制

![](./img/Snip20190306_29.png)

![](./img/Snip20190306_30.png)


#### 参照源码 gnustep-base_1.26.0

##### start
```

- (void) start
{
  NSAutoreleasePool	*pool = [NSAutoreleasePool new];
  double		prio = [NSThread  threadPriority];

  AUTORELEASE(RETAIN(self));	// Make sure we exist while running.
  [internal->lock lock];
  NS_DURING
    {
      if (YES == [self isExecuting])
	{
	  [NSException raise: NSInvalidArgumentException
		      format: @"[%@-%@] called on executing operation",
	    NSStringFromClass([self class]), NSStringFromSelector(_cmd)];
	}
      if (YES == [self isFinished])
	{
	  [NSException raise: NSInvalidArgumentException
		      format: @"[%@-%@] called on finished operation",
	    NSStringFromClass([self class]), NSStringFromSelector(_cmd)];
	}
      if (NO == [self isReady])
	{
	  [NSException raise: NSInvalidArgumentException
		      format: @"[%@-%@] called on operation which is not ready",
	    NSStringFromClass([self class]), NSStringFromSelector(_cmd)];
	}
      if (NO == internal->executing)
	{
	  [self willChangeValueForKey: @"isExecuting"];
	  internal->executing = YES;
	  [self didChangeValueForKey: @"isExecuting"];
	}
    }
  NS_HANDLER
    {
      [internal->lock unlock];
      [localException raise];
    }
  NS_ENDHANDLER
  [internal->lock unlock];

  NS_DURING
    {
      if (NO == [self isCancelled])
	{
	  [NSThread setThreadPriority: internal->threadPriority];
	  [self main];
	}
    }
  NS_HANDLER
    {
      [NSThread setThreadPriority:  prio];
      [localException raise];
    }
  NS_ENDHANDLER;

  [self _finish];
  [pool release];
}
```


##### main

```
- (void) main;
{
  return;	// OSX default implementation does nothing
}
```