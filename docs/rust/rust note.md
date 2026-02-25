## 对于option和result的理解:
刚开始写rust对于option和result的处理感觉非常别扭,option的出现迫使程序必须进行None的检查,一旦返回Some就一定可以被调用,消除了NULL的危险,但是又保留了这种error的回传,在上层函数中处理.而result是对于函数运行结果的强制处理包装,其实同样是为了避免NULL的出现,同时可以将错误信息包装在err中传递给上层函数处理.  
### 可以用?,unwrap(),expect()来解开包装.
 - ?是一种快捷的做法,对于option和result都可以使用?来取出里面的内容,但是?要匹配函数的返回值,返回值是option就不能对result做?,但是这个时候可以对result使用ok()函数来取出option来,对于ok()?,但是这里会丢失错误信息(如果完全不在乎错误信息这样非常便捷).
 - unwrap相当于assert,expect相当于assert加panic("str"),这样简单粗暴,用在基本不会出错,一出错就比较严重的地方比如内存分配.