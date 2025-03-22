# Base 64 编码

## ASCLL 编码

是以 七位二进制 为一组进行编码，共有 128 种组合

![image-20240130143930025](http://images.xiaohai-hx.cn/复习笔记/面试题/image-20240130143930025.png)



## Base 64 编码

以 六位二进制 为一组进行编码，共有 64 种组合

![image-20240130144014921](http://images.xiaohai-hx.cn/复习笔记/面试题/image-20240130144014921.png)

比如要对“Hello World” 进行编码，现将每一个字母转换为二进制，然后以六位一组进行分割，最后一组不足六位补0，然后转为十进制，再查看 Base64 编码表进行转换，最后的结果长度如果不足 4 的倍数，则需要使用等号进行补充



### 其他类似编码

![image-20240130144658683](http://images.xiaohai-hx.cn/复习笔记/面试题/image-20240130144658683.png)



### 使用场景

![image-20240130144854183](http://images.xiaohai-hx.cn/复习笔记/面试题/image-20240130144854183.png)



> [参考视频](【五分钟彻底掌握Base64编码】 https://www.bilibili.com/video/BV1hk4y1S7PJ/?share_source=copy_web&vd_source=534cd597c2fb4b381da3354013fd5304)