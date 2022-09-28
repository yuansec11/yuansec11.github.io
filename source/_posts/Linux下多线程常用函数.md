---
title: Linux下多线程常用函数
date: 2022-09-28 16:04:30
tags:
- linux
- 多线程
- 嵌入式
categories: Linux
---
## 创建线程
# pthread_create()
头文件：#include <pthread.h>  
定义函数：int pthread_create(pthread_t *tidp, const pthread_attr_t *attr,
( void *)(*start_rtn)( void *), void  *arg); 
<!--more-->  
函数说明：创建一个新的线程。  
第一个参数：存储线程的ID  
第二个参数：设置线程属性，一般情况下设置为NULL  
第三个参数：线程运行代码的起始地址  
第四个参数：运行函数的参数地址，如果需要传入多个参数，则需使用一个包含这些参数的结构体地址  
返回值：成功则返回0, 失败返回非0值。  
# pthread_exit()
头文件：#include <pthread.h>  
定义函数：void pthread_exit(void *_retval);   
函数说明：结束一个线程。  
返回值：  
# pthread_join()
头文件：#include <pthread.h>  
定义函数：void pthread_join(pthread_t ch, void **thread_return);  
函数说明：等待一个线程结束。  
第一个参数：被等待的线程ID  
第二个参数：指向一个保存线程退出状态的完整区域  
返回值：成功则返回0, 失败返回非0值  
# pthread_cancel()
头文件：#include <pthread.h>  
定义函数：int pthread_cancel(pthread_t thread);  
函数说明：取消一个正在执行的线程。  
返回值：成功则返回0, 失败返回非0值  
# pthread_setcancelstate()
头文件：#include <pthread.h>  
定义函数：int pthread_setcancelstate(int state, int *oldstate);  
函数说明：设置当前线程的可取消性状态。  
第一个参数：要设置的新状态值  
第二个参数：存储原来的状态
返回值：
# pthread_setcanceltype()
头文件：#include <pthread.h>  
定义函数：int pthread_setcanceltype(int type, int *oldtype);  
函数说明：设置当前线程的取消类型。  
第一个参数：要设置的新取消类型  
第二个参数：存储原来的状态  
返回值：  
## 线程私有数据
# pthread_key_create()
头文件：#include <pthread.h>  
定义函数：int pthread_key_create(pthread_key_t *key, void (*destructor)(void*));  
函数说明：创建线程私有数据。  
第一个参数：将其地址赋给key供以后访问  
第二个参数：如果不为空，在线程退出时将key所关联的数据为参数调用其指向的资源释放函数，以释放分配的缓冲区  
返回值：  
# pthread_key_delete()
头文件：#include <pthread.h>  
定义函数：int pthread_key_delete(pthread_key_t key);  
函数说明：销毁线程特定数据键。  
返回值：成功返回0.其他任何返回值都表示出了错误  
# pthread_getspecific()
头文件：#include <pthread.h>  
定义函数：void *pthread_getspecific( pthread_key_t key);  
函数说明：将与key 相关联的数据读出来。  
返回值：  
# pthread_setspecific()
头文件：#include <pthread.h>  
定义函数：int pthread_setspecific( pthread_key_t key , const void *value);  
函数说明：将pointer 的值 (不是锁指的内容) 与key 相关联。  
返回值：  
## 互斥锁
# pthread_mutex_init()
头文件：#include <pthread.h>  
定义函数：int pthread_mutex_init(pthread_mutex_t *restrict mutex,const pthread_mutexattr_t *restrict attr);  
函数说明：初始化互斥锁。  
第一个参数：要初始化互斥锁的指针  
第二个参数：指向属性对象的指针，如果为NULL，则使用默认属性  
返回值：成功返回0，失败返回非0  
# pthread_mutex_lock()
头文件：#include <pthread.h>  
定义函数：int pthread_mutex_lock(pthread_mutex_t *mutex);  
函数说明：申请互斥锁。  
返回值：成功返回0，失败返回非0  
# pthread_mutex_unlock()
头文件：#include <pthread.h>  
定义函数：int pthread_mutex_unlock(pthread_mutex_t *mutex);  
函数说明：释放互斥锁。  
返回值：成功返回0，失败返回非0  
# pthread_mutex_trylock()
头文件：#include <pthread.h>  
定义函数：int pthread_mutex_trylock(pthread_mutex_t *mutex);  
函数说明：以非阻塞方式申请互斥锁。  
返回值：成功返回0，失败返回非0  
# pthread_mutex_destroy()
头文件：#include <pthread.h>  
定义函数：int pthread_mutex_destroy(pthread_mutex_t *mutex);  
函数说明：销毁互斥锁。  
返回值：成功返回0，失败返回非0  
## 条件变量
# pthread_cond_init()
头文件：#include <pthread.h>  
定义函数：int pthread_cond_init(pthread_cond_t *cond,const pthread_condattr_t *cattr);  
函数说明：初始化条件变量。  
第一个参数：要初始化条件变量的指针  
第二个参数：指向属性对象的指针，如果为NULL，则使用默认属性  
返回值：成功返回0，失败返回非0  
# pthread_cond_wait()
头文件：#include <pthread.h>  
定义函数：int pthread_cond_init(pthread_cond_t *cond, pthread_condattr_t *cond_attr);  
函数说明：阻塞等待某个条件变量。  
第一个参数：要要等待的条件变量的指针  
第二个参数：指向于条件变量关联的互斥锁指针  
返回值：成功返回0，失败返回非0  
# pthread_cond_signal()
头文件：#include <pthread.h>  
定义函数：int pthread_cond_signal(pthread_cond_t *cond);  
函数说明：唤醒等待出现与条件变量关联的第一个线程，如果没有任何阻塞线程，此函数不起作用。  
返回值：成功返回0，失败返回非0  
# pthread_cond_timedwait()
头文件：#include <pthread.h>  
定义函数：int pthread_cond_timedwait(pthread_cond_t *restrict cond,
           pthread_mutex_t *restrict mutex,
           const struct timespec *restrict abstime);  
函数说明：阻塞等待某个条件变量。  
第一个参数：要要等待的条件变量的指针  
第二个参数：指向于条件变量关联的互斥锁指针  
第三个参数：等待时间  
返回值：成功返回0，失败返回非0  
# pthread_cond_broadcast()
头文件：#include <pthread.h>  
定义函数：int pthread_cond_broadcast(pthread_cond_t *cond);  
函数说明：唤醒等待出现与条件变量关联的所有线程，如果没有任何阻塞线程，此函数不起作用。  
返回值：成功返回0，失败返回非0  
# pthread_cond_destroy()
头文件：#include <pthread.h>  
定义函数：int pthread_cond_destroy(pthread_cond_t *cond);  
函数说明：销毁条件变量。  
返回值：成功返回0，失败返回非0  
## 读写锁
# pthread_rwlock_init()
头文件：#include <pthread.h>  
定义函数：int pthread_rwlock_init(pthread_rwlock_t *restrict rwlock,
const pthread_rwlockattr_t *restrict attr);  
函数说明：初始化读写锁。  
第一个参数：指向要初始化的读写锁的指针  
第二个参数：指向属性的对象  
返回值：成功返回0，失败返回非0  
# pthread_rwlock_rdlock()
头文件：#include <pthread.h>  
定义函数：int pthread_rwlock_rdlock(pthread_rwlock_t *restrict rwlock);  
函数说明：以阻塞的方式申请读锁。  
返回值：成功返回0，失败返回非0  
# pthread_rwlock_tryrdlock()
头文件：#include <pthread.h>  
定义函数：int pthread_rwlock_rdlock(pthread_rwlock_t *restrict rwlock);  
函数说明：以非阻塞的方式申请读锁。  
返回值：成功返回0，失败返回非0  
# pthread_rwlock_wrlock()
头文件：#include <pthread.h>  
定义函数：int pthread_rwlock_wrlock(pthread_rwlock_t *restrict rwlock);  
函数说明：以阻塞的方式申请写锁。  
返回值：成功返回0，失败返回非0  
# pthread_rwlock_trywrlock()
头文件：#include <pthread.h>  
定义函数：int pthread_rwlock_trywrlock(pthread_rwlock_t *restrict rwlock);  
函数说明：以非阻塞的方式申请写锁。  
返回值：成功返回0，失败返回非0  
# pthread_rwlock_unlock()
头文件：#include <pthread.h>  
定义函数：int pthread_rwlock_unlock(pthread_rwlock_t *restrict rwlock);  
函数说明：释放锁。  
1、如果调用此函数来释放锁，但当前还有其他锁定，则保持读锁定状态，如果释放最后一个读锁，则读写锁将处理解锁状态。  
2、如果调用此函数释放锁，则读写锁为解锁状态  
返回值：成功返回0，失败返回非0  
# pthread_rwlock_destroy()
头文件：#include <pthread.h>  
定义函数：int pthread_rwlock_destroy(pthread_rwlock_t *restrict rwlock);  
函数说明：销毁读写锁。  
返回值：成功返回0，失败返回非0  

