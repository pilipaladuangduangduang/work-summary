# Javascript工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和javascript相关的内容：

### js中String类型不能用于new Date()

 > 今天将数据库中的时间戳在js中直接用于new Date(timestamp)，报Invalid Date错误；结果改为new Date(parseInt(timestamp))就OK了，看来String类型不能用于new Date()。
 
### js中定义函数，并且传入多个实参

``` 
// js字符串中定义函数，并传入多个参数，单引号和双引号的使用
func('" + param1 + "','" + param2 + "')
``` 
 
### 将JSON字符串作为请求参数提交到后端

提交请求时将普通JSON传到后台，如果JSON中包含双引号就会丢失内容，所以我们需要将其中的双引号改成单引号，这样子就没有问题了。

```
var json = [{...},{}...];
// 将json中的双引号改成单引号
JSON.stringify(json).replace(/\"/g,"'")
```

### javascript中encodeURI和encodeURIComponent区别

```
var uri="http://isgod.niezhic.xyz?param1=xxx&param2=yyy"; 
// 结果输出：http://isgod.niezhic.xyz?param1=xxx&param2=yyy
console.log(encodeURI(uri));
// 结果输出：http%3A%2F%2Fisgod.niezhic.xyz%3Fparam1%3Dxxx%26param2%3Dyyy
console.log(encodeURIComponent(uri));
```

> 结论：encodeURI不会对保留字符进行encode，比如冒号、斜杠、问号、&符号，而encodeURIComponent会对保留字符进行encode

### js格式化播放时长，从秒到时分秒

``` javascript
function formatSeconds(value, row) {
    var theTime = parseInt(value);// 秒
    var theTime1 = 0;// 分
    var theTime2 = 0;// 小时
    if(theTime > 60) {
        theTime1 = parseInt(theTime/60);
        theTime = parseInt(theTime%60);
            if(theTime1 > 60) {
            theTime2 = parseInt(theTime1/60);
            theTime1 = parseInt(theTime1%60);
            }
    }
    var result = ""+parseInt(theTime)+"秒";
    if(theTime1 > 0) {
        result = ""+parseInt(theTime1)+"分"+result;
    }
    if(theTime2 > 0) {
        result = ""+parseInt(theTime2)+"小时"+result;
    }
    return result;
}
```