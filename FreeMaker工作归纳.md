# FreeMaker工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和FreeMaker相关的内容：

### FreeMaker中使用switch

``` 
// EL表达式${}需要在 '' 中，否则会报错
<#switch '${resType}'> 
	<#case 48> 
    	<input disabled="disabled" value="星宝书" />
	<#break> 
	<#case 64> 
    	<input disabled="disabled" value="儿歌" />
	<#break> 
	<#case 80> 
    	<input disabled="disabled" value="故事" />
	<#break> 
	<#case 96> 
    	<input disabled="disabled" value="视频" />
	<#break> 
</#switch>
``` 
