# Vue技术总结
## 常见问题
### Tooltip 文字提示无法正常显示
* 在日常开发中会存在某个按钮上会放置一些提示的信息，如下：

![An image](/vue/tooltip.PNG)

但是在一些情况下可能提示信息不会显示出来，我遇到这种情况的是这个按钮会随着一定的状态显示或者隐藏，在\<el-button\>中会使用v-if进行判断：

``` html
<el-tooltip content="签到" placement="bottom" effect="light">
   <el-button
   type="warning"
   icon="el-icon-chat-dot-round"
   @click="openNotice()"
   style="margin-left:20px"
   circle
   ></el-button>
</el-tooltip>
```

此时在界面中无论我们鼠标点在哪里tooltip都不会显示出来，遇到这种情况需要将v-if的判断写在\<el-tooltip\>中，而不是在buttoon中，调整之后的代码如下

``` html
 <el-tooltip content="签到" placement="bottom" v-if="this.liveStatus == '直播中'">
   <el-button
    class="m-button"
    type="primary"
    icon="el-icon-s-claim"
    @click="singin()"
    style="margin-left:20px
    circle
    ></el-button>
 </el-tooltip>

```

这样就能解决的了我们的问题了。
