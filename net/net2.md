## http 中get和post的区别以及用java来具体实现

post和get的区别

1. GET在浏览器回退时是无害的，而POST会再次提交请求。

2. GET产生的URL地址可以被Bookmark，而POST不可以。

3. GET请求会被浏览器主动cache，而POST不会，除非手动设置。

4. GET请求只能进行url编码，而POST支持多种编码方式。

5. GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。

6. GET请求在URL中传送的参数是有长度限制的，而POST么有。

7. 对参数的数据类型，GET只接受ASCII字符，而POST没有限制。

8. GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。

9. GET参数通过URL传递，POST放在Request body中。

<font size=4 face=黑体 color=#ff0000>
下面这个是浏览器行为，不同浏览器POST发送数据次数不同</font>

```

GET和POST还有一个重大区别，简单的说：

GET产生一个TCP数据包；POST产生两个TCP数据包。

长的说：

对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；

而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。

也就是说，GET只需要汽车跑一趟就把货送到了，而POST得跑两趟，第一趟，先去和服务器打个招呼“嗨，我等下要送一批货来，你们打开门迎接我”，然后再回头把货送过去。

因为POST需要两步，时间上消耗的要多一点，看起来GET比POST更有效。因此Yahoo团队有推荐用GET替换POST来优化网站性能。

```


```java


public class GetPost {

	public String getTest(String url,String params){
		String resultString = "";
		String urlString = url + "?" + params;
		try {
			URL mUrl = new URL(urlString);
			URLConnection connection = mUrl.openConnection();
			connection.setRequestProperty("accept", "*/*");
			connection.setRequestProperty("connection", "Keep-Alive");
			connection.connect();
			BufferedReader in = new BufferedReader(
					new InputStreamReader(connection.getInputStream()));
			String line;            
			while ((line = in.readLine()) != null) {                
				resultString += line;            
			}		
			
		} catch (MalformedURLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return resultString;
	}
	
	public String postTest(String url,String params){
		String result = "";
		try {
			URL murl = new URL(url);
			URLConnection conn = murl.openConnection();
			
			conn.setRequestProperty("accept", "*/*");
			conn.setRequestProperty("connection", "Keep-Alive");
			//发送POST请求必须设置如下两行
			conn.setDoOutput(true);
			conn.setDoInput(true);
			
			//获取URLConnection对象对应的输出流			
			PrintWriter out = new PrintWriter(conn.getOutputStream());			
			//发送请求参数			
			out.print(params);			
			//flush输出流的缓冲			
			out.flush();
			
			BufferedReader in = new BufferedReader(
					new InputStreamReader(conn.getInputStream()));
			String line;            
			while ((line = in.readLine()) != null) {                
				result += line;            
			}	

		} catch (MalformedURLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return result;
	}

```