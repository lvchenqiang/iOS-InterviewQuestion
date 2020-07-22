#  Runtime
## 一. 类结构
### 1.1 基本数据结构
> 64位下Class类结构

```ObjectiveC
struct objc_object {
    //isa指针
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;
};

struct objc_class : objc_object {
    Class superclass;    //父类指针
    cache_t cache;       //缓存          
    class_data_bits_t bits;  //掩码
    ...
};

//用于获取类信息
struct class_data_bits_t {
    uintptr_t bits;
    class_rw_t* data() {
        return (class_rw_t *)(bits & FAST_DATA_MASK);
    }
    ...
};

// rw --> read/write
struct class_rw_t {
    const class_ro_t *ro;        //类的只读原始信息
    method_array_t methods;      //方法列表(二维数组)
    property_array_t properties; //属性列表(二维数组)
    protocol_array_t protocols;  //协议列表(二维数组)
    ...
}

// ro --> readOnly
struct class_ro_t {
    uint32_t instanceSize;  //实例大小
    const char * name;      //类名

    method_list_t * baseMethodList;     //原始方法列表
    property_list_t *baseProperties;    //属性列表
    protocol_list_t * baseProtocols;    //原始协议列表
    const ivar_list_t * ivars;          //成员变量
    ...
};

```

### 1.2 Q & A
```bash
Q: id 和 NSObject 实例对象的区别?
A:  1. id 是指向Class的指针,是Objective-C 对象，但是并不一定是NSObject对象,id也可以用来指向NSProxy
    2. id你可以调用任意可见的selector，编译器和IDE不会进行类型检查,NSObject会进行编译检查

Q: id 和 instancetype的区别?
A:  1. id 有一个相关返回类型,不过只有方法名以 alloc,init,new
    2. instancetype 与 id 不一样, instancetype 只能在方法声明中作为返回类型使用。
    3. instancetype只是一个关键字,而id是一个指向对象结构体的指针
    4. instancetype 主要的目的是为了帮助编译器更了解你的代码，提早在编译阶段就发现问题
    
Q: self 和super的区别?
A: 1. self 是类的隐藏参数，指向当前调用方法的这个类的实例。而 super 是一个 Magic Keyword，它本质是一个编译器标示符，和 self 是指向的同一个消息接受者。
   2. 当调用[super class]时，会转换成objc_msgSendSuper函数。第一步先构造objc_super结构体，结构体第一个成员就是self。
   3. 第二个成员是(id)class_getSuperclass(objc_getClass(“Son”)), 
   4. 实际该函数输出结果为Father。第二步是去Father这个类里去找- (Class)class，没有，然后去NSObject类去找，
   5. 找到了。最后内部是使用objc_msgSend(objc_super->receiver, @selector(class))去调用，此时已经和[self class]调用相同了，故上述输出结果仍然返回Son。
当发送 class 消息 时不管是 self  还是 super 其消息主体依然是  self ,也就是说 self 和 super 指向的 是同一个对象。只是 查找方法的位置 区别，一个从本类，一个从本类的超类。
super 就是个障眼法，编译器符号， 它可以替换成 [slef class],只不过 方法是从 self 的超类开始 寻找。


Q: SEL,IMP,Method三者的关系与联系
A: SEL是一个运行时代表方法的C类型字符串,在类加载到内存的时候会被注册;
   IMP是一个函数指针,指向函数的具体实现代码,标准的C方法调用;
   Method是一个结构体指针,包含SEL,IMP;
   在运行时，类（Class）维护了一个消息分发列表来解决消息的正确发送。每一个消息列表的 入口是一个方法（Method），这个方法映射了一对键值对，其中键值是这个方法的名字 selector（SEL），值是指向这个方法实现的函数指针 implementation（IMP）

```


## 二.消息机制
OC里的方法调用都会被翻译成`objc_msgSend`、`objc_msgSend_stret`、`objc_msgSendSuper` 和 `objc_msgSendSuper_stret`。 给对象父类发送消息会使用 `objc_msgSendSuper` 有数据结构作为返回值的方法会使用 `objc_msgSendSuper_stret` 或 `objc_msgSend_stret` 其它的消息都是使用 `objc_msgSend` 发送的.
> 上面这些方法都使用汇编来实现: 
> 1). 高效 
> 2). 满足方法调用约定,不同参数,不同返回值的方法用同一个方法实现,只能使用底层的汇编

### 2.1 消息发送
> 消息接收者receiver 通过isa指针找到receiveClass
> 消息接收者receiver 通过superclass指针找到superClass
> 对象方法缓存列表采用散列表来存储,key为selector, value为IMP,当散列表扩容或者有方法交换时,方法缓存清空
> 调用 `_objc_msgForward`会忽略消息查找阶段直接走到动态方法解析阶段, `_objc_msgForward`是一个IMP函数指针

2.1.1 判断消息接收者是否为nil,为nil结束; 否则下一步
2.1.2 从消息接收者的方法缓存列表中查找是否有此方法,查找到了,调用方法结束; 否则下一步
2.1.3 在消息接收者的类对象的方法列表中查找方法,如果找到,则调用方法,并将方法添加到缓存列表,消息发送结束; 否则下一步
2.1.4 沿着消息接收者的继承体系,如果在父类的方法列表缓存中找到,则调用方法结束;否则下一步
2.1.5  在父类的方法列表中查找,如果找到了,则方法调用,并加入消息接收者的缓存,消息发送结束;
2.1.6 在类的继承体系中如果都没有找到,则调用调用 `_objc_msgForward` 进行动态方法解析流程

### 2.2 动态方法解析
> 动态解析过后，会重新走“消息发送”的流程,“从receiverClass的cache中查找方法”这一步开始执行
> 一般用来为 @dynamic 修饰的变量,动态添加getter & setter

2.2.1 判断否曾经是否动态解析过对应的selector,如果解析过,则进行动态方法调用; 否则下一步
2.2.2 调用 `+ (BOOL)resolveInstanceMethod:` 或者 `+ (BOOL)resolveClassMethod:`方法
来动态解析方法,如果有动态添加的方法,则标记为动态解析过,流程结束;否则进入消息转发流程

### 2.3 消息转发 
> TypeEncode

2.3.1 调用 `+ (id)forwardingTargetForSelector:` 方法,如果有返回要转发的对象,则此方法交接target来进行消息发送流程; 否则下一步;
2.3.2 调用 `- (NSMethodSignature *)methodSignatureForSelector:`,如果没有返回方法签名,则系统调用 `doesNotRecognizeSelector`方法,程序崩溃; 否则进行下一步;
2.3.3 调用 `- (void)forwardInvocation:` 来执行自定义逻辑


## 三.Runtime应用
### 3.1 KVO原理
> 本质: 利用RuntimeAPI动态生成一个子类，并且让instance对象的isa指向这个全新的子类,当修改instance对象的属性时，会调用Foundation的`_NSSetXXXValueAndNotify`函数,`willChangeValueForKey:` -> 父类原来的setter -> `didChangeValueForKey:`,内部会触发监听器（Oberser）的监听方法`observeValueForKeyPath:ofObject:change:context:`

3.1.1 判断被监听的对象的keyPath是否有对应的set方法,没有直接报错
3.1.2 判断是否已经生成监听对象类的子类,如果没有生成进行下一步,如果有生成,跳过
3.1.3 生成监听对象类的子类,重写class方法来掩盖真实的类,为子类添加对应的set方法,向系统注册此类
3.1.4 交换子类与父类的isa指针
3.1.5 保存上下文等

### 3.2 KVC原理
> KVC可以触发KVO监听

3.2.1 调用`setValue:forKey:`或者 `setValue:forKeyPath:`
3.2.2 按照`setKey:`和`_setKey`顺序查找方法,找到直接调用,找不到下一步
3.2.3 查看`+ (BOOL)accessInstanceVariablesDirectly`,如果返回NO,则调用 `- (void)setValue:forUndefinedKey:`方法抛出`NSUndefinedKeyException`异常,否则进行下一步
3.2.4 按照 `_key`,`_isKey`,`key`,`isKey`顺序查找成员变量,如果找到了则直接进行赋值,没有找到,则调用 `- (void)setValue:forUndefinedKey:`方法抛出`NSUndefinedKeyException`异常

### 3.3 Category实现原理
> Category编译之后的底层结构是struct category_t，里面存储着分类的对象方法、类方法、属性、协议信息.
> 编译后会按命名规则生成不同的结构体变量,所以分类的名称不能相同,分类里的方法,属性,协议不会覆盖类里的,只是位置在列表前面,所以调用的时候会先找到,最后参与编译的分类会放到最前面.
> 在程序运行的时候，runtime会将Category的数据，合并到类信息中（类对象、元类对象中）


```ObjectiveC
//分类结构体
struct category_t {
    const char *name; //分类名称
    classref_t cls;   // 分类所属类
    struct method_list_t *instanceMethods; //实例方法
    struct method_list_t *classMethods;    //类方法
    struct protocol_list_t *protocols;     //协议列表
    struct property_list_t *instanceProperties; //实例属性列表
    // Fields below this point are not always present on disk.
    struct property_list_t *_classProperties; //类属性
    ...
};

//分类加载过程
//1. void _read_images(header_info **hList, uint32_t hCount, int totalClasses, int unoptimizedTotalClasses)

//2. _getObjc2CategoryList

//3. addUnattachedCategoryForClass

//4. static void remethodizeClass(Class cls)

//5. static voidattachCategories(Class cls, category_list *cats, bool flush_caches)

while(i--){
    //6. class_rw_t类型的结构体的方法,属性,协议调用
    void attachLists(List* const * addedLists, uint32_t addedCount) 
    
    //6.1 申请要附加多大的空间 realloc
    //6.2 原来的位置向后移动
    //6.3 将要附加的内容拷贝到前面空出来的位置

}
```

3.3.1 Q & A

```bash
Q: Category和Class Extension的区别是什么?
A: Class Extension在编译的时候，它的数据就已经包含在类信息中
   Category是在运行时，才会将数据合并到类信息中
   
Q: Category能否添加成员变量?
A: 不能,Category的本质是category_t结构体,结构体里没有存储成员变量的成员,但是可以通过关联对象的手段来间接实现添加成员变量
```

### 3.4 对比`+ load` & `+ initalize`
|  | 调用时刻 | 调用方式 | 调用顺序 |
| :-: | :-: | :-: | :-: |
| + load | runtime加载类和分类的时候调用,只会调用一次 | 直接找到方法实现地址进行方法调用  IMP | 1.递归调用类的(先编译的先调用)2.如果有父类先调用父类的 3.分类按照编译顺序,先编译的先调用 |
| + initialize | 类第一次接收到消息时调用,(父类的可能会被调用多次) | 发送消息objc_msgSend | 1.如果分类有实现,则按照分类的编译顺序,只调用最后编译的一个分类的; 2.如果子类没有实现,则会调用父类的 |


> load伪代码

```js
//1. 按编译顺序先加载类,再加载分类 存入数组

//1.0 从Mach-O加载到没有映射的类
var unremappedClasses = _getObjc2NonlazyClassList(mhdr, &count);
//1.1 重新映射类并且递归
var classList = {
    schedule_class_load(remapClass(unremappedClasses[i])));
    function schedule_class_load(class clz){
        schedule_class_load(clz->superClass);
        insertInto(classList,clz);
    }
};

//1.2 加载分类
var unremappedCats = _getObjc2NonlazyCategoryList(mhdr, &count);
var catList = {
    for(unremappedCats){
        var cat = remapClass(unremappedCats[i]);
        insertInto(catList,cat);
    }
}

//2. 调用类的load方法
for(classList){
    load_method_t load_method = classList[i].method
    //直接通过函数指针调用类的load方法
    (*load_method)(cls, SEL_load);
}

//3. 调用分类的load方法
for(catList){
    var cat = catList[i];
    load_method_t load_method = (load_method_t)cat.method;
    cls = _category_getClass(cat);
    //直接通过函数指针调用类的load方法
    (*load_method)(cls, SEL_load);
}
```

> initialize伪代码

```js
//1. 方法调用肯定会先通过isa查找方法,所以从`class_getInstanceMethod`入手跟踪

//2. lookUpImpOrNil

//3 .lookUpImpOrForward

//4. _class_initialize

//5. callInitialize(cls);
((void(*)(Class, SEL))objc_msgSend)(cls, SEL_initialize);
```

### 3.5 Runtime应用场景

#### 3.5.0 关联对象
> 为分类添加属性
并不是存储在类信息中,而是在一个全局的AssociationManager中,以对象的地址为key,在被关联的对象dealloc时释放.

```ObjectiveC
//为对象添加关联对象
OBJC_EXPORT void
objc_setAssociatedObject(id _Nonnull object, const void * _Nonnull key,
id _Nullable value, 
objc_AssociationPolicy policy);

//获取对象的关联对象
OBJC_EXPORT id _Nullable
objc_getAssociatedObject(id _Nonnull object, 
const void * _Nonnull key);
    
//删除对象所有关联的对象
OBJC_EXPORT void
objc_removeAssociatedObjects(id _Nonnull object);
                         
```

#### 3.5.1 字典模型转换
> eg: YYModel, MJExtension

#### 3.5.2 方法交换,Hook,AOP
> eg: Aspect,打点
> Aspect: 所有被hook的方法都转发到 `_objc_msgForward`或者 `_objc_msgForward_stret`, 并且交换 `forwardInvocation` 到 `__ASPECTS_ARE_BEING_CALLED__`

#### 3.5.3 JSPatch
JS传递字符串给OC，OC通过 Runtime 接口调用和替换OC方法
> `_objc_msgForward`
> `class_replaceMethod`
> `class_addMethod`
> `SEL selector = NSSelectorFromString`


