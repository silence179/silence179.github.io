[minegrub theme](https://github.com/Lxtharia/minegrub-theme) 感觉比较有意思的我的世界主题的grub启动 
grub启动会读取/boot/grub/grub.cfg文件这个文件决定了所有的选项  

!!! Note
	这里的/boot实际上是linux系统挂载的efi硬盘分区,所以其实整个grub是从efi分区中启动来逐渐引导linux启动的.而我们又可以在linux里面修改grub的配置然后让grub-makconfig来自动化的读取和写入efi分区.所以真正的.cfg文件不在linux系统中的etc之中,而是由etc的文件构建写入boot中.

grub-mkconfig自动生成,读取/etc/default/grub的设置和/etc/grub.d/* 的所有脚本. 这里的.d文件是将整个cfg文件拆分成很多.d文件再由grub-mkconfig来读取生成cfg. 
这里minegrub主题是由两个主题拼凑出来的,先通过一个doublemenu来引导选择模拟我的世界最开始的主界面.然后选择之后,chosen会被设置,基于chosen被设置也就是被定义了,这个时候我们再次调用加载cfg,但是这次用if判断chosen来规避再次进入主界面,只有chosen没有被设置的时候我们才执行主界面的theme.这样就有两层theme,关键是chosen变量.  如果这里进入死循环,那么很好进live自己把文件修好再写进efi吧.

|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||  

神秘快照系统,出大问题:  
先看一个命令输出:
```bash
 systemctl status grub-btrfsd.service
● grub-btrfsd.service - Regenerate grub-btrfs.cfg
     Loaded: loaded (/usr/lib/systemd/
system/grub-btrfsd.service; enabled; preset: disabled)
    Drop-In: /etc/systemd/system/grub-btrfsd.service.d
             └─override.co
nf
     Active: active (running) since Fri 2026-01-02 01:18:49 CST; 17min ago
 Invocation: f76d5daad04d42dea74ec167079e3d38
   Main PID: 878 (bash)
      Tasks: 3 (limit: 18740)
     Memory: 8.2M (peak: 8.4M)
        CPU: 28ms
     CGroup: /system.slice/grub-btrfsd.service
             ├─ 878 bash /usr/bin/grub-btrfsd --syslog --timeshift-auto
             ├─ 890 bash /usr/bin/grub-btrfsd --syslog --timeshift-auto
             └─1037 inotifywait -q -q -e create -e delete /run/timeshift

1月 02 01:18:50 SilenceArch grub-btrfsd[1036]: Watching /run/timeshift for times
hift to start
```
对于timeshift,我们创建一个快照的时候,grub-btrfsd.service的守护进程是会监视我们的timeshift的目录的,一旦我们的目录发生更改,就会触发这个inotifywait监视,会自动的运行脚本中的一行命令就是grub-mkconfig指令,这会导致什么呢.
似乎没什么毛病,但是一旦我们先运行了timeshift的恢复指令,也就是从一个子卷中恢复,我们此时会进入一个暂时的子卷,timeshift也会提示我们[live]字样,此时@卷被移除,被以前的一个子卷创建子卷覆盖.而我们下次启动的之前做的操作都会被记录在这个live子卷中.但是如果好死不死又创建一个子卷,那么炸了,mkconfigh指令执行,以后会莫名奇妙地启动到一个子卷里面,而且以后的快照都不能正常使用了.所以不能这么做.而且很难意识到这一点,我意识到之后已经是一直在用几个月之前的子卷当我的系统了.    
修了很久,做个记录.  

