# Easyui工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Easyui相关的内容：

### Easyui中对后台编码进行中文转换

``` js
// 1、先定义js函数
function formatStatus(value,row,index){
    if (value==0) {
        return '关';
    } else {
        return '开';
    } 
};

// 2、通过formatter属性引用
<input formatter="formatStatus" />
``` 

### Easyui中获取select中的值

``` jQuery
$('#status').combobox('getValue');// 这样是OK的
$('#status').val();// 这样是不行的，除非去除Easyui-combobox样式
``` 

###  Easyui中下拉框动态加载数据库中的数据

``` 
<!-- 需要定义Easyui-combobox样式，data-options中定义code和中文，url定义请求数据的 路径 -->
<input class="Easyui-combobox"  data-options="valueField:'code',textField:'name',url:'url'" />
```

### Easyui中回显Easyui-datetimebox中的值：

``` el
${page.liveTime?string("yyyy-MM-dd hh:mm:ss")}// 这样是OK的
${page.liveTime}// 这样是不行的
```

### Easyui中的特殊方法名称

 > 在Easyui中定义函数时，有些方法名称不能定义比如（close），及时定义了也不会被执行
 
### Easyui中的回显textarea中的数据

``` jsp
// 这样是不行的
<textarea value="${page.brief}" type="text"></textarea>
// 内容需要回显在标签之间
<textarea type="text">${page.brief}</textarea>
```

### Easyui自定义校验规则

``` js
// 先进行校验规则的扩展
$.extend($.fn.validatebox.defaults.rules, {
    QQ: {// 这个就是定义在validType中的
      validator: function (value, param) {
        return /^[1-9]\d{4,10}$/.test(value);
      },
      message: 'QQ号码不正确'
    },
}
// 还需要在validType中声明
<input required="true" class="Easyui-validatebox" validType="QQ" />
```

### Easyui表单校验多个条件：

``` 
// 一个校验条件只需要这样直接声明
validType="isPositive"
// 多个校验条件需要这样子定义
data-options="validType:['isPositive','maxLength[5]']"

// 对应的自定义校验方法
$.extend($.fn.validatebox.defaults.rules, {
	isPositive : {// 验证整数或小数
        validator: function (value) {
            return /^[1-9]\d*$/i.test(value);
        },
        message: '只能输入正整数'
    },
    maxLength:{
        validator:function(value,param){
          return value.length<=param[0]
        },
        message:'长度不能超过{0}位'
      }
});
```

### Easyui中下拉列表可多选，十分简单

```
// 只需要在data-options中加上 multiple:true 即可
data-options="multiple:true"
```

### Easyui中下拉框中定义分页

```
// 页面代码
<input class="Easyui-combobox" required="true" name="serialid" id="serialid" />
// js代码
$('#serialid').combogrid({
	panelWidth: 226,
	panelHeight: 353,
	idField: 'id',        //ID字段  
	textField: 'title',    //显示的字段  
	url: "/serialsCatalog/getSerialsList",
	fitColumns: true,
	striped: true,
	editable: true,
	pagination: true,           //是否分页
	rownumbers: true,           //序号
	collapsible: true,         //是否可折叠的
	method: 'post',
	columns: [[
				{ field: 'id', title: '专题编号', width: 80, hidden: true },
				{ field: 'title', title: '专题名称', width: 189 }
			]],
	onSelect: function () {              //选中处理
		$('#serialid').val($('#serialid').combogrid('grid').datagrid('getSelected').Gender);
    }
});
```

### Easyui中回显多选下拉框中的默认值

```
// EL表达式只能在HTML中的<script></script>中使用
$('#tags').combobox('setValues', [${books.tags}]);
```

### Easyui-datagrid获取所有行

```
$('#tt').datagrid({onLoadSuccess : function(data){
	var classroom = $('#classroomID').val();
	var rows = data.rows;
	if(classroom !== '') {
		for(var i = 0;i < rows.length; i++) {
			if(rows[i].id == classroom) {
		    	$('#tt').datagrid('selectRow',i);
				break;
			}
		}
	}
}});
```

### Easyui中getChecked和getSelected区别

``` 
// getChecked能够获取到所有的行对象
var rows = $ ('#tt').datagrid ('getChecked');
// getSelected只能获取到单行对象，即使选择了多行对象
var row = $ ('#tt').datagrid ('getSelected');
``` 

### Easyui中表数据设置多选、全选按钮

``` 
<!-- 在table中只需要添加一行th -->
<th width=10% data-options="field:'ck', checkbox:true"></th>
``` 

### Easyui中idField属性

 > 今天在对Easyui-datagrid数据做批量删除的时候，getChecked选中多个，提交时却只有一条数据，经历一番搜索发现是因为Easyui-datagrid中的<font color="FF2D2D">idField</font>属性设置的字段和后端传输过来的字段匹配不上，匹配上了之后就OK了。

### easyui监听窗口关闭事件

今天在写easyui代码的时候需要在关闭某个窗口后实时刷新另一个页面的数据：

```
$(function() {
    // 定义的窗口
	$('body').append('<div id="myWindow" class="easyui-dialog" closed="true"></div>');
	// 窗口关闭之后，当前页面就会刷新，完美
	 $('#myWindow').window({
        onBeforeClose: function () { //当面板关闭之前触发的事件
        	window.location.reload();
        }
    });
});
```