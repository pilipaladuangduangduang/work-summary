# jQuery工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和jQuery相关的内容：

### 页面中如何设置只读

> jQuery添加只读属性：$("#id").attr("readonly",true)；HTML中的input属性只读：readonly="readonly"；SpringMVC中form:input添加只读属性：readonly="true"。

### jQuery将form表单转换成json传给后端

``` 
// 1、首先定义转换的方法
$.fn.serializeObject = function() {    
   var o = {};    
   var a = this.serializeArray();    
   $.each(a, function() {    
       if (o[this.name]) {    
           if (!o[this.name].push) {    
               o[this.name] = [o[this.name]];    
           }    
           o[this.name].push(this.value || '');    
       } else {    
           o[this.name] = this.value || '';    
       }    
   });    
   return o;    
}; 
// 2、表单对象调用该方法，并转换成json
var data = $("#form").serializeObject();
var json = JSON.stringify(data);
``` 

### jQuery将时间戳转换为自定义样式的时间

``` js
function formatDateTime(value) {
	//获取一个时间对象，注意：如果是uinx时间戳记得乘于1000。比如php函数time()获得的时间戳就要乘于1000
	var date = new Date(value);
	Y = date.getFullYear() + '-';
	M = (date.getMonth()+1 < 10 ? '0'+(date.getMonth()+1) : date.getMonth()+1) + '-';
	D = (date.getDate() < 10 ? '0'+(date.getDate()) : date.getDate()) + ' ';
	h = (date.getHours() < 10 ? '0'+(date.getHours()) : date.getHours()) + ':';
	m = (date.getMinutes() < 10 ? '0'+(date.getMinutes()) : date.getMinutes());
	s = (date.getSeconds() < 10 ? '0'+(date.getSeconds()) : date.getSeconds()); 
	return (Y+M+D+h+m);
}
``` 

### jQuery中ajax获取success方法的返回值

 > 一般情况下，ajax的success方法中return返回值时是无效的，加上<font color="FF2D2D">async=false</font>属性使得ajax变成同步就可以获取到ajax的返回值；
 
### jQuery中将JSON字符串转为对象

 >  jQuery.parseJSON(json字符串)，将字符串转换成对象，就可以操纵对象中的属性了。

### jQuery判断文件是否存在

``` js
var file = $('#file').val();
if(file === '') {// 判断文件是否为空
    alert ("附件不存在，请选择附件！");
    return false;
}
```

### jQuery循环后端List数据

``` jQuery
success: function(data){
    if(data !== null) {
        jQuery.each(data, function(i,item){ 
            // i 表示每个元素对应的索引： 0、1、2...
            // item 表示当前遍历的元素，可以操作元素中的属性
            arr.push(item.id)    
        });  
    }
}
```

### jQuery判断元素是否存在于数组中

``` jQuery
// 存在返回元素在数组中的索引值，不存在则返回 -1
$.inArray(value, arr);
```

### jQuery中ajax的URL上追加请求参数导致中文乱码

 > 前端往后端ajax传输数据的时候，请求参数和数据追加在URL上时，中文在后端会出现乱码现象，不过放请求数据放在ajax中的data中就不会出现乱码了~
 
### jQuery中异步提交不同形式的请求：

``` jQuery
// jQuery中支持$.get和$.post发送GET和POST异步请求
$.get (url, function (data){ //对返回数据的处理});
$.post (url, function (data){ //对返回数据的处理});

// 如果想要发送DELETE或PUT异步请求，需要这样
$.ajax({
    url: '',
    type: 'DELETE或PUT',
    success: function(data) {
        //对返回数据的处理
    }
});
``` 

### jQuery stringify函数

``` 
// 将后端传回的Object对象转换为json字符串，十分的方便
console.log(JSON.stringify(data));
``` 

### swiper3.x预览轮播图功能

首先上swiper官方：http://www.swiper.com.cn/

1、首先下载swiper相关css和js文件然后引入

```
<link rel="stylesheet" href="/resources/css/swiper-3.3.1.min.css">
<script src="/resources/js/swiper.min.js"></script>
```

2、定义必要的swiper-container

```
<div class="swiper-container"></div>
```

3、从后台获取到数据

```
// data就是后台获取到的数据
function mainContainer(data) {
    // 定义必要的wrapper
	var wrapper = $('<div class="swiper-wrapper"></div>');
	// 定义slide
	$.each(data, function(index, item) {
		var slide = $('<div class="swiper-slide"></div>');
		$(slide).html('<a><img src="' + item.thumbPath + '"/></a>');
		$(slide).appendTo($(wrapper));
	});
	// 添加到最外层的swiper-container中
	$(wrapper).appendTo($('.swiper-container'));
	
	// 最后启动Swiper
	var mySwiper = new Swiper('.swiper-container', {
		autoplay: 2000,
		// ... 里面还有很多属性
	})
}
```

### jQuery的serializeArray方法

 > jQuery中的serializeArray()方法可以将form表单中的数据自动转换成JSON字符串
 
```
var json = $('#form').serializeArray();
```