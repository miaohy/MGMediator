# MGMediator
遇到问题1
NSInvocation 执行getReturnValue返回值后对象释放了，因为该方法仅仅是从invocation的返回值拷贝到指定内存地址，如果返回值是一个nsobject对象的话，是没有处理自己的内存管理的，而我们在定义returnValue时使用的是__strong类型的指针对象，arc就会假设该内存块已被retain（实际没有），当returnValue出了定义域释放时，导致该crash。  
解决方法如下  
 NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:methodSig];  
          [invocation setArgument:&params atIndex:2];  
          [invocation setSelector:action];  
          [invocation setTarget:target];  
          [invocation invoke];  
          void *result = NULL;  
          [invocation getReturnValue:&result];  
      return (__bridge id)result;  


TestObject没有实现想要调用的方法，这时候可以通过如下两种方式调用，然后通过消息转发的方式在别的类里面实现需要调用的方法  
 TestObject *objec = [[TestObject alloc] init];  
    SEL sel = NSSelectorFromString(@"eatBone");  
    NSMethodSignature *sig = [NSMethodSignature signatureWithObjCTypes:"v@:"];  
    if (sig){  
        NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:sig];  
        [invocation setTarget:objec];  
        [invocation setSelector:sel];  
        [invocation invoke];  
    }  

[objec performSelector:sel withObject:nil]; //会报警告，内存泄漏  
      [objec performSelector:@selector(eatFish) withObject:nil]; //这么写不会有内存泄漏  
