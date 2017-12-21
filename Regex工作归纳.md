# Regex工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Regex相关的内容：

### 正则表达式中的?&lt;group&gt;分组

今天在项目中遇到了一个正则表达式，如下：

``` 
^(?<id>\\d+)-(?<fileType>\\d+)-(?<size>\\d+)-(?<orfId>[0-9a-zA-Z]{32})-(?<screen>\\d+)-(?<version>\\d+)\\.orf$
``` 

刚开始我看这个正则表达式我是拒绝的，后来问了度娘之后才发现好简单，这里其实用了?&lt;group&gt;分组的功能，也就是说将具体内容放入字典中。

``` 
// 这段文本可以匹配这个正则表达式
233-0-123321-989df0cd8c4140d9b4c000f3a7afc878-4-1.orf

// 模拟匹配后的结果
id : 233
fileType : 0
size : 123321
orfId : 989df0cd8c4140d9b4c000f3a7afc878
screen : 4
version : 1
``` 

除了(?&lt;group&gt;)其余的正则表达式符号意义分别是：

``` 
// ^ 表示以指定字符串开头
// $ 表示以指定字符串结尾
// \\d+ 第一个\起转义的作用，d表示[0-9]中的一个，+表示至少一个
``` 

### 利用正则表达式截取字符串中特定格式的字符串

代码需要在一段很长的字符串中截取特定的字符串（有多个）

``` 
String longlong = "````````````<i>6d1cceba652645a080936cd97a585280`暖洋洋的红丝带````````````<i>6d1cceba652645a080936cd97a585280`阿西吧一道雷```````````"；

// 需要从中截取出32位的UUID，可以这样做，就能截取到匹配规则的字符串了
Pattern p=Pattern.compile("<i>[0-9a-zA-Z]{32}");  
Matcher m=p.matcher(responseBody);  
while(m.find()){  
	System.out.println(m.group().replace("<i>", ""));  
}
``` 

### 利用正则表达式替换字符串中的字符

``` 
String longlong = "<res result=\"0\" uid=\"0\"  sid=\"asdf798asd7fas7df89asd7\">";

// 想把sid=""和其中的数据干掉，如下：
longlong.replaceAll(" sid=\"(.*?)\"", "");
``` 


