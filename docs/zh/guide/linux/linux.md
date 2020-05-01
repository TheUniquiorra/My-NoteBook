# linux技术总结
## 问题
1. 在运行vuepress打包部署命令时出现：Command "build\r" not found. Did you mean "build",如下
![无法执行脚本](/Linux/无法执行脚本.png)

**原因：** 因为该脚本是在windows下编写的，到linux系统上会有格式的问题

**解决方案：** 编辑器vi/vim打开你需要执行的脚本文件，输入 ：set fileformat=unix 并回车，然后保存退出，重新执行脚本，问题就解决了

## Linux环境中~/.profile,/etc/profile,~/.bashrc的区别
  * ~/.profile 作用的范围是当前用户下的单个shell窗口，只会在一个窗口中生效
  * /etc/profile 作用的范围是系统全局变量设置
  * ~/.bashrc 作用的范围是当前用户，每打开一个shell窗口就会执行一次
