首先在lab1的基础上可能会出现一些问题,具体体现在 [问题出处](https://blog.csdn.net/ChloeWeever/article/details/144327611?spm=1001.2014.3001.5502)   
   
可以选择包装一次`thread_yield`调用  
```c
void thread_try_yield(void) {
  if (!list_empty(&ready_list) && thread_current() != idle_thread)
    thread_yield();
}
```
[解决方案出处](https://stackoverflow.com/questions/52472084/pintos-userprog-all-tests-fail-is-kernel-vaddr)   
当然也可以选择重新开一个pintos (以下基于一个新的pintos源码)   
[更好的pintos指导](https://pkuflyingpig.gitbook.io/pintos)   

需要在usrprog的目录下去重新构建项目并且将pintos的loader 和 kernel都重新指向这个新的.整个项目基于pintos的文件系统,所以要新建一个filesys.dsk,以下是一个测试   
```bash
$ pintos-mkdisk filesys.dsk --filesys-size=2
$ pintos -p ../../examples/echo -a echo -- -f -q run 'echo PKUOS'
```
但是会发现最后执行是echo PKUOS整个字符串作为一个程序一起执行的,而不是echo,而pintos在这里的第一个任务就是完成整个程序调用的参数分离.   
不过在完成参数分离之前,看接下来的函数:
```c
static void
run_task (char **argv)
{
  const char *task = argv[1];
  
  printf ("Executing '%s':\n", task);
#ifdef USERPROG
  process_wait (process_execute (task));
#else
  run_test (task);
#endif
  printf ("Execution of '%s' complete.\n", task);
}
```
而process_wait是没有被实现的,直接返回-1,整个线程就不会等待应用程序的线程完成,就直接退出,所以在这个时候make check会出现没有任何东西输出的结果.所以要实现这个process_wait函数.   
在此之前要给thread函数维护一个子线程的队列,也就是要加入  
```c
    struct list children_list;
	struct list_elem child_elem;
    struct semaphore being_waited_on;
```
然后在process_wait里面实现这个查询
```c
int
process_wait (tid_t child_tid) 
{
    struct thread *child_thread = NULL;

    struct list_elem *tmp;

    if(list_empty(&thread_current()->children_list)){
        return -1;  //we don't have to wait so return -1
    }

    for(tmp=list_front(&thread_current()->children_list);tmp!=NULL;tmp=tmp->next){
        struct thread *t = list_entry(tmp,struct thread,child_elem);
        if(t->tid==child_tid){
            child_thread = t;
            break;
        }
    }
    if(child_thread==NULL){
        return -1;
    }
    list_remove(&child_thread->child_elem);
    
    sema_down(&child_thread->being_waited_on);

    return child_thread->exit_code;
}
```
这个地方的child_list的入队是在process_execute函数里面做的 
```c
if (tid == TID_ERROR)
    palloc_free_page (fn_copy); 
  else{
    current_tid = tid;
    enum intr_level old_level = intr_disable();
    thread_foreach(*find_tid,NULL);
    list_push_front(&thread_current()->children_list,&matching_thread->child_elem);
    intr_set_level(old_level);
  }
```
然后我们在thread_exit()中将信号量sema_up就完成了wait的逻辑.  
这个时候的执行结果是打印了system call!  
说明子线程调用了系统调用   
![图片](images/输出1.png)
接下来要实现参数的分离.  







```
Thus, when the system call handler syscall_handler() gets control, the system call number is in the 32-bit word at the caller's stack pointer, the first argument is in the 32-bit word at the next higher address, and so on. The caller's stack pointer is accessible to syscall_handler() as the esp member of the struct intr_frame passed to it. (struct intr_frame is on the kernel stack.)
```
根据这段话,我们知道调用system call的时候会将系统调用号放在esp的低32位,而第一个参数放在了高32位上面.pintos要求我们对于传入的指针做判断,要求判断是否是用户空间内的指针,即高于PHYS_BASE还有是否是一个unmap的或者是一个空的.  
我们要实现一个check_pointer函数:
```c

```