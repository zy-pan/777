## Pull request

![PR2](https://github.com/zy-pan/hello-world/blob/master/PR2.png)

![PR1](https://github.com/zy-pan/hello-world/blob/master/PR1.png)

## Make

![make-durationtest](https://github.com/zy-pan/hello-world/blob/master/make-durationtest.png)

## Test

修改后运行结果：

![run-duration](https://github.com/zy-pan/hello-world/blob/master/run-duration.png)

想利用steady_clock测试不同大小容量的vector求和的耗时

## Questions & Solutions

1. 安装虚拟机

   问题：在第一次新建虚拟机时选择了典型（推荐），但是第一次进度条卡死在了copying file步骤，第二次虽然成功安装了虚拟机但是耗时4个小时才安装成功，且第二次开启虚拟机虚拟机就打不开了。

   <img src="https://github.com/zy-pan/hello-world/blob/master/典型.png" alt="典型" style="zoom:50%;" />

   解决：改用自定义（高级）安装，一个小时不到就安装好了。

2. 虚拟机屏幕不适应

   问题：安装后虚拟机在页面内显示的屏幕很小，且点击 查看——自动调整大小——自动适应窗口 按钮为灰。

   解决：安装VMware Tools后，虚拟机可以填满屏幕框了。

## Learning & Feeling

1. 按照nebula手册中的步骤一步步安装，在make中停留了一晚上，第二天继续做，但是发现后续报错，重新make一次后很顺利，直至welcome to nebula

2. 第一次接触vim编辑器，学习了基本操作：按i插入，按esc后：wq保存并退出

3. 学习了怎样进行PR：由 fork——clone——建立链接——新建分支——修改——提交

   与此同时学习了git命令：git clone，git remote -v，git checkout -b my_branch，git status，git add .，git commit，git push等

总结：一步步看着教程，遇到问题想办法解决，磕磕绊绊最后改动了下时钟测试的cpp文件提交PR。

