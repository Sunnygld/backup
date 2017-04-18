---
title: 有道API应用
date: 2016-11-20
tags: http
categories: java
---

## HTTP连接

使用HttpClient进行对URL的发送
<!--more -->
````JAVA
	public String Connect() {
		try {
			HttpClient client = new DefaultHttpClient();
			// 创建get

			String a = "http://fanyi.youdao.com/openapi.do?keyfrom=dverere&key=2031067193&type=data&doctype=json&version=1.1&q=";

			// String strUrl = URLEncoder.encode(strUrl1, "UTF-8");

			String strUrl1 = a + ToTrans;

			// 查找了一些网上资料，说地址中涉及了特殊字符，如‘｜’‘&’等。所以不能直接用String代替URI来访问。
			// 必须采用%0xXX方式来替代特殊字符。但这种办法不直观。所以只能先把String转成URL，再能过URL生成URI的方法来解决问题。
			URL url = new URL(strUrl1);
			URI uri = new URI(url.getProtocol(), url.getHost(), url.getPath(), url.getQuery(), null);
			HttpGet httpget = new HttpGet(uri);

			// System.out.println(url);
			// 服务端返回数据
			HttpResponse response = client.execute(httpget);// 发送 没有封装线程的对象
															// 就不需要回调
			InputStream input = response.getEntity().getContent();

			strResult = EntityUtils.toString(response.getEntity());

			//System.out.println(strResult);

			input.close();
		} catch (Exception e) {
			e.printStackTrace();
		}
		return strResult;
	}
````
- 注意URL一直非法问题的解决方法
- 同时此方法需要三个外部依赖包 apache-httpcomponents-httpclient,commons-logging-1.0.4,httpcore-4.2.2

## GSON

使用GSON解析JSON数据

首先需要自定义解析规则,网上也有很多网站提供在线的JSON解析,以下是对有道API的JSON数据的解析

````JAVA
class Person {
	private List<String> translation;
	private String querye;
	private int errorCode;
	private Basic basic;
	private List<Web> web;

	public List<String> getTranslation() {
		return translation;
	}

	public void setTranslation(List<String> translation) {
		this.translation = translation;
	}

	public int getErrorCode() {
		return errorCode;
	}

	public void setErrorCode(int errorCode) {
		this.errorCode = errorCode;
	}

	public String getQuerye() {
		return querye;
	}

	public void setQuerye(String querye) {
		this.querye = querye;
	}

	public Basic getBasic() {
		return basic;
	}

	public void setBasic(Basic basic) {
		this.basic = basic;
	}

	public List<Web> getWeb() {
		return web;
	}

	public void setWeb(List<Web> web) {
		this.web = web;
	}

	public class Basic {
		private List<String> explains;
		
		// private String us-phonetic;
		private String phonetic;
		//private String uk-phonetic;

		public String getPhonetic() {
			return phonetic;
		}

		public List<String> getExplains() {
			return explains;
		}

		public void setExplains(List<String> explains) {
			this.explains = explains;
		}

		public void setPhonetic(String phonetic) {
			this.phonetic = phonetic;
		}

		// @Override
		// public String toString() {
		// return "basic:[explains = " + explains + ", phonetic = " + phonetic +
		// "]";
		// }
	}

	public class Web {
		private List<String> value;
		private String key;

		public List<String> getValue() {
			//return value.get(0);
			return value;
		}

		public void setValue(List<String> value) {
			this.value = value;
		}

		public String getKey() {
			return key;
		}

		public void setKey(String key) {
			this.key = key;
		}
	}

}
````

- 需要注意的是,GSON会通过生成对象的方式调用得到的数据,所以可以使用自定的泛型方法来实现对不同JSON数据的解析

````JAVA
class GsonUtil {
	// 将Json数据解析成相应的映射对象
	public static <T> T parseJsonWithGson(String jsonData, Class<T> type) {
		Gson gson = new Gson();
		T result = gson.fromJson(jsonData, type);
		return result;
	}

	// 将Json数组解析成相应的映射对象列表
	public static <T> List<T> parseJsonArrayWithGson(String jsonData, Class<T> type) {
		Gson gson = new Gson();
		List<T> result = gson.fromJson(jsonData, new TypeToken<List<T>>() {
		}.getType());
		return result;
	}
}
````

- 也可以直接生成对象进行使用

````JAVA
Gson gson = new Gson();
Person person = gson.fromJson(res, Person.class);
````

- 对于ListString与String类型之间转化的问题,直接使用return value.get(0)即可,value为<List>String类型.
- 当然GSON同样需要外部依赖包作为支持:gson-2.2.4

## ListString与String

当然也有其他的方法实现这个问题

[原文链接](http://blog.csdn.net/shareye1992/article/details/50036615)

````JAVA
public static String listToString(List<String> list){
	      if(list==null){
	      return null;
	   }
	   StringBuilder result = new StringBuilder();
	   boolean first = true;
	   //第一个前面不拼接","
	   for(String string :list) {
	      if(first) {
	         first=false;
	      }else{
	         result.append(",");
	      }
	      result.append(string);
	   }
	   return result.toString();
	}
````
- 反过来也可以

首先String类没有提供直接转换出List的功能,String提供了一个根据字符来分割字符串的功能，但是分割的结果是String[]类型的，如果我们需要的是String[]类型，那么正好直接使用，需要List可以使用如下的方法：

````JAVA
private List<String> stringToList(String strs){
	String str[] = strs.split(",");
	return Arrays.asList(str);
}
````

或者获取String[]对象后自己迭代一遍后返回，但是有现成的方法，还是直接用吧！

## 键盘监听

通过KeyListener借口来实现

````JAVA
JButton jb;
jb.addActionListener(this);
@Override
	public void keyTyped(KeyEvent e) {
		//按下某个键时调用此方法。
	}

	@Override
	public void keyPressed(KeyEvent e) {
		//释放某个键时调用此方法
	}

	@Override
	public void keyReleased(KeyEvent e) {
		//键入某个键时调用此方法。
		if(e.getKeyCode()==KeyEvent.VK_ENTER){

}
````

## 窗口居中

实现初始化窗口时,出现在屏幕中央的位置
````JAVA
  Dimension screensize = Toolkit.getDefaultToolkit().getScreenSize();
		int width = (int)screensize.getWidth();
		int height = (int)screensize.getHeight();
		setLocation((width-300)/2,(height-200)/2);
````
