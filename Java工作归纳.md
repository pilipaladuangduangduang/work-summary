# Java工作归纳

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Java相关的内容：

### 防止java.lang.OutOfMemoryError

> 分配JVM的内存防止OOM：-Xms512m -Xmx512m -XX:MaxNewSize=512m -XX:MaxPermSize=512m

### Java枚举的常见用法

``` 
public enum SQLEnum {
    
    INSERT("insert","新增"),
    DELETE("delete","删除"),
    UPDATE("update","更新"),
    SELECT("select","查询");
    
    private String key;// 一般为数字或字母
    
    private String value;// 一般为中文

    SQLEnum(String key, String value) {
        this.key = key;
        this.value = value;
    }
    
    public static SQLEnum getEnumByKey(String key) {
        for (SQLEnum sqlenum : SQLEnum.values()) {
            if (StringUtils.equals(key, sqlenum.getKey())) {
                return sqlenum;
            }
        }
        return null;
    }
    
    public static SQLEnum getEnumByValue(String value) {
        for (SQLEnum sqlenum : SQLEnum.values()) {
            if (StringUtils.equals(value, sqlenum.getValue())) {
                return sqlenum;
            }
        }
        return null;
    }
    
    // 省略setter、getter方法
}
``` 

### Java1.7中的try(){}

``` java
// try () {} catch() {}，1.7新特性自动关闭流
try(InputStream in = new FileInputStream(source);
    OutputStream out = new FileOutputStream(target)){
    
} catch(Exception e) {
    
}
``` 

### 重写equals()、hashCode()、toString()

``` java
// 通过commons.lang3.builder包中的XxxBuilder类来重写
@Override 
public boolean equals(Object obj ) {
    return EqualsBuilder.reflectionEquals (this , obj );
}
@Override
public int hashCode() {
    return HashCodeBuilder.reflectionHashCode( this);
}
@Override
public String toString() {
return ToStringBuilder.reflectionToString( this, ToStringStyle.SHORT_PREFIX_STYLE );
}
``` 

### 获取文件的后缀名（主要是为了以后的复用）

``` java
int dot = fileName.lastIndexOf(".");
// 不包含点
String extension = fileName.substring(dot + 1, fileName.length());
// 包含点
String dotExtension = fileName.substring(dot, fileName.length());
``` 

### 通过POI读取Excel文件中的数据

``` java
// 通过工厂类获取文件流对象，无需自行判断xls或xlsx
Workbook wb = WorkbookFactory.create(inputStream);

// 获取sheet对象
Sheet sheet = wb.getSheetAt(0);

// 普通for循环遍历，性能比iterator相比差很多，不推荐使用
// int rows = sheet.getPhysicalNumberOfRows();
//
// long forStartTime = System.currentTimeMillis();
// for (int i = 0; i < rows; i++) {
// Row row = sheet.getRow(i);
// Cell cell = row.getCell(1);
// cell.setCellFormula("0" + "0" + "0" + "0" + "0" + "0" + "0"); //  字符串相加是比较耗时的操作
// }
// long forEndTime = System.currentTimeMillis();
// System.out.println(forEndTime - forStartTime + "forforfor");

// 在这里，通过迭代器遍历，性能是for循环的10倍，推荐使用
Iterator<Row> rowIterator = sheet.rowIterator();

// 转换后的数据都是存储在这个List中
List<T> list = new ArrayList<T>();
int j = 0;// 计数器用于跳过Excel的首行
int titleLen = 0;// 用于记录Excel中的列的个数

while (rowIterator.hasNext()) {
	++j;
	Row row = rowIterator.next();

	if (j == 1) {// 跳过Excel中的第一行
		titleLen = row.getPhysicalNumberOfCells();
		continue;
	}
	
	// 将Excel数据存储到数组中
	String[] arr = new String[titleLen];
	for (int i = 0; i < titleLen; i++) {
		Cell cell = row.getCell(i);
		if (cell != null) {
			arr[i] = cell.getStringCellValue();
		}
	}
	
	T t = new T();
	
	// 对Exel中的信息进行转换到T中...
	
	list.add(t);
}

// 将Excel转换成对应的List<T>之后，就开始入库...
insert(List<T>)
``` 

### POI中对Excel中数值日期数据的转换

``` java
switch (cell.getCellType()) {// 单元格中的值类型
    case 0:// 数值类型
    	if (HSSFDateUtil.isCellDateFormatted(cell)) {
    		// 如果是date类型则 ，获取该cell的date值
    		Date value = HSSFDateUtil.getJavaDate(cell
    				.getNumericCellValue());
    		arr[i] = DateUtils.date2String(value);
    	} else { // 纯数字
    		arr[i] = String.valueOf(cell.getNumericCellValue());
    	}
    	break;
    case 1:// 字符串类型
    	arr[i] = cell.getStringCellValue();
    	break;
    case 3:// 空
    	arr[i] = "";
    	break;
    default:
    	break;
}
```


### 解压Zip或Rar文件的工具类：

``` java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.util.Enumeration;

import org.apache.tools.zip.ZipEntry;
import org.apache.tools.zip.ZipFile;

import com.github.junrar.Archive;
import com.github.junrar.rarfile.FileHeader;

/**
 * @desc 解压ZIP或RAR
 * @author niezhichun
 */
public class UnZipOrRarUtils {

	/*** 这里用到了synchronized ，也就是防止出现并发问题 ***/
	public static synchronized void untar(String tarFileName, String extPlace)
			throws Exception {
		unRarFile(tarFileName, extPlace);
	}

	public static synchronized void unzip(String zipFileName, String extPlace)
			throws Exception {
		unZipFiles(zipFileName, extPlace);
	}

	/**
	 * 解压zip格式的压缩文件到指定位置
	 * 
	 * @param zipFileName
	 *            压缩文件
	 * @param extPlace
	 *            解压目录
	 * @throws Exception
	 */
	public static boolean unZipFiles(String zipFileName, String extPlace)
			throws Exception {
		System.setProperty("sun.zip.encoding",
				System.getProperty("sun.jnu.encoding"));
		try {
			(new File(extPlace)).mkdirs();
			File f = new File(zipFileName);
			ZipFile zipFile = new ZipFile(zipFileName); 
			if ((!f.exists()) && (f.length() <= 0)) {
			    //一定要记得关闭ZipFile，否则JVM不会释放内存，后果你懂的~
				zipFile.close();
				throw new Exception("要解压的文件不存在!");
			}
			String strPath, gbkPath, strtemp;
			File tempFile = new File(extPlace);
			strPath = tempFile.getAbsolutePath();
			Enumeration<?> e = zipFile.getEntries();
			while (e.hasMoreElements()) {
				ZipEntry zipEnt = (ZipEntry) e.nextElement();
				gbkPath = zipEnt.getName();
				if (zipEnt.isDirectory()) {
					strtemp = strPath + File.separator + gbkPath;
					File dir = new File(strtemp);
					dir.mkdirs();
					continue;
				} else {
					// 读写文件
					InputStream is = zipFile.getInputStream(zipEnt);
					BufferedInputStream bis = new BufferedInputStream(is);
					gbkPath = zipEnt.getName();
					strtemp = strPath + File.separator + gbkPath;

					// 建目录
					String strsubdir = gbkPath;
					for (int i = 0; i < strsubdir.length(); i++) {
						if (strsubdir.substring(i, i + 1).equalsIgnoreCase("/")) {
							String temp = strPath + File.separator
									+ strsubdir.substring(0, i);
							File subdir = new File(temp);
							if (!subdir.exists())
								subdir.mkdir();
						}
					}
					FileOutputStream fos = new FileOutputStream(strtemp);
					BufferedOutputStream bos = new BufferedOutputStream(fos);
					int c;
					while ((c = bis.read()) != -1) {
						bos.write((byte) c);
					}
					bos.close();
					fos.close();
				}
			}
			//一定要记得关闭ZipFile，否则JVM不会释放内存，后果你懂的~
			zipFile.close();
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}

	/**
	 * 根据原始rar路径，解压到指定文件夹下.
	 * 
	 * @param srcRarPath
	 *            原始rar路径
	 * @param dstDirectoryPath
	 *            解压到的文件夹
	 */
	public static void unRarFile(String srcRarPath, String dstDirectoryPath) {
		if (!srcRarPath.toLowerCase().endsWith(".rar")) {
			System.out.println("非rar文件！");
			return;
		}
		File dstDiretory = new File(dstDirectoryPath);
		if (!dstDiretory.exists()) {// 目标目录不存在时，创建该文件夹
			dstDiretory.mkdirs();
		}
		Archive a = null;
		try {
			a = new Archive(new File(srcRarPath));
			if (a != null) {
				// a.getMainHeader().print(); // 打印文件信息.
				FileHeader fh = a.nextFileHeader();
				while (fh != null) {
					// 防止文件名中文乱码问题的处理

					String fileName = fh.getFileNameW().isEmpty() ? fh
							.getFileNameString() : fh.getFileNameW();
					if (fh.isDirectory()) { // 文件夹
						File fol = new File(dstDirectoryPath + File.separator
								+ fileName);
						fol.mkdirs();
					} else { // 文件
						File out = new File(dstDirectoryPath + File.separator
								+ fileName.trim());
						try {
							if (!out.exists()) {
								if (!out.getParentFile().exists()) {// 相对路径可能多级，可能需要创建父目录.
									out.getParentFile().mkdirs();
								}
								out.createNewFile();
							}
							FileOutputStream os = new FileOutputStream(out);
							a.extractFile(fh, os);
							os.close();
						} catch (Exception ex) {
							ex.printStackTrace();
						}
					}
					fh = a.nextFileHeader();
				}
				a.close();
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
``` 

对应的maven依赖

``` pom.xml
<!-- 用于解压zip文件 -->
<dependency>
    <groupId>org.apache.ant</groupId>
    <artifactId>ant</artifactId>
    <version>1.9.7</version>
</dependency>

<!-- 用于解压rar文件 -->
<dependency>
    <groupId>com.github.junrar</groupId>
    <artifactId>junrar</artifactId>
    <version>0.7</version>
</dependency>
``` 

### Java文件重命名

``` java
// renameTo之后，会把源文件给删除的
oldFile.renameTo(new File("newFilePath"))
``` 

### jaudiotagger解析音频和语音文件

``` java
public class AudioUtils {

	private AudioUtils() {
	}

	/**
	 * 利用第三方开源项目：jaudiotagger，获取音频文件的播放时长
	 */
	public static String getAudioTime(String audioPath) throws Exception {
		File file = new File(audioPath);
		if(audioPath.endsWith(".mp3")) {// 解析MP3文件，获取时长
			MP3File f = (MP3File) AudioFileIO.read(file);  
			MP3AudioHeader audioHeader = (MP3AudioHeader) f.getAudioHeader();
			return audioHeader.getTrackLength() + "";
		} else if(audioPath.endsWith(".wav")) {// 解析wav文件，获取时长
			WavInfoReader wavread = new WavInfoReader();
			GenericAudioHeader header = wavread.read(new RandomAccessFile(file, "r"));
			return (int) header.getPreciseLength() + "";
		} else {
			return null;
		}
		
	}
	
}
``` 

需要的Maven依赖：
``` pom.xml
<dependency>  
    <groupId>org</groupId>  
    <artifactId>jaudiotagger</artifactId>  
    <!-- 2010年就停止更新了 -->
    <version>2.0.3</version>  
</dependency> 
``` 

### FileUtils中删除不存在的文件

``` java
// 该方法删除文件时，即使文件不存在也不会抛异常
FileUtils.deleteQuietly(file);
更多和FileUtils相关的API操作，请参考：http://eksliang.iteye.com/blog/2217374
``` 

### FileUtils创建文件夹

``` java
// 没有文件夹就会创建，有文件夹之后不会覆盖
FileUtils.forceMkdir(new File(PATH));
``` 

### StringTokenizer的用法

``` java
// StringTokenizer，字符串分割器
String str = "wo-shi-nie-zhi-chun";
// 默认以空格分割；可以自定义分割符，第三个参数：true表示包含自定义的分隔符，flase表示不包含
StringTokenizer stoken = new StringTokenizer(str, "-", true);
// 要分成几部分
System.out.println(stoken.countTokens());
while(stoken.hasMoreElements()){
     System.out.println(stoken.nextToken());
}
``` 

### StringBuilder去除最后一位符号最优方法

``` java
// StringBuilder删除最后一个字符，最优选择
sb.setLength(sb.length() - 1);
``` 

### 以指定长度分割字符串算法

``` java
/**
 * 以指定长度切割字符串，并返回字符串数组
 * @author niezhichun
 * @param str         需要被切割的字符串
 * @param splitLength 指定切割的长度
 * @return []         字符串切割后的数组
 */
static String[] specifiedLengthSplitArr(String str, int splitLength) {
    StringBuilder newStr = new StringBuilder(str);
    while(newStr.length() % splitLength != 0) {
        // 字符串长度不能被指定长度整除就在末端补 * 后续可优化
        newStr.append("*");
    }
    int len = newStr.length();
    char[] arr = newStr.toString().toCharArray();
    String[] strArr = new String[ len / splitLength];
    int i = 0, k = 0;
    for (; i < len; i += splitLength) {
        StringBuilder sb = new StringBuilder();
        for (int j = i ; j < splitLength + i ; j ++) {
            sb.append( arr[ j]);
        }
        strArr[ k] = sb.toString();
        k++;
    }
    return strArr;
}
``` 

### 实用的位运算 >> 1

``` java
// >> 1的作用相当于值取一半，可以用于二分法判断
if (index < (size >> 1)) {// 小于数组一半的时候，从前往后遍历
    for (int i = 0; i <= index; i++)    
        e = e.next;    
} else {// 大于数组一半的时候，从后往前遍历
    for (int i = size; i > index; i--)    
        e = e.previous;    
}

// >> 1规律：显而易见
0 >> 1 = 0
1 >> 1 = 0
2 >> 1 = 1
3 >> 1 = 1
4 >> 1 = 2
5 >> 1 = 2
6 >> 1 = 3
7 >> 1 = 3
8 >> 1 = 4
9 >> 1 = 4
``` 

### 使用RandomAccessFile

使用RandomAccessFile过程中，遇到了以下错误：

 > must be one of "r", "rw", "rws", or "rwd"
 
 表示第二个参数只能使用这四个模式（只读、读写等）中的一种。
 
### 使用jaudiotagger操作wav文件

今天在服务器上上传wav文件出错（在windows下没问题），如下:

 > No line matching interface Clip supporting format PCM_SIGNED unknown sample rate, 16 bit, stereo, 4 bytes/frame, big-endian is supported
 
看了下源代码发现处理wav文件是通过javax.sound包下中类，但是具体原因还是没能找到（怀疑是因为操作系统底层的一些差别），于是只能别的方式来处理了：

``` java
// 使用了jaudiotagger 开源库
WavInfoReader wavread = new WavInfoReader();
GenericAudioHeader header = wavread.read(new RandomAccessFile(file, "r"));
// 返回播放时长信息
return (int) header.getPreciseLength() + "";
``` 

### Java file.getName() 在Linux和Windows中的大坑

今天遇到了一个大坑，在本地Windows环境中，调用file.getName()方法获取到的仅仅只是文件名，然而在Linux环境中file.getName()会获取到该文件的完整路径名称，然后在Windows环境中正常执行的程序在Linux环境中就失败了。所以得出一条结论：<font color="FF2D2D">不使用file.getName()</font>或通过操作系统的类型判断：

``` java
// 判断不同的操作类型：先获取到当前系统的类型
public static final String OS_NAME = System.getProperties().getProperty("os.name");
``` 

### 使用AtomicInteger

``` java
// 因为++和--操作符都不是线程安全的
AtomicInteger threadSafeNum = new AtomicInteger(233);
threadSafeNum.getAndIncrement();// 安全的递增
threadSafeNum.getAndDecrement();// 安全的递减
// 等等
``` 

### 关于基本类型的值Copy问题

问题比较抽象，就直接看代码吧~

``` java
int i = 0;
// 调用某个方法，即使该方法中有操作对i进行了修改，也不会i实际的值
somemethod(i);
// i还是等于0
``` 
 
### JDK中自带的进制转换

``` 
// 项目中有需求用到进制转换，十六进制的数转为十进制的数
Integer.parseInt("30500", 16) // 197888
// 其他进制之间的转换，等到用到的时候再说吧
``` 

### 判断流对象中文件大小

``` 
InputStream in = request.getInputStream();
int size = in.available();
``` 

### JDK中Appendable接口

JDK中的Appendable接口封装了append()这一系列操作。

像是StringBuilder和StringBuffer就是该接口的子类。

### String.valueOf()转换字符

``` 
// 将 char 类型转换为 String类型
String.valueOf(char)

// valueOf具体原理实现：new一个String对象，String类是通过char[]存储具体数据的
public static String valueOf(char c) {
    char[] data = {c};
    return new String(data, true);
}
String(char[] value, boolean share) {
    // assert share : "unshared not supported";
    this.value = value;
}
``` 

### String不可变的原因

``` 
// String不可变的原因是因为存储数据的char[]是final修饰的
public final class String  {
    private final char value[];
    // 省略其他代码...
}
``` 

### Java中Runtime执行程序或脚本（shell、bat）

``` 
// 获取运行时环境对象
Runtime runtime = Runtime.getRuntime();
// exec调用jarsigner程序进行签名
runtime.exec(jarsigner ...);
// exec调用cmd tasklist查看当前系统的进行列表详情， /c 表示执行命令后退出cmd
runtime.exec("cmd /c tasklist")
// runtime.exec 返回 Process（进程）对象
``` 

### Integer之间的比较也得用equals()

 > 不仅String对象之间比较需要用equals，Integer、Long、Double、Float对象之间的比较也需要通过equals。
 
 
### Json数组字符串转换为List集合

``` 
// 今天封装了一个转换json的工具类（通过Gson）
/**
 * @desc   Gson解析json字符串工具类
 * @author niezhichun
 */
public class GsonUtils {

	private static Gson GSON = new Gson();
	private static JsonParser PARSER = new JsonParser();
	
	private GsonUtils() {
	};

	/**
	 * @desc         将json字符串数组，根据相应的类型转换为List集合
	 * @param json   需要被转换的json字符串
	 * @param clazz  与json字符串相对应的类型
	 * @return       List集合
	 */
	public static <T> List<T> getList(String json, Class<T> clazz) {

		List<T> list = new ArrayList<>();

		JsonElement el = PARSER.parse(json);

		// 转换json数组
		JsonArray jsonArray = null;
		if (el.isJsonArray()) {
			jsonArray = el.getAsJsonArray();
		} else {
			return list;
		}

		Iterator it = jsonArray.iterator();
		while (it.hasNext()) {
			JsonElement e = (JsonElement) it.next();
			list.add(GSON.fromJson(e, clazz));
		}

		return list;
	};

	/**
	 * 转换单个对象的方法等用到的时候再说
	 */
}
``` 

### String的substring()

``` 
String filepath = file.getPath();
// 该方法和下面方法的效果一致
filepath.substring(index);
filepath.substring(index, filepath.length());
``` 

### 获取文件的MD5值

``` 
// 很简单，org.apache.commons.codec.digest包中已经自带了
// 这里方法中的参数是流对象
String fileMD5 = DigestUtils.md5Hex(in);
``` 

### JAXB规范将XML转换为对应的JavaBean，JAXB是JDK自带的XML2Bean的规范

``` 
// 首先定义能好XML数据配对上的JavaBean
JaxbUtils resultBinder = new JaxbUtils(YourClass.class, CollectionWrapper.class);
YourClass dto = resultBinder.fromXml(xml);

// 然后再对应的JavaBean中添加注解
@XmlRootElement(name = "定义根节点")
@XmlElement(name = "定义标签") 
@XmlAttribute(name = "定义标签中属性")
// XmlValue是定义标签外的值：<tag>看这里看这里</tag>
@XmlValue
``` 

``` 
// 这里是具体的JaxbUtils
public class JaxbUtils {

	// 多线程安全的Context.
	private JAXBContext jaxbContext;

	/**
	 * @param types
	 *            所有需要序列化的Root对象的类型.
	 */
	public JaxbUtils(Class<?>... types) {
		try {
			jaxbContext = JAXBContext.newInstance(types);
		} catch (JAXBException e) {
			throw new RuntimeException(e);
		}
	}

	/**
	 * Java Object->Xml.
	 */
	public String toXml(Object root, String encoding) {
		try {
			StringWriter writer = new StringWriter();
			createMarshaller(encoding).marshal(root, writer);
			return writer.toString();
		} catch (JAXBException e) {
			throw new RuntimeException(e);
		}
	}

	/**
	 * Java Object->Xml, 特别支持对Root Element是Collection的情形.
	 */
	public String toXml(Collection<?> root, String rootName, String encoding) {
		try {
			CollectionWrapper wrapper = new CollectionWrapper();
			wrapper.collection = root;

			JAXBElement<CollectionWrapper> wrapperElement = new JAXBElement<CollectionWrapper>(new QName(rootName), CollectionWrapper.class, wrapper);

			StringWriter writer = new StringWriter();
			createMarshaller(encoding).marshal(wrapperElement, writer);

			return writer.toString();
		} catch (JAXBException e) {
			throw new RuntimeException(e);
		}
	}

	/**
	 * Xml->Java Object.
	 */
	@SuppressWarnings("unchecked")
	public <T> T fromXml(String xml) {
		try {
			StringReader reader = new StringReader(xml);
			return (T) createUnmarshaller().unmarshal(reader);
		} catch (JAXBException e) {
			throw new RuntimeException(e);
		}
	}

	/**
	 * Xml->Java Object, 支持大小写敏感或不敏感.
	 */
	@SuppressWarnings("unchecked")
	public <T> T fromXml(String xml, boolean caseSensitive) {
		try {
			String fromXml = xml;
			if (!caseSensitive)
				fromXml = xml.toLowerCase();
			StringReader reader = new StringReader(fromXml);
			return (T) createUnmarshaller().unmarshal(reader);
		} catch (JAXBException e) {
			throw new RuntimeException(e);
		}
	}

	/**
	 * 创建Marshaller, 设定encoding(可为Null).
	 */
	public Marshaller createMarshaller(String encoding) {
		try {
			Marshaller marshaller = jaxbContext.createMarshaller();

			marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, Boolean.TRUE);

			if (StringUtils.isNotBlank(encoding)) {
				marshaller.setProperty(Marshaller.JAXB_ENCODING, encoding);
			}
			return marshaller;
		} catch (JAXBException e) {
			throw new RuntimeException(e);
		}
	}

	/**
	 * 创建UnMarshaller.
	 */
	public Unmarshaller createUnmarshaller() {
		try {
			return jaxbContext.createUnmarshaller();
		} catch (JAXBException e) {
			throw new RuntimeException(e);
		}
	}

	/**
	 * 封装Root Element 是 Collection的情况.
	 */
	public static class CollectionWrapper {
		@XmlAnyElement
		protected Collection<?> collection;
	}

}
``` 

### 比较文本信息并获取到具体的不同之处

``` 
// Google大法好，这里引用文本比较的开源项目，然后自行封装了一个工具类
public class DiffMatchPatchUtils {

	private DiffMatchPatchUtils() {
	};

    // 这个开源项目中就这么一个 DiffMatchPatch 类
	private static DiffMatchPatch dmp = new DiffMatchPatch();

	/**
	 * 将两个文本的不同之处返回
	 * @param text1 文本1
	 * @param text2 文本2
	 * @return 返回的Map中的K、V分别对应着两个文本中的不同之处
	 */
	public static Map<String, String> patchText(String text1, String text2) {
		Map<String, String> diffMap = new LinkedHashMap<>();
		LinkedList<Patch> paths = dmp.patchMake(text1, text2);

		for (Patch patch : paths) {
			LinkedList<DiffMatchPatch.Diff> diffs = patch.diffs;

			String temp = StringUtils.EMPTY;
			for (Diff diff : diffs) {

				if (DiffMatchPatch.Operation.DELETE.equals(diff.operation)) {
					temp = diff.text;
				}
				if (DiffMatchPatch.Operation.INSERT.equals(diff.operation)) {
					diffMap.put(temp, diff.text);
				}
			}

		}
		return diffMap;
	}

}
``` 

``` 
// 对应的Maven依赖
<dependency>
    <groupId>org.bitbucket.cowwoc</groupId>
    <artifactId>diff-match-patch</artifactId>
    <version>1.1</version>
</dependency>
```

### HttpClinet发送请求

``` 
// 首先 new 出客户端
CloseableHttpClient httpclient = HttpClients.createDefault();

// 然后 new 出请求，可以使GET方式，也可以是POST方式
HttpGet get = new HttpGet(url);

// 修改此次请求中的头部信息
get.setHeader(key, value);

// 最后执行，这里的可以定义响应处理帮助类，把一系列的响应处理都封装到单例中
httpclient.execute(get, ResponseHandlerHelper.getInstance().getTextResponseHandler());
``` 

``` 
// 封装了响应处理帮助类
public class ResponseHandlerHelper {

	private static final Logger LOGGER = LoggerFactory.getLogger(ResponseHandlerHelper.class);

	private ResponseHandlerHelper() {
	};

	// 静态内部类初始化实例，懒加载
	static class ResponseHandlerInstanceHelper {
		private static ResponseHandlerHelper instance = new ResponseHandlerHelper();
	}

	public static ResponseHandlerHelper getInstance() {
		return ResponseHandlerInstanceHelper.instance;
	}

    // 封装对文本操作的响应处理方法
	public ResponseHandler<String> getTextResponseHandler() {
		return new ResponseHandler<String>() {

			@Override
			public String handleResponse(final HttpResponse response) throws ClientProtocolException, IOException {
				int status = response.getStatusLine().getStatusCode();
				if (status >= 200 && status < 300) {
					HttpEntity entity = response.getEntity();
					return entity != null ? EntityUtils.toString(entity) : null;
				} else {
					throw new ClientProtocolException("Unexpected response status: " + status);
				}
			}

		};
	}

    // 封装对文件操作的响应处理方法
	public ResponseHandler<Void> fileResponseHandler() {
		return new ResponseHandler<Void>() {
			@Override
			public Void handleResponse(final HttpResponse response) throws ClientProtocolException, IOException {

				int status = response.getStatusLine().getStatusCode();
				if (status >= 200 && status < 300) {
					HttpEntity entity = response.getEntity();
					if (entity != null) {
						InputStream currentIn = entity.getContent();
						// ... 对文件的处理
					}
				} else {
					throw new ClientProtocolException("Unexpected response status: " + status);
				}
				return null;
			}
		};
	}
}
``` 

### for循环中定义多个变量

``` 
// for循环中第一个分号前可以定义多个同一类型的参数
for (int i = 0, j =0, k = 0; i < args.length; i++) {
	// ...
}
```

### 匿名对象的方式获取接口或抽象类的实例

``` 
// 下面的通过匿名对象的方式实现接口的方式稍微有点复杂
public final class Splitter {

    private Splitter(Strategy strategy) {
        // 省略给属性赋值的过程
    }
    
    // 内部接口，是对集合数据的封装
    private interface Strategy {
        Iterator<String> iterator(Splitter splitter, CharSequence toSplit);
    }
    
    // 静态抽象内部类，是对上面接口定义方法的实现
    private abstract static class SplittingIterator extends AbstractIterator<String> {
        
        abstract int separatorStart(int start);

        abstract int separatorEnd(int separatorPosition);

        // 构造器
        protected SplittingIterator(Splitter splitter, CharSequence toSplit) {
            // 省略...
        }

    }
    
    // 这是一个返回当前类实例的静态方法
    public static Splitter on(final CharMatcher separatorMatcher) {
        checkNotNull(separatorMatcher);
        // return时直接 new 当前类实例，很常见
        return new Splitter(
            // 因为构造器需要Strategy类型的参数，而Strategy是接口类型，并且该接口没有实现类，所以只有通过匿名对象的方式来获取该接口的实例，本质上就是需要实现接口中的抽象方法
            new Strategy() {
                @Override
                // 实现Strategy接口的抽象方法，返回SplittingIterator类，SplittingIterator是AbstractIterator的子类，当然也就是Iterator的子类
                public SplittingIterator iterator(Splitter splitter, final CharSequence toSplit) {
                    //直接返回SplittingIterator类的实例，但是SplittingIterator类是一个静态内部抽象类，也没有实现类，所以也需要通过匿名对象的方式来实例化SplittingIterator类的对象，本质上也就是实现了SplittingIterator类中的抽象方法
                    return new SplittingIterator(splitter, toSplit) {
                        @Override
                        // 实现SplittingIterator类的抽象方法
                        int separatorStart(int start) {
                            return separatorMatcher.indexIn(toSplit, start);
                        }

                        @Override
                        // 实现SplittingIterator类的抽象方法
                        int separatorEnd(int separatorPosition) {
                            return separatorPosition + 1;
                        }
                    };
                }
            });
    }

}

``` 

### Java中Interface中的成员变量

Java中的Interface中的成员变量都是public static final修饰，所以可以像这样：

``` 
public interface Test {
    // 和public static final String TEST = "TEST";效果等同
    String TEST = "TEST";
}
``` 

### Java获取当前时间的前几分钟或后几分钟

``` 
public static String getCurrentTimeBATime(int count) {
	Calendar calendar = Calendar.getInstance();

	calendar.add(Calendar.MINUTE, count);

	return new SimpleDateFormat(DateFormatUtils.DATE_FORMAT3).format(calendar.getTime());

}
``` 

### 获取某个时间点的前几分钟或后几分钟

``` 
public static String getSomeTimeBATime(String someTime, int count) {
	SimpleDateFormat dateFormat = new SimpleDateFormat(DateFormatUtils.DATE_FORMAT3);
	
	try {
		Date hehe = dateFormat.parse(someTime);
		Calendar calendar = Calendar.getInstance();
		calendar.setTime(hehe);
		calendar.add(Calendar.MINUTE, count);
		return dateFormat.format(calendar.getTime());
	} catch (ParseException e) {
		e.printStackTrace();
		return null;
	}
}
``` 

### Java获取一个时间段中的每一天

``` 
public static LinkedList<String> betweenDate(String d1, String d2) {
	SimpleDateFormat dateFormat = new SimpleDateFormat(DateFormatUtils.DATE_FORMAT1);
	LinkedList<String> days = new LinkedList<String>();

	try {
		Date date1 = dateFormat.parse(d1);
		Date date2 = dateFormat.parse(d2);

		Calendar calendar = Calendar.getInstance();

		if (d1.compareTo(d2) > 0) {// 前面的时间比后面的时间大
			calendar.setTime(date2);
			while (calendar.getTime().before(date1)) {
				days.offer(dateFormat.format(calendar.getTime()));
				calendar.add(Calendar.DAY_OF_MONTH, 1);
			}
			days.offer(d1.substring(0, 10));// 把最后一天也加上
		} else if (d1.compareTo(d2) < 0) {// 前面的时间比后面小
			calendar.setTime(date1);
			while (calendar.getTime().before(date2)) {
				days.offer(dateFormat.format(calendar.getTime()));
				calendar.add(Calendar.DAY_OF_MONTH, 1);// 向后一天靠近
			}
			days.offer(d2.substring(0, 10));// 把最后一天也加上
		} else {// 两个比较的时间相等就返回其中的一个
			days.offer(d1);
		}

	} catch (Exception e) {
		e.printStackTrace();
	}

	return days;
}
``` 

### Java获取一个时间段中的每一分钟

``` 
public static LinkedList<String> betweenMin(String d1, String d2) {
	SimpleDateFormat dateFormat = new SimpleDateFormat(DateFormatUtils.DATE_FORMAT3);
	LinkedList<String> days = new LinkedList<String>();

	try {
		Date date1 = dateFormat.parse(d1);
		Date date2 = dateFormat.parse(d2);

		Calendar calendar = Calendar.getInstance();

		if (d1.compareTo(d2) > 0) {// 前面的时间比后面的时间大
			calendar.setTime(date2);
			while (calendar.getTime().before(date1)) {
				days.offer(dateFormat.format(calendar.getTime()));
				calendar.add(Calendar.MINUTE, 1);
			}
			days.offer(d1.substring(0, 16));// 把最后一天也加上
		} else if (d1.compareTo(d2) < 0) {// 前面的时间比后面小
			calendar.setTime(date1);
			while (calendar.getTime().before(date2)) {
				days.offer(dateFormat.format(calendar.getTime()));
				calendar.add(Calendar.MINUTE, 1);// 向后一天靠近
			}
			days.offer(d2.substring(0, 16));// 把最后一天也加上
		} else {// 两个比较的时间相等就返回其中的一个
			days.offer(d1);
		}

	} catch (Exception e) {
		e.printStackTrace();
	}

	return days;
}
``` 

### 将List<String>中重复的数据提取成Map<String, 个数>

自认为这是一个很常见的功能，所以在这里先记上一笔：

``` 
Map<String, Integer> map = new TreeMap<String, Integer>();
for (String item : channels) {
	if (map.containsKey(item)) {// 有就覆盖原有的数据，value + 1
		map.put(item, map.get(item).intValue() + 1);
	} else {// 当前map中没有该数据，就添加然后value设为1
		map.put(item, 1);
	}
}
LOGGER.warn("转换后的Map：[{}]", map);
Set<Map.Entry<String, Integer>> entries = map.entrySet();
for (Map.Entry<String, Integer> entry : entries) {
	String key = entry.getKey();
	String value = entry.getValue();
}
``` 

### CollectionUtils取两个集合的交集、并集、补集

这个功能不用想就知道很有用

``` 
Collection a,b;
CollectionUtils.union(a,b);// 并集
CollectionUtils.intersection(a,b)// 交集
CollectionUtils.subtract(a,b)// 补集
``` 

### LinkedList的一些使用心得

在开发过程中，我一般会把LinkedList当做队列使用，下面是我使用的一些心得：

``` 
LinkedList<String> hehe = new LinkedList<String>();
hehe.offer("666");// 建议使用offer，而不是add
hehe.poll();// 建议使用poll，而不是remove
hehe.peek();
``` 

### Collections对List中元素指定的属性进行排序

``` 
// 0、这是一个List
List<Topic> topics = ...

// 1、定义静态内部排序类，根据回复时间来排序（正序、逆序都行）
static class TopicReplyTimeComparator implements Comparator<Topic> {
	public int compare(Topic t1, Topic t2) {
		return t2.getReplyTime().compareTo(t1.getReplyTime());
	}
}

// 2、进行排序
Collections.sort(topics, new TopicReplyTimeComparator());
``` 

### List集合模拟分页返回数据

``` 
// 自认为这个功能以后还会用到，不过集合中的数据不能太大（不能超过十万）
// page是第几页：1、2、3...
// rows是每页的数量：15、20、30...
// topics是一个集合
int liststart = (page - 1) * rows;
int listend = liststart + rows;
int size = topics.size();
if (liststart >= size) {// 起始数据大于集合大小就返回空
	topics.clear();
} else {//自行体会
	topics = listend <= size ? topics.subList(liststart, listend) : topics.subList(liststart, size);
}
``` 

### 项目整合JSR-303注解式校验Bean规范

对于请求的参数，我们肯定是要去校验的，但如果每次都是手动校验的话，就会做很多重复的活，所以我们需要JSR-303的帮助~

首先引入响应的依赖：

``` 
<!-- Spring整合JSR303之注解式校验 -->
<dependency>
	<groupId>javax.validation</groupId>
	<artifactId>validation-api</artifactId>
	<version>1.1.0.Final</version>
</dependency>

<!-- 注解大法好 -->
<dependency>
	<groupId>org.hibernate</groupId>
	<artifactId>hibernate-validator</artifactId>
	<version>5.2.4.Final</version>
</dependency>
``` 

然后对jar包的类进行封装

``` 
public class WebValidator {
	
	private WebValidator() {
    }
	
    private static Validator validator;

    static {
        ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
        // 拿到规范中的实例对象
        validator = factory.getValidator();
    }

    // 校验DTO对象
    public static <T> List<String> validate(T t) {
    	ArrayList<String> list = Lists.newArrayList();
    	Set<ConstraintViolation<T>> violations = validator.validate(t);
    	for (ConstraintViolation<T> violate : violations) {
    		list.add(violate.getMessage());
		}
    	return list;
    }
    
	// 上诉只是很简单的Bean校验实现...

}
``` 

定义对应的异常

``` 
public class ValidateException extends RuntimeException {

	private static final long serialVersionUID = 1L;

	private List<String> errors = new ArrayList<String>();

	public ValidateException(List<String> errors) {
		this.errors = errors;
	}
	
	public ValidateException(String error) {
		this.errors.add(error);
	}

	@Override
	public String getMessage() {

		if (errors.size() == 1) {
			return errors.get(0);
		}

		return JoinerUtils.joinList(SymbolEnum.COMMA, errors);
	}
}
``` 

在Controller中再次封装，有异常就抛

``` 
public <T> void checkArgs(T t) {
	List<String> errmsgs = WebValidator.validate(t);
	if (errmsgs.size() > 0) {
		throw new ValidateException(errmsgs);
	}
}
``` 

最后在catch中进行处理就OK啦

### String中的valueOf方法

最近看到了这个方法，感觉挺有意思的，所以就记录一下

``` 
// 对null返回字符串的null
public static String valueOf(Object paramObject) {
	return ((paramObject == null) ? "null" : paramObject.toString());
}
// 对布尔类型，如果是true返回字符串的true，false则返回字符串的false
public static String valueOf(boolean paramBoolean) {
	return ((paramBoolean) ? "true" : "false");
}
// 对char则直接new String
public static String valueOf(char paramChar) {
	char[] arrayOfChar = { paramChar };
	return new String(arrayOfChar, true);
}
// 对int、long、float、double类型都是调用各自包装类中的toString方法
public static String valueOf(int paramInt) {
	return Integer.toString(paramInt);
}

public static String valueOf(long paramLong) {
	return Long.toString(paramLong);
}

public static String valueOf(float paramFloat) {
	return Float.toString(paramFloat);
}

public static String valueOf(double paramDouble) {
	return Double.toString(paramDouble);
}
``` 

### 字符串模板format方法，很强大，可以用于输出日志

``` 
main() {
    // 普通的format，输出：vip [a, b, c]
    format("vip","a","b","c");

    // 带有占位符的format，输出：a is before than b
    format("%s is before than %s","a","b");
}

static String format(String template, Object... args) {
    template = String.valueOf(template); // null -> "null"

    // start substituting the arguments into the '%s' placeholders
    StringBuilder builder = new StringBuilder(template.length() + 16 * args.length);
    int templateStart = 0;
    int i = 0;
    while (i < args.length) {
        int placeholderStart = template.indexOf("%s", templateStart);
        if (placeholderStart == -1) {
            break;
        }
        builder.append(template.substring(templateStart, placeholderStart));
        builder.append(args[i++]);
        templateStart = placeholderStart + 2;
    }
    builder.append(template.substring(templateStart));

    // if we run out of placeholders, append the extra args in square braces
    if (i < args.length) {
        builder.append(" [");
        builder.append(args[i++]);
        while (i < args.length) {
            builder.append(", ");
            builder.append(args[i++]);
        }
        builder.append(']');
    }

    return builder.toString();
}
``` 

### 判断Java类为哪种类型

``` 
// 判断Java类型是否是注解类型
test.getClass().isAnnotation();
// 判断Java类型是否是数组类型
test.getClass().isArray();
// 判断Java类型是否是枚举类型
test.getClass().isEnum();
// 判断Java类型是否是接口类型
test.getClass().isInterface();
// ...
``` 

### java.util.Arrays中的deepEquals和deepEquals0方法

 > 这两个方法是相互嵌套的，主要适用于比较两个数组及其中元素是否相等
 
### java.util.Arrays中的deepToString方法

 > 该方法主要用于将数组中的元素统统转换为String类型
 
### java.lang.ThreadLocal的使用

 > 在我们当前项目中ThreadLocal是用于存储每次请求的请求头信息和Session信息（因为ThreadLocal是线程隔离的），项目中还会有个请求拦截器，拦截所有请求然后封装请求头信息和Session信息set到ThreadLocl中，在之后的业务代码中我们想要使用Session，只需要get就OK了

### @Deprecated注解

 > Java中@Deprecated注解是对<font color="FF2D2D">过时</font>的类或方法的修饰。
 

### 通过包装类型来转换不同类型的值

Double、Long、Integer有自带的方法可以相互转换~

```
// Double值转long
new Double(2.33).longValue();
// Long值转double
new Long(233).doubleValue();
// ...
```

### Java中类都需要提供默认的构造器

 > 今天遇到一个错误：...Exception:com.xxx.Xxx.<font color="FF2D2D">&lt;init&gt;</font>()，这个错误是由SpringMVC抛出的，原因是因为当Controller方法中接收请求参数是一个对象的话，这个对象就需要提供默认的构造器，以便SpringMVC在接收请求之前可以实例化该对象然后将请求参数中数据赋值到该对象中~
 
 > 下午在iBatis中也遇到了，就是在iBatis执行SQL获取到数据之后，需要新建实例（需要默认构造器）来接收这些数据~
 
### List对象转为二维数组

今天在使用Dbutils的过程中，执行批量插入的时候，要求传入参数为二维数组而非集合对象，所以就需要进行一次转换

```
// 定义好集合
List<Group> ts = ... 

// 定义好二维数组
Object[][] data = new Object[ts.size()][3];
for (int i = 0; i < ts.size(); i++) {
	T t = ts.get(i);
	data[i][0] = t.getFirst();
	data[i][1] = t.getSecond();
	data[i][2] = t.getThird();
}
```

### Apache的Dbutils的使用

Dbuitils介于ORM框架和JDBC两者之间，是一个类库的存在，使用起来十分的方便~

项目的依赖：

```
<dependency>
    <groupId>commons-dbutils</groupId>
    <artifactId>commons-dbutils</artifactId>
    <version>1.6</version>
</dependency>
```

具体的使用过程：

```
// 定义好SQL语句
String sql = "select * from xxx";

// 定义好数据库连接对象
Connection conn = DriverManager.getConnection(DB_HOST, USERNAME, PASSWORD);

// 定义好Dbutils中的客户端对象
QueryRunner runner = new QueryRunner();

// 为查询出的属性封装DTO对象T，批量查询
List<T> ts = runner.query(conn, sql, new BeanListHandler<T>(T.class));

// 查询单个对象
T t = runner.query(conn, sql, new BeanHandler<T>(T.class));

// 设置事务默认不提交
conn.setAutoCommit(false);

// 定义好insert语句
sql = "insert into xxx(a, b, c) values(?, ?, ?)";

// 执行批量插入，这里传入的参数是一个二维数组
try {
	runner.insertBatch(conn, sql, new BeanListHandler<T>(T.class), data);
	conn.setAutoCommit(true);// 成功后事务提交
} catch (Exception e) {
	// 记录log
}

// 执行单个对象的插入，传入的参数是一个数组
try {
	runner.insert(conn, sql, new BeanHandler<T>(T.class), data);
	conn.setAutoCommit(true);// 成功后事务提交
} catch (Exception e) {
    // 记录log
}

// 执行修改和删除语句，都是update
try {
	runner.update // 参数还请见API文档
	conn.setAutoCommit(true);// 成功后事务提交
} catch (Exception e) {
    // 记录log
}
```

### Java正则表达式匹配中文

```
匹配中文字符的正则表达式：[u4e00-u9fa5]
匹配双字节字符(包括汉字在内)：[^x00-xff]
```

### Java正则表达式之捕获组()

 > 使用起来很简单，就是使用()来捕获你想要的数据，然后用Matcher进行匹配。
 
### GSON输出有缩进格式的JSON字符串

在开发过程中，无缩进格式的JSON字符串很难调试，但是GSON帮我们实现：

```
Gson gson = new GsonBuilder().setPrettyPrinting().create();
gson.toJson(obj);
```

### Iso8601规范的时间格式

调用阿里云接口时，其中要求的时间规范是Iso8601的：yyyy-MM-dd'T'HH:mm:ss'Z'

```
// 获取此时的ISO8601时间
new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'").format(new Date());
```

### 生成请求阿里云接口的URL

其实就是相当于封装了一个请求阿里云的工具类

```
/** GET请求 */
private static final String HTTP_GET = "GET";

/** POST请求 */
private static final String HTTP_POST = "POST";

/** 从properties中获取AccessKeyId */
private static final String ACCESS_KEY_ID = PropertiesUtils.getValue("aliyun.oss.accessKeyId");

/** 从properties中获取AccessKeySecret */
private static final String ACCESS_KEY_SECRET = PropertiesUtils.getValue("aliyun.oss.accessKeySecret");

public static String aliyunURL() throws Exception {

	Map<String, String> parameterMap = new HashMap<String, String>();
	// 加入请求公共参数
	parameterMap.put("Action", "阿里云SDK文档中的接口名称");
	parameterMap.put("Version", "2014-06-18");
	parameterMap.put("AccessKeyId", ACCESS_KEY_ID);
	parameterMap.put("Timestamp", formatIso8601Date());
	parameterMap.put("SignatureMethod", "HMAC-SHA1");
	parameterMap.put("SignatureVersion", "1.0");
	parameterMap.put("SignatureNonce", UUID.randomUUID().toString());
	parameterMap.put("Format", "JSON");// 还可以XML
	
	// 加入方法特有参数
	parameterMap.put("MediaInfoJobIds", "");// 不同接口名称的特有参数
	
	// 对参数进行排序
	List<String> sortedKeys = new ArrayList<String>(parameterMap.keySet());
	Collections.sort(sortedKeys);
	
	// 生成stringToSign字符
	final String SEPARATOR = "&";
	final String EQUAL = "=";
	StringBuilder stringToSign = new StringBuilder();
	stringToSign.append(HTTP_GET).append(SEPARATOR);
	stringToSign.append(percentEncode("/")).append(SEPARATOR);
	StringBuilder canonicalizedQueryString = new StringBuilder();
	for (String key : sortedKeys) {
		// 此处需要对key和value进行编码
		String value = parameterMap.get(key);
		canonicalizedQueryString.append(SEPARATOR).append(percentEncode(key)).append(EQUAL).append(percentEncode(value));
	}
	// 此处需要对canonicalizedQueryString进行编码
	stringToSign.append(percentEncode(canonicalizedQueryString.toString().substring(1)));

	final String ALGORITHM = "HmacSHA1";
	final String secret = ACCESS_KEY_SECRET;
	SecretKey key = new SecretKeySpec((secret + SEPARATOR).getBytes(), SignatureMethod.HMAC_SHA1);
	Mac mac = Mac.getInstance(ALGORITHM);
	mac.init(key);
	String signature = URLEncoder.encode(new String(new Base64().encode(mac.doFinal(stringToSign.toString().getBytes(StandardCharsets.UTF_8.name()))), StandardCharsets.UTF_8.name()), StandardCharsets.UTF_8.name());

	// 生成请求URL
	StringBuilder requestURL;
	requestURL = new StringBuilder("http://mts.aliyuncs.com?");
	requestURL.append(URLEncoder.encode("Signature", StandardCharsets.UTF_8.name())).append("=").append(signature);
	for (Map.Entry<String, String> e : parameterMap.entrySet()) {
		requestURL.append("&").append(percentEncode(e.getKey())).append("=").append(percentEncode(e.getValue()));
	}

	return requestURL.toString();
}

private static String formatIso8601Date() {
	return new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'").format(new Date());
}

private static String percentEncode(String value) throws UnsupportedEncodingException {
	if (value == null)
		return null;
	return URLEncoder.encode(value, StandardCharsets.UTF_8.name()).replace("+", "%20").replace("*", "%2A").replace("%7E", "~");
}
```

### 使用mp4parser获取mp4时长

首先引入jar包

```
<dependency>
    <groupId>com.googlecode.mp4parser</groupId>
    <artifactId>isoparser</artifactId>
    <!-- 最好是最新版本，在1.1.7版本，会有一个很严重的BUG -->
    <version>1.1.21</version>
  </dependency>
```

获取时长的java代码很简单

```
IsoFile isoFile = new IsoFile(videoPath);
MovieBox box = isoFile.getMovieBox();
int duration = (int) (box.getMovieHeaderBox().getDuration() / box.getMovieHeaderBox().getTimescale());
box.close();
isoFile.close();
```

Tips：在使用过程中，我遇到了一个BUG，在1.1.7版本获取完mp4时长之后，mp4资源不会被释放，这是一个非常严重的问题，久而久之会造成内存溢出；后来我将版本提高到1.1.21，就不会出现这个问题了~

