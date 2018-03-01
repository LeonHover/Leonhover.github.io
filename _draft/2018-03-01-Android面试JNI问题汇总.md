JNI所有的Api都可以在jni.h中查找。

- [ ] JNI对Java类操作
    * jclass FindClass(const char* clsName):通过类的名称（全名，'.'替换为'/'）来获取jclass
    * jclass GetObjectClass(jobject obj):通过对象实例来获取jclass，相当于java中的getClass方法
    * jclass GetSuperClass(jclass obj):通过jclass可以获取其父类的jclass对象
- [ ] JNI调用Java的Field操作
    * GetFieldID(jclass clazz,const char* name,const char* sign):获取字段id
    * GetStaticFieldID(jclass clazz,const char* name,const char* sign):获取静态字段的id
    * Get<TYPE>Field:获取类型为TYPE的字段
    * Set<TYPE>Field:设置类型为TYPE的字段的值
    * GetStatic<TYPE>Field:获取类型为TYPE的静态字段
    * SetStatic<TYPE>Field:设置类型为TYPE的静态字段的值
- [ ] JNI调用Java的Method操作
    * GetMethodID(jclass clazz,const char* name,const char* sign):获取方法id
    * GetStaticMethodID(jclass clazz,const char* name,const char* sign):获取静态方法id
    * Call<TYPE>Method(jobject obj,jmethodID,...):调用返回类型为TYPE的方法
    * Call<TYPE>Method(jobject obj,jmethodID id,va_list lst):调用返回类型为TYPE的方法，其中va_list为参数表
    * Call<TYPE>Method(jobject obj,jmethodID id,jvalue* v):同上，但v为jvalue或jvalue数组的指针
    * CallStatic<TYPE>Method(jclass clazz,jmethodID,...):调用返回类型为TYPE的静态方法
    * CallNonvirtual<TYPE>Method(jobject obj,jclass clazz,jmethodID id,...):调用父类的方法

- [ ] sign签名对应表

    |类型|对应签名|
    |:-:|:-:|
    |boolean|Z|
    |byte|B|
    |char|C|
    |short|S|
    |int|I|
    |long|L|
    |float|F|
    |double|D|
    |void|V|
    |object|对象类的完整名称，"Ljava/lang/String"|
    |Array|[类型,[Ljava/lang/String|
    |Method|方法签名，(参数1类型签名，参数2类型签名...)返回值类型签名，"(ID)D"|

- [ ] 动态注册与静态注册
    


- [ ] 

    ```
    GetStringUTFChars(JNIEnv * ,jstring,jboolean*)
    ReleaseStringUTFChars(JNIEnv * ,jstring,const char *)

    GetObjectField GetObjectClass 
    DeleteLocalRef

    GetByteArrayElements
    ReleaseByteArrayElements

    NewGlobalRef
    DeleteGlobalRef

    FindClass
    DeleteLocalRef

    NewString
    NewStringChars
    NewObject
    NewByteArray
    ReleaseStringChars

    ```
    本地代码抛出异常
    ```
    JNIEXPORT void JNICALL
    Java_CatchThrow_doit(JNIEnv *env,jobject obj)
    {
        jthrowable exc;
        jclass cls = (*env)->GetObjectClass(env,obj);
        jmethodID mid = (*env)->GetMethodID(env,cls,"callback","()V");
        if(mid == NULL){
            return;
        }
        (*env)->CallVoidMethod(env,obj,mid);
        exc = (*env)->ExceptionOcurred(env);
        if(exc){
            jclass newExcCls;
            (*env)->ExceptionDescribe(env);
            (*env)->ExceptionClear(env);
            newExcCls = (*env)->FindClass(env,"java/lang/IllegalArgumentException");
            if(newExcCls == NULL){
                return;
            }
            (*env)->ThrowNew(env,newExcCls,"throw from c");
            (*env)->DeleteLocalRef(env,newExcCls);
        }
    }
    ```

    封装一个抛出异常的工具函数
    ```
    void JNU_ThrowByName(JNIEnv *env,const char *name,const char *msg)
    {
        jclass cls = (*env)->FindClass(env,name);
        if(cls != NULL){
            (*env)->ThrowNew(env,cls,msg);
        }

        (*env)->DeleteLocalRef(env,cls);
    }
    ```

    JNI与多个线程导致JNIEnv*发生异常

        JNI中JNIEnv*和jobject不可以直接在多线程共用，但我们可以通过保存JavaVM和全局变量jobject来达到在多线程中共享的目的。
        ```
        JavaVM * gJavaVM;
        jobject gJavaObj;

        //初始化的时候
        (*env)->GetJavaVM(env,&gJavaVM);
        gJavaObject = (*env)->NewGlobalRef(env,obj);

        //在底层的线程中回调Java中的方法——onNativeCallback
        //Jni创建的线程无法使用JNIEnv指针，我们首先是要获得到可用的JNIEnv指针
        JNIEnv *env;
        (*gJavaVM)->AttachCurrentThread(gJavaVM,&env,NULL);

        //获取Java层对应的类
        jclass javaClass = (*env)->GetObjectClass(env,gJavaObj);
        if(javaClass == NULL){
            return 0;
        }

        //获取Java层被回调的函数
        jmethodID javaCallback = (*env)->GetMethodID(env,javaClass,"onNativieCallback","(I)V");
        if(javaCallback == NULL){
            return 0;
        }

        int count = 0;

        //线程循环
        while(!gIsThreadExit){
            //回调Java
            (*env)->CallVoidMethod(env,gJavaObj,javaCallback,count++);
            sleep(1);
        }

        (*gJavaVM)->DetachCurrentThread(gJavaVM);    
        
        ```


