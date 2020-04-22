# Emacs总结

## Emacs 使用diff
  在Windows下因为没有安装diff相关的包，所以需要自在Windows中安装所需要的diff软件包，本次使用的是KDiff3，具体安装和配置的过程如下：

**下载安装KDiff3**

- [KDiff3官网下载](https://sourceforge.net/projects/kdiff3/files/kdiff3/)

- 安装对应版本的KDiff3

- 配置PATH
在我的电脑图标上右键点击->选择“属性”->选择“高级系统设置”->选择"高级"->选择“环境变量”

选择“新建”，在弹窗中新建一个变量，变量名可以输入“KDIFF3_HOME”,变量值可以选择刚才安装的路径下的bin目录。

- 验证安装是否成功
可以选择cmd中任意路径下输入diff，如果出现以下情况，证明安装是没有问题的。

![An image](/emacs/cmddiff.png)

**Emacs 使用diff**

- ediff-buffers

使用M-x,输入ediff-buffers，然后选择两个已经打开的buffer进行对比

- ediff-files


使用M-x,输入ediff-files,然后可以选择两个文件进行对比

**常用的快捷建**

::: warning
  在进行对比的时候如果点击的文本内容需要在重新点击一下Ediff Control Panel 这个buffer，否则快捷键会不生效。
:::

| 快捷键 | 说明 |
| -----  | ---  |
| n/p |   下一个差异/上一个差异  |
| v/V | 所有的buffer同步向下/向上移动 |
|</>| 所有的buffer同步向左/右移动|
| \| |切换buffer布局方式，竖直或者水平显示buffer|
|a/b|把buffer中的从a拷到b/从b拷到a|
|A/B|切换Buffer-A 或 Buffer-B 的只读状态|
|r-a / r-b|恢复 Buffer-A 或 Buffer-B 差异区域中的被修改的内容|
|g-a / g-b|根据光标在缓冲区中的位置，设置一个离它们最近的差异区域为当前活动区域|
|C-l|恢复先前的所有缓冲区比较的高亮差异区。|
|!|重新比较并高亮差异区域|
|w-a /w-b|保存 Buffer-A 或 Buffer-B 到磁盘|
|E|打开 Ediff 文档|
|z|关闭 ediff control buffer, 只是挂起，可在以后恢复 ediff 状态|
|q|关闭 ediff control buffer， 并退出 ediff|

## Emacs company自动补全忽略大小写

### 解决方法
> (setq company-dabbrev-downcase nil)

