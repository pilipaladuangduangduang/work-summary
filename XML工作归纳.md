# XML工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和XML相关的内容：

### 编写CDATA

> CDATA的表示方式：<font color="FF2D2D">&lt;![CDATA[</font> 文本内容 <font color="FF2D2D">]]&gt;</font>


### XML配置文件中URL通过&连接请求参数

 > 今天修改配置文件时，在URL后通过<font color="FF2D2D">&</font>符号连接请求参数后，项目启动就报错，后来发现XML中不能直接使用<font color="FF2D2D">&</font>符号，而是需要通过转义符号&amp ;来连接。





