    Title: 使用CPP扩展Python
    Date: 2013-02-06T00:00:00

基本需求是这样的，当  

  
    const char* p = NULL;
    if(!PyArg_ParseTuple(args, "s", &p))
      return NULL;

      
看python帮助文档，当在PyArg_ParseTuple中使用格式字符串s时，一定要传入指针的地址。
不能为该指针分配存储空间。char str[100]，这样肯定是不行的。
通过该调用，该指针就指向了一个已经存在的对象。  


于是我想，把指针，字符串地址保存起来，需要时，就可以直接输出Python虚拟机内的一个字符串了。
这样不用自己分配内存，管理内存了。
关于引用计数问题，既然是指针指向了一个已经存在的对象，理所当然的认为Python虚拟机会自己搞定引用计数问题。


经过实践  

    
    int i = 0;
    
    static PyObject*
    test_test(PyObject* self, PyObject* args)
    {
      return Py_BuildValue("i", i++);
    }
    
    const char* ptest[10];
    
    static PyObject*
    test_str(PyObject* self, PyObject* args)
    {
      const char* p = NULL;
      if(!PyArg_ParseTuple(args, "s", &p))
        return NULL;
    
      ptest[i] = p;
    
      Py_RETURN_NONE;
    }
    
    static PyObject*
    test_flush(PyObject* self, PyObject* args)
    {
      for(int j = 0; j < 10; ++j){
        printf("%d=%s\n", j, ptest[j]);
      }
      Py_RETURN_NONE;
    }

    static PyMethodDef testMethods[] = {
      {"test",     test_test,       METH_VARARGS, "hello world test"},
      {"str",      test_str,        METH_VARARGS, "hello world test"},
      {"flush",    test_flush,      METH_VARARGS, "hello world test"},
      {NULL, NULL, 0, NULL}
    };
    
    PyMODINIT_FUNC
    inittest(void)
    {
      (void)Py_InitModule("test", testMethods);
    }


上面是cpp代码  

    
    import test
    
    for i in range(10):
        strs = "nihao"+str(i)
        test.str(strs)
        test.test()
    
    test.flush()    


这是测试用的py代码。  


结果证明，偶是错误的。
形式主义害死人啊。  


仔细想想，如果在这种情况下，虚拟机可以自己搞定引用计数问题，
也就是PyArg_ParseTuple(args, "s", &p)这段代码增加虚拟机内字符串对象的引用计数，什么时候削减该对象的引用呢？
如果需要自己手动调用delete的话，在写代码时也不符合成对编程，也就是new/delete,malloc/free成对出现的良好习惯。
忽略strdup这个东东。  


而且文档里说，指针指向已经存在的一个对象，并没有说引用计数的问题。
而且传入一个指针就想增加引用计数，也不合情理。
如果增加引用计数，cpp代码又没有主动调用delete或者free，导致的内存泄漏，是虚拟机的责任，还是自己代码的责任？
如果你是设计者，你会怎么简单有效暴力的解决这个问题？！  


所以告诫自己，这个地方要自己管理内存。  


\=====================================================


补充：


PyArg_ParseTuple中的关于传递object对象的格式字符串是大写o，不是0，也不是小写o。是大写O。 



\=====================================================

同样可想而知，如果在CPP内分配了一段内存，如new char[]，通过buildvalue传递给py时，自己也要记得delete。  