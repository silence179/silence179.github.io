
- 在配置文件里面搜索中文快捷键,或者bind来找到自己的快捷键设置.  
- window-rule 里面设置各种窗口的启动设置.  
- 在output上修改各个显示器的相对位置
- 希望设置浮动窗口可以通过niri msg pick-window来得到app-id来设置
- prefer-no-csd是用于给窗口取消标题栏
- 将layer-rule match namespace "swww-daemonoverview"并且将place-within-backup 设置为true就可以在overview的背景匹配上另一个守护进程的壁纸了
-  matugen  [wiki页面](https://iniox.github.io/#matugen/configuration) pre_hook 和 post_hook的用法是在template后面直接跟就可以了.