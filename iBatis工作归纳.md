# iBatis工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中使用iBatis的一些心得：

### iBatis返回Map的小BUG

 > 今天第一次用iBatis查询Map结构的返回结果，结果出错了，错误原因说是java.util.Map不能实例化，然后我把resultClass改成<font color="FF2D2D">java.util.HashMap</font>就OK了。