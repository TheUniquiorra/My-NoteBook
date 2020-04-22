# linux技术总结
## 问题
1. 在运行vuepress打包部署命令时出现：Command "build\r" not found. Did you mean "build",如下
![无法执行脚本](/Linux/无法执行脚本.png)

**原因：** 因为该脚本是在windows下编写的，到linux系统上会有格式的问题

**解决方案：** 编辑器vi/vim打开你需要执行的脚本文件，输入 ：set fileformat=unix 并回车，然后保存退出，重新执行脚本，问题就解决了
