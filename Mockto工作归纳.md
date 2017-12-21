# Mockto工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Mockto相关的内容：

``` java
// 实际代码
Mockito.doNothing().when(Mockito.spy(new ServiceImpl())).doSth(service);
// 示例样板
Mockito.doNothing().when(Mockito.spy(<调用方法的对象>)).<被调用的方法>(<方法中的参数>);
```




