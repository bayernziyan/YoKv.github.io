---
title: Java Web基础
date: 2017-08-30 10:23:14
categories: 
- Java基础
---

## 概述
Java web是一种基于B\S（浏览器\服务器）架构的网络应用程序的技术实现。Java 在1999年推出了自己的动态Web技术Servlet。Servlet技术利用输出流动态生成HTML页面，能够以一种可移植的方法来提供动态的、面向用户的内容。　
Servlet 是 J2EE 标准的一部分，是一种运行在Web服务器端的小型Java程序，更具体的说，Servlet 是按照Servlet规范编写的一个Java类，用于交互式地浏览和修改数据，生成动态Web内容。要注意的是，由于 Servlet 是服务器端小程序，所以 Servlet 必须部署在 Servlet 容器中才能使用，例如 Tomcat，Jetty 等。
<!--more-->

## Http协议
HTTP是hypertext transfer protocol（超文本传输协议）的简写，它是TCP/IP协议的一个应用层协议，用于定义WEB浏览器与WEB服务器之间交换数据的过程。客户端连上web服务器后，若想获得web服务器中的某个web资源，需遵守一定的通讯格式，HTTP协议用于定义客户端与web服务器通迅的格式。

### http1.0,1.1,2.0
* 在HTTP1.0协议中，客户端与web服务器建立连接后，只能获得一个web资源。
* 在HTTP1.1协议，允许客户端与web服务器建立连接后，在一个连接上获取多个web资源。
* HTTP2.0 采用二进制格式传输数据，而非 HTTP1.x 的文本格式。二进制格式在协议的解析和优化扩展上带来更多的优势和可能。HTTP2.0 对消息头采用 HPACK 进行压缩传输，能够节省消息头占用的网络的流量。而 HTTP1.x 每次请求，都会携带大量冗余头信息，浪费了很多带宽资源。头压缩能够很好的解决该问题。多路复用，直白的说就是所有的请求都是通过一个 TCP 连接并发完成。HTTP1.x 虽然通过 pipeline 也能并发请求，但是多个请求之间的响应会被阻塞的，所以 pipeline 至今也没有被普及应用，而 HTTP2 做到了真正的并发请求。同时，流还支持优先级和流量控制。Server Push：服务端能够更快的把资源推送给客户端。例如服务端可以主动把 JS 和 CSS 文件推送给客户端，而不需要客户端解析 HTML 再发送这些请求。当客户端需要的时候，它已经在客户端了。HTTP2 主要是 HTTP1.x 在底层传输机制上的完全重构，HTTP2 是基本兼容 HTTP1.x 的语义的。

### http请求
请求行：请求行中的GET称之为请求方式，请求方式有：POST、GET、HEAD、OPTIONS、DELETE、TRACE、PUT
消息头：　
* accept:浏览器通过这个头告诉服务器，它所支持的数据类型
* Accept-Charset: 浏览器通过这个头告诉服务器，它支持哪种字符集
* Accept-Encoding：浏览器通过这个头告诉服务器，支持的压缩格式
* Accept-Language：浏览器通过这个头告诉服务器，它的语言环境
* Host：浏览器通过这个头告诉服务器，想访问哪台主机
* If-Modified-Since: 浏览器通过这个头告诉服务器，缓存数据的时间
* Referer：浏览器通过这个头告诉服务器，客户机是哪个页面来的  防盗链
* Connection：浏览器通过这个头告诉服务器，请求完后是断开链接还是何持链接

## Tomcat及Nginx
### Tomcat
Tomcat是Apache在符合J2EE的JSP、Servlet标准下开发的一个JSP服务器，tomcat侧重于servlet引擎，如果以standalone方式运行，可以作为web服务器来使用，支持JSP；tomcat是由java语言开发的，需要在java虚拟机上进行工作。
虚拟目录映射，todo。
[详细配置](http://bengbengtu.blog.51cto.com/9505633/1707207)

### Nginx
Nginx是一个网页服务器，它能反向代理HTTP, HTTPS, SMTP, POP3, IMAP的协议链接，以及一个负载均衡器和一个HTTP缓存。
特性：
* 可针对静态资源高速高并发访问及缓存；
* 可使用反向代理加速，并且可进行数据缓存；
* 具有简单负载均衡、节点健康检查和容错功能；
* 支持远程FastCGI、Uwsgi、SCGI、Memcached Servers的加速和缓存；
* 支持SSL、TLS、SNI；
* 具有模块化的架构：过滤器包括gzip压缩、ranges支持、chunked响应、XSLT、SSI及图像缩放等功能。在SSI过滤器中，一个包含多个SSI的页面，如果FastCGI或反向代理处理，可被并行处理；
* 它具备的其他WWW服务特性：
* 支持基于名字、端口及IP的多虚拟主机站点；
* 支持Keep-alived和pipelined连接；
* 可进行修改Nginx配置，并且在代码上线时，可平滑重启，不中断业务访问；
* 可自定义访问日志格式，临时缓冲些日志操作，快速日志轮询及通过rsyslog处理日志；
* 可利用信号控制Nginx进程；
* 支持 3xx-5xx HTTP状态码重定向；
* 支持rewrite模块，支持URI重写及正则表达式匹配；
* 支持基于客户端IP地址和HTTP基本认证的访问控制；
* 支持PUT、DELETE、MKCOL、COPY及MOVE等较特殊的HTTP请求方法；
* 支持FLV流和MP4流技术产品应用；
* 支持HTTP响应速率限制；
* 支持同一IP地址的并发连接或请求数连接；
* 支持邮件服务器代理；

## Servlet对象

### 运行过程
1. Web服务器首先检查是否已经装载并创建了该Servlet的实例对象。如果是，则直接执行第4步，否则，执行第2步。
2. 装载并创建该Servlet的一个实例对象。 
3. 调用Servlet实例对象的init()方法。
4. 创建一个用于封装HTTP请求消息的HttpServletRequest对象和一个代表HTTP响应消息的HttpServletResponse对象，然后调用Servlet的service()方法并将请求和响应对象作为参数传递进去。
5. WEB应用程序被停止或重新启动之前，Servlet引擎将卸载Servlet，并在卸载之前调用Servlet的destroy()方法。 
### Servlet类层次
Servlet接口SUN公司定义了两个默认实现类，分别为：GenericServlet、HttpServlet。
HttpServlet指能够处理HTTP请求的servlet，它在原有Servlet接口上添加了一些与HTTP协议处理方法，它比Servlet接口的功能更为强大。因此开发人员在编写Servlet时，通常应继承这个类，而避免直接去实现Servlet接口。
HttpServlet在实现Servlet接口时，覆写了service方法，该方法体内的代码会自动判断用户的请求方式，如为GET请求，则调用HttpServlet的doGet方法，如为Post请求，则调用doPost方法。因此，开发人员在编写Servlet时，通常只需要覆写doGet或doPost方法，而不要去覆写service方法。

### 线程安全
定义成全局变量，多个线程并发地访问这个变量，存在线程安全问题。
针对Servlet的线程安全问题，Sun公司是提供有解决方案的：让Servlet去实现一个SingleThreadModel接口，如果某个Servlet实现了SingleThreadModel接口，那么Servlet引擎将以单线程模式来调用其service方法。
查看Sevlet的API可以看到，SingleThreadModel接口中没有定义任何方法和常量，在Java中，把没有定义任何方法和常量的接口称之为标记接口，经常看到的一个最典型的标记接口就是"Serializable"，这个接口也是没有定义任何方法和常量的，标记接口在Java中有什么用呢？主要作用就是给某个对象打上一个标志，告诉JVM，这个对象可以做什么，比如实现了"Serializable"接口的类的对象就可以被序列化，还有一个"Cloneable"接口，这个也是一个标记接口，在默认情况下，Java中的对象是不允许被克隆的，就像现实生活中的人一样，不允许克隆，但是只要实现了"Cloneable"接口，那么对象就可以被克隆了。
让Servlet实现了SingleThreadModel接口，只要在Servlet类的定义中增加实现SingleThreadModel接口的声明即可。  
对于实现了SingleThreadModel接口的Servlet，Servlet引擎仍然支持对该Servlet的多线程并发访问，其采用的方式是产生多个Servlet实例对象，并发的每个线程分别调用一个独立的Servlet实例对象。
实现SingleThreadModel接口并不能真正解决Servlet的线程安全问题，因为Servlet引擎会创建多个Servlet实例对象，而真正意义上解决多线程安全问题是指一个Servlet实例对象被多个线程同时调用的问题。事实上，在Servlet API 2.4中，已经将SingleThreadModel标记为Deprecated（过时的）。  

### 初始化
在Servlet的配置文件web.xml中，可以使用一个或多个<init-param>标签为servlet配置一些初始化参数。
```xml
<servlet>
    <servlet-name>ServletConfigDemo1</servlet-name>
    <servlet-class>gacl.servlet.study.ServletConfigDemo1</servlet-class>
    <!--配置ServletConfigDemo1的初始化参数 -->
    <init-param>
        <param-name>name</param-name>
        <param-value>gacl</param-value>
    </init-param>
     <init-param>
        <param-name>password</param-name>
        <param-value>123</param-value>
    </init-param>
    <init-param>
        <param-name>charset</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</servlet>

```
获取相应的值

```java
 /**
     * 定义ServletConfig对象来接收配置的初始化参数
     */
    private ServletConfig config;
    
    /**
     * 当servlet配置了初始化参数后，web容器在创建servlet实例对象时，
     * 会自动将这些初始化参数封装到ServletConfig对象中，并在调用servlet的init方法时，
     * 将ServletConfig对象传递给servlet。进而，程序员通过ServletConfig对象就可以
     * 得到当前servlet的初始化参数信息。
     */
    @Override
    public void init(ServletConfig config) throws ServletException {
        this.config = config;
    }

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        //获取在web.xml中配置的初始化参数
        String paramVal = this.config.getInitParameter("name");//获取指定的初始化参数
        response.getWriter().print(paramVal);
        
        response.getWriter().print("<hr/>");
        //获取所有的初始化参数
        Enumeration<String> e = config.getInitParameterNames();
        while(e.hasMoreElements()){
            String name = e.nextElement();
            String value = config.getInitParameter(name);
            response.getWriter().print(name + "=" + value + "<br/>");
        }
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        this.doGet(request, response);
    }

```

### ServletContext对象
WEB容器在启动时，它会为每个WEB应用程序都创建一个对应的ServletContext对象，它代表当前web应用。
ServletConfig对象中维护了ServletContext对象的引用，开发人员在编写servlet时，可以通过ServletConfig.getServletContext方法获得ServletContext对象。
由于一个WEB应用中的所有Servlet共享同一个ServletContext对象，因此Servlet对象之间可以通过ServletContext对象来实现通讯。ServletContext对象通常也被称之为context域对象。

#### 用servletContext实现请求转发
```java
        String data = "<h1><font color='red'>abcdefghjkl</font></h1>";
        response.getOutputStream().write(data.getBytes());
        ServletContext context = this.getServletContext();//获取ServletContext对象
        RequestDispatcher rd = context.getRequestDispatcher("/servlet/ServletContextDemo5");//获取请求转发对象(RequestDispatcher)
        rd.forward(request, response);//调用forward方法实现请求转发
```

#### 使用servletContext读取资源文件
```java
 		InputStream in = this.getServletContext().getResourceAsStream("/WEB-INF/classes/gacl/servlet/study/db4.properties");
        Properties prop = new Properties();
        prop.load(in);
        String driver = prop.getProperty("driver");
```

#### 使用类装载器读取资源文件
```java
  		 ClassLoader loader = ServletContextDemo7.class.getClassLoader();
         //用类装载器读取src目录下的db1.properties配置文件
         InputStream in = loader.getResourceAsStream("db1.properties");
         Properties prop = new Properties();
         prop.load(in);
         String driver = prop.getProperty("driver");
```

对于不经常变化的数据，在servlet中可以为其设置合理的缓存时间值，以避免浏览器频繁向服务器发送请求，提升服务器的性能。
```java
response.setDateHeader("expires",System.currentTimeMillis() + 24 * 3600 * 1000);
```

### web应用初始化
在web.xml文件中使用<context-param>标签配置WEB应用的初始化参数
```xml
      <context-param>
          <param-name>url</param-name>
          <param-value>jdbc:mysql://localhost:3306/test</param-value>
      </context-param>

```

servlet中获取值
```java
 
         ServletContext context = this.getServletContext();
         //获取整个web站点的初始化参数
         String contextInitParam = context.getInitParameter("url");
         response.getWriter().print(contextInitParam);

```


### HttpServletResponse
HttpServletResponse对象代表服务器的响应。这个对象中封装了向客户端发送数据、发送响应头，发送响应状态码的方法。查看HttpServletResponse的API，可以看到这些相关的方法。
* 使用OutputStream流向客户端浏览器输出中文数据
* 使用PrintWriter流向客户端浏览器输出中文数据

文件下载功能的实现思路：
　　1.获取要下载的文件的绝对路径
　　2.获取要下载的文件名
　　3.设置content-disposition响应头控制浏览器以下载的形式打开文件
　　4.获取要下载的文件输入流
　　5.创建数据缓冲区
　　6.通过response对象获取OutputStream流
　　7.将FileInputStream流写入到buffer缓冲区
　　8.使用OutputStream将缓冲区的数据输出到客户端浏览器

使用PrintWriter流处理字节数据，会导致数据丢失，这一点千万要注意，因此在编写下载文件功能时，要使用OutputStream流，避免使用PrintWriter流，因为OutputStream流是字节流，可以处理任意类型的数据，而PrintWriter流是字符流，只能处理字符数据，如果用字符流处理字节数据，会导致数据丢失。

请求重定向指：一个web资源收到客户端请求后，通知客户端去访问另外一个web资源，这称之为请求重定向。

实现方式：response.sendRedirect(String location)，即调用response对象的sendRedirect方法实现请求重定向
sendRedirect内部的实现原理：使用response设置302状态码和设置location响应头实现重定向

getOutputStream和getWriter方法分别用于得到输出二进制数据、输出文本数据的ServletOuputStream、Printwriter对象。
getOutputStream和getWriter这两个方法互相排斥，调用了其中的任何一个方法后，就不能再调用另一方法。  
Servlet程序向ServletOutputStream或PrintWriter对象中写入的数据将被Servlet引擎从response里面获取，Servlet引擎将这些数据当作响应消息的正文，然后再与响应状态行和各响应头组合后输出到客户端。 
Serlvet的service方法结束后，Servlet引擎将检查getWriter或getOutputStream方法返回的输出流对象是否已经调用过close方法，如果没有，Servlet引擎将调用close方法关闭该输出流对象。

### HttpServletRequest
HttpServletRequest对象代表客户端的请求，当客户端通过HTTP协议访问服务器时，HTTP请求头中的所有信息都封装在这个对象中，通过这个对象提供的方法，可以获得客户端请求的所有信息。

获得客户机请求参数(客户端提交的数据)
* getParameter(String)方法(常用)
* getParameterValues(String name)方法(常用)
* getParameterNames()方法(不常用)
* getParameterMap()方法(编写框架时常用)

#### 中文数据乱码问题
1、如果提交方式为post，想不乱码，只需要在服务器端设置request对象的编码即可，客户端以哪种编码提交的，服务器端的request对象就以对应的编码接收，比如客户端是以UTF-8编码提交的，那么服务器端request对象就以UTF-8编码接收(request.setCharacterEncoding("UTF-8"))

2、如果提交方式为get，设置request对象的编码是无效的，request对象还是以默认的ISO8859-1编码接收数据，因此要想不乱码，只能在接收到数据后再手工转换，步骤如下：

　　1).获取获取客户端提交上来的数据，得到的是乱码字符串,data="???è?????"

　　 String data = request.getParameter("paramName"); 

　　2).查找ISO8859-1码表，得到客户机提交的原始数据的字节数组

　　 byte[] source = data.getBytes("ISO8859-1"); 

　　3).通过字节数组以指定的编码构建字符串，解决乱码

　　 data = new String(source, "UTF-8"); 

通过字节数组以指定的编码构建字符串，这里指定的编码是根据客户端那边提交数据时使用的字符编码来定的，如果是GB2312，那么就设置成data = new String(source, "GB2312")，如果是UTF-8，那么就设置成data = new String(source, "UTF-8")

#### 请求转发
```java
RequestDispatcher reqDispatcher =this.getServletContext().getRequestDispatcher("/test.jsp");
reqDispatcher.forward(request, response);

```

一个web资源收到客户端请求后，通知服务器去调用另外一个web资源进行处理，称之为请求转发/307。
一个web资源收到客户端请求后，通知浏览器去访问另外一个web资源进行处理，称之为请求重定向/302。

## 会话管理
### Cookie
Cookie是客户端技术，程序把每个用户的数据以cookie的形式写给用户各自的浏览器。当用户使用浏览器再去访问服务器中的web资源时，就会带着各自的数据去。这样，web资源处理的就是用户各自的数据了。

| 方法 |  描述 |
| ---- | ----:|	
| Cookie(String name, String value)  |实例化Cookie对象，传入cooke名称和cookie的值 |	
| public String getName()   |  取得Cookie的名字	 |
| public String getValue()   | 取得Cookie的值 |
| public void setValue(String newValue)  |设置Cookie的值 |
| public void setMaxAge(int expiry)     |   设置Cookie的最大保存时间，即cookie的有效期，当服务器给浏览器回送一个cookie时，如果在服务器端没有调用setMaxAge方法设置cookie的有效期，那么cookie的有效期只在一次会话过程中有效，用户开一个浏览器，点击多个超链接，访问服务器多个web资源，然后关闭浏览器，整个过程称之为一次会话，当用户关闭浏览器，会话就结束了，此时cookie就会失效，如果在服务器端使用setMaxAge方法设置了cookie的有效期，比如设置了30分钟，那么当服务器把cookie发送给浏览器时，此时cookie就会在客户端的硬盘上存储30分钟，在30分钟内，即使浏览器关了，cookie依然存在，在30分钟内，打开浏览器访问服务器时，浏览器都会把cookie一起带上，这样就可以在服务器端获取到客户端浏览器传递过来的cookie里面的信息了，这就是cookie设置maxAge和不设置maxAge的区别，不设置maxAge，那么cookie就只在一次会话中有效，一旦用户关闭了浏览器，那么cookie就没有了，那么浏览器是怎么做到这一点的呢，我们启动一个浏览器，就相当于启动一个应用程序，而服务器回送的cookie首先是存在浏览器的缓存中的，当浏览器关闭时，浏览器的缓存自然就没有了，所以存储在缓存中的cookie自然就被清掉了，而如果设置了cookie的有效期，那么浏览器在关闭时，就会把缓存中的cookie写到硬盘上存储起来，这样cookie就能够一直存在了。   | 
| public int getMaxAge()     |   获取Cookies的有效期   | 
| public void setPath(String uri)     |  	设置cookie的有效路径，比如把cookie的有效路径设置为"/xdp"，那么浏览器访问"xdp"目录下的web资源时，都会带上cookie，再比如把cookie的有效路径设置为"/xdp/gacl"，那么浏览器只有在访问"xdp"目录下的"gacl"这个目录里面的web资源时才会带上cookie一起访问，而当访问"xdp"目录下的web资源时，浏览器是不带cookie的    | 
| public String getPath()     |  	获取cookie的有效路径    | 
| public void setDomain(String pattern)    |  设置cookie的有效域    | 
| public String getDomain()     |  获取cookie的有效域    | 


### Session
Session是服务器端技术，利用这个技术，服务器在运行时可以为每一个用户的浏览器创建一个其独享的session对象，由于session为用户浏览器独享，所以用户在访问服务器的web资源时，可以把各自的数据放在各自的session中，当用户再去访问服务器中的其它web资源时，其它web资源再从用户各自的session中取出数据为用户服务。
服务器创建session出来后，会把session的id号，以cookie的形式回写给客户机，这样，只要客户机的浏览器不关，再去访问服务器时，都会带着session的id号去，服务器发现客户机浏览器带session id过来了，就会使用内存中与之对应的session为之服务。

浏览器禁用了cookie后，就可以用URL重写这种解决方案解决Session数据共享问题
response.encodeRedirectURL(java.lang.String url) 用于对sendRedirect方法后的url地址进行重写
response.encodeURL(java.lang.String url)用于对表单action和超链接的url地址进行重写

session对象的创建时机：在程序中第一次调用request.getSession()方法时就会创建一个新的Session，可以用isNew()方法来判断Session是不是新创建的
session对象的销毁时机：session对象默认30分钟没有使用，则服务器会自动销毁session，在web.xml文件中可以手工配置session的失效时间
```xml
 <!-- 设置Session的有效时间:以分钟为单位-->
     <session-config>
         <session-timeout>15</session-timeout>
     </session-config>
```

#### 防止重复提交
在服务器端生成一个唯一的随机标识号，专业术语称为Token(令牌)，同时在当前用户的Session域中保存这个Token。然后将Token发送到客户端的Form表单中，在Form表单中使用隐藏域来存储这个Token，表单提交的时候连同这个Token一起提交到服务器端，然后在服务器端判断客户端提交上来的Token与服务器端生成的Token是否一致，如果不一致，那就是重复提交了，此时服务器端就可以不处理重复提交的表单。如果相同则处理表单提交，处理完后清除当前用户的Session域中存储的标识号。

### JSP
略！

### Servlet3.0注解方式
```java

/**
 * 注解WebServlet用来描述一个Servlet
 * 属性name描述Servlet的名字,可选
 * 属性urlPatterns定义访问的URL,或者使用属性value定义访问的URL.(定义访问的URL是必选属性)
 */
@WebServlet(name="Servlet3Demo",urlPatterns="/Servlet3Demo")
public class ServletDemo extends HttpServlet {
    
    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.getWriter().write("Hello Servlet3.0");
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        this.doGet(request, response);
    }
}

/*
 * 完成了一个使用注解描述的Servlet程序开发。
　　使用@WebServlet将一个继承于javax.servlet.http.HttpServlet的类定义为Servlet组件。
　　@WebServlet有很多的属性：
    　　1、asyncSupported：    声明Servlet是否支持异步操作模式。
    　　2、description：　　    Servlet的描述。
    　　3、displayName：       Servlet的显示名称。
    　　4、initParams：        Servlet的init参数。
    　　5、name：　　　　       Servlet的名称。
    　　6、urlPatterns：　　   Servlet的访问URL。
    　　7、value：　　　        Servlet的访问URL。
　　Servlet的访问URL是Servlet的必选属性，可以选择使用urlPatterns或者value定义。
　　像上面的Servlet3Demo可以描述成@WebServlet(name="Servlet3Demo",value="/Servlet3Demo")。
　　也定义多个URL访问：
　　如@WebServlet(name="Servlet3Demo",urlPatterns={"/Servlet3Demo","/Servlet3Demo2"})
　　或者@WebServlet(name="AnnotationServlet",value={"/Servlet3Demo","/Servlet3Demo2"})
 *
 */

```


## Filter
Filter也称之为过滤器，它是Servlet技术中最激动人心的技术，WEB开发人员通过Filter技术，对web服务器管理的所有web资源：例如Jsp, Servlet, 静态图片文件或静态 html 文件等进行拦截，从而实现一些特殊的功能。例如实现URL级别的权限访问控制、过滤敏感词汇、压缩响应信息等一些高级功能。
Servlet API中提供了一个Filter接口，开发web应用时，如果编写的Java类实现了这个接口，则把这个java类称之为过滤器Filter。通过Filter技术，开发人员可以实现用户在访问某个目标资源之前，对访问的请求和响应进行拦截

Filter接口中有一个doFilter方法，当我们编写好Filter，并配置对哪个web资源进行拦截后，WEB服务器每次在调用web资源的service方法之前，都会先调用一下filter的doFilter方法，因此，在该方法内编写代码可达到如下目的：
* 调用目标资源之前，让一段代码执行。
* 是否调用目标资源（即是否让用户访问web资源）。
* 调用目标资源之后，让一段代码执行。

在一个web应用中，可以开发编写多个Filter，这些Filter组合起来称之为一个Filter链。
web服务器根据Filter在web.xml文件中的注册顺序，决定先调用哪个Filter，当第一个Filter的doFilter方法被调用时，web服务器会创建一个代表Filter链的FilterChain对象传递给该方法。在doFilter方法中，开发人员如果调用了FilterChain对象的doFilter方法，则web服务器会检查FilterChain对象中是否还有filter，如果有，则调用第2个filter，如果没有，则调用目标资源。

Filter生命周期和配置参照Servlet

## Listener
监听器是一个专门用于对其他对象身上发生的事件或状态改变进行监听和相应处理的对象，当被监视的对象发生情况时，立即采取相应的行动。监听器其实就是一个实现特定接口的普通java程序，这个程序专门用于监听另一个java对象的方法调用或属性改变，当被监听对象发生上述事件后，监听器某个方法立即被执行。

在Servlet规范中定义了多种类型的监听器，它们用于监听的事件源分别为ServletContext，HttpSession和ServletRequest这三个域对象
* ServletContextListener
* HttpSessionListener
* ServletRequestListener
* ServletContextAttributeListener
* HttpSessionAttributeListener
* ServletRequestAttributeListener
* HttpSessionBindingListener
* HttpSessionActivationListener


在web.xml文件中注册监听器
```xml
   <listener>
       <description>ServletContextListener监听器</description>
       <!--实现了ServletContextListener接口的监听器类 -->
       <listener-class>me.gacl.web.listener.MyServletContextListener</listener-class>
   </listener>
```

## MVC
MVC模型：是一种架构型的模式，本身不引入新功能，只是帮助我们将开发的结构组织的更加合理，使展示与模型分离、流程控制逻辑、业务逻辑调用与展示逻辑分离。

* Model（模型）：数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型(domain)或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据） 和 服务层（行为）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。
* View（视图）：负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。
* Controller（控制器）：接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。 也就是说控制器做了个调度员的工作。
