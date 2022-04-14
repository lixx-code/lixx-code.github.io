---
layout: post
title: "一文看懂HttpServletResponse"
date: 2022-04-14
author: "li.xx"
tags: 
  - JAVA
  - Spring
---

> 原文链接：blog.ouyangsihai.cn >> [一文看懂HttpServletResponse](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.ouyangsihai.cn%2F%2Fyi-wen-kan-dong-httpservletresponse.html)

Web服务器收到客户端的http请求，会针对每一次请求，分别创建一个用于代表请求的request对象、和代表响应的response对象。获取网页提交过来的数据，只需要找request对象就行了。要向网页输出数据，只需要找response对象。

### 一，HttpServletResponse对象介绍

![img](https:////upload-images.jianshu.io/upload_images/5824016-59d786bc8f7eda6e.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1048/format/webp)

1.jpg

HttpServletResponse对象代表服务器的响应。这个对象中封装了向客户端发送数据、发送响应头，发送响应状态码的方法。

### 二，HttpServletResponse对象常用方法

**1，负责向客户端(浏览器)发送数据的相关方法 **

| getOutputStream() | 该方法用于返回Servlet引擎创建的字节输出流对象，Servlet程序可以按字节形式输出响应正文。 |
| ----------------- | ------------------------------------------------------------ |
| getWriter()       | 该方法用于返回Servlet引擎创建的字符输出流对象，Servlet程序可以按字符形式输出响应正文。 |

**注意：**

1，getOutputStream()和getWriter()这两个方法**互相排斥**，调用了其中的任何一个方法后，就不能再调用另一方法。

2，getOutputStream()返回的字节输出流对象，类型为：ServletOutputStream，直接输出字节数组中的**二进制数据**。

3，getWriter()方法将Servlet引擎的数据缓冲区包装成P：rintWriter类型的字符输出流对象后返回，PrintWriter对象只能输出**字符文本内容**。

**2，负责向客户端(浏览器)发送响应头的相关方法**

| addDateHeader(String name ,long date) | 该方法是设置只有一个值且值的类型为long类型的响应头,例如expies响应头,表示过期时间 |
| ------------------------------------- | ------------------------------------------------------------ |
| addHeader(String name,String value)   | 该方法是设置有多个值的响应头,参数name表示响应头名称,参数value表示响应头的值 |
| addIntHeader(String name,int value)   | 该方法是设置有多个值且值的类型为int类型的响应头              |
| setHeader(String name,String value)   | 该方法是设置只有一个值的响应头,参数name表示响应头名称,参数value表示响应头的值 |
| setDateHeader(String name,long value) | 该方法是设置只有一个值且值的类型为long类型的响应头,例如expies响应头,表示过期时间 |
| setIntHeader(String name,int value)   | 该方法是设置只有一个值且值的类型为int类型的响应头,例如Content-Length响应头,该响应头是代表响应内容有多少字节数 |



```cpp
//设置Content-Type响应头
resp.setHeader("Content-Type","text/html;charset=utf-8");
//添加一个响应头：xxx是表示某一响应头
resp.addHeader("xxx", "123");
//通知客户端响应内容长度为888个字节
resp.setIntHeader("Context-Length", 5);
//设置过期时间为:5000毫秒
resp.setDateHeader("", 5000);
```

**3，负责向客户端(浏览器)发送响应状态码的相关方法**

| setStatus(int value) | 设置临时定向响应码 |
| -------------------- | ------------------ |
|                      |                    |

**4，响应状态码的常量**

HttpServletResponse定义了很多状态码的常量(具体可以查看Servlet的API)，当需要向客户端发送响应状态码时，可以使用这些常量，避免了直接写数字，常见的状态码对应的常量：

| SC_NOT_FOUND             | 状态码404对应的常量 |
| ------------------------ | ------------------- |
| SC_OK                    | 状态码200对应的常量 |
| SC_INIERNAL_SERVER_ERROR | 状态码500对应的常量 |

### 三，HttpServletResponse对象常见应用

##### 1，向客户端浏览器输出数据

**例子1：**使用**OutputStream**流向客户端浏览器输出数据



```java
public class HelloResponse extends HttpServlet{
 
    private static final long serialVersionUID = 3903946972744326948L;
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置Content-Type响应头，编码格式为UTF-8
        resp.setHeader("Content-Type","text/html;charset=utf-8");
        outPutOutputStream(resp);
    }
    /**
     * 
     * @方法名: outPutOutputStream
     * @描述: 使用OutputStream输出流输出数据
     * @param resp
     * @throws IOException 
     * @创建人 zender
     */
    private void outPutOutputStream(HttpServletResponse resp) throws IOException{
        String data = "输出的内容";
        //获取OutputStream输出流
        OutputStream outputStream = resp.getOutputStream();
        //将字符转换成字节数组，指定以UTF-8编码进行转换
        byte[] dataByteArr = data.getBytes("UTF-8");
        //使用OutputStream流向客户端输出字节数组
        outputStream.write(dataByteArr);
    }
}
```

运行结果如下：

![img](https:////upload-images.jianshu.io/upload_images/5824016-eb303a80af96b952.png?imageMogr2/auto-orient/strip|imageView2/2/w/475/format/webp)

image

**例子2：**使用**PrintWriter**流向客户端浏览器输出数据



```java
public class PrintWriterResponse extends HttpServlet{
 
    private static final long serialVersionUID = 3903946972744326948L;
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置编码格式为UTF-8
        resp.setCharacterEncoding("UTF-8");
        //通过设置响应头控制浏览器以UTF-8的编码显示数据，如果不加这句话，那么浏览器显示的将是乱码
        resp.setHeader("content-type", "text/html;charset=UTF-8");
        outPutPrintWriter(resp);
    }
    /**
     * 
     * @方法名: outPutPrintWriter
     * @描述: 使用PrintWriter输出流输出数据
     * @param resp
     * @throws IOException 
     * @创建人 zender
     */
    private void outPutPrintWriter(HttpServletResponse resp) throws IOException{
        String data = "输出的内容";
        //获取PrintWriter输出流
        PrintWriter out = resp.getWriter();
        //使用PrintWriter流向客户端输出字符
        out.write(data);
    }
}
```

运行结果如下：

![img](https:////upload-images.jianshu.io/upload_images/5824016-f7bc8d7a287359ad.png?imageMogr2/auto-orient/strip|imageView2/2/w/480/format/webp)

image

**注意：**

1，在获取PrintWriter输出流之前首先使用"response.setCharacterEncoding(charset)"设置字符以什么样的编码输出到浏览器，再使用response.getWriter();获取PrintWriter输出流，这两个步骤不能颠倒。

2，在开发过程中，如果希望服务器输出什么浏览器就能看到什么，那么在服务器端都要以字符串的形式进行输出。

##### 2，文件下载

文件下载功能是web开发中经常使用到的功能，使用HttpServletResponse对象就可以实现文件的下载。

文件下载功能的实现思路：

- 获取要下载的文件的绝对路径
- 获取要下载的文件名
- 设置content-disposition响应头控制浏览器以下载的形式打开文件
- 获取要下载的文件输入流
- 创建数据缓冲区
- 通过response对象获取OutputStream流
- 将FileInputStream流写入到buffer缓冲区
- 使用OutputStream将缓冲区的数据输出到客户端浏览器



```java
public class FileDownloadResponse extends HttpServlet{
 
    private static final long serialVersionUID = 3903946972744326948L;
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取要下载的文件的绝对路径
        String realPath = this.getServletContext().getRealPath("/下载.png");
        //获取要下载的文件名
        String fileName = realPath.substring(realPath.lastIndexOf("\\")+1);
        //设置content-disposition响应头控制浏览器以下载的形式打开文件
        //中文文件名要使用URLEncoder.encode方法进行编码，否则会出现文件名乱码
        resp.setHeader("content-disposition", "attachment;filename=" + URLEncoder.encode(fileName, "UTF-8"));
        //获取文件输入流
        InputStream in = new FileInputStream(realPath);
        int len = 0;
        byte[] buffer = new byte[1024];
        OutputStream out = resp.getOutputStream();
        while ((len = in.read(buffer)) > 0) {
            out.write(buffer,0,len);//将缓冲区的数据输出到客户端浏览器
        }
        in.close();
        out.close();
    }
}
```

![img](https:////upload-images.jianshu.io/upload_images/5824016-e0e3e3e8427e3a39.png?imageMogr2/auto-orient/strip|imageView2/2/w/480/format/webp)

image

**注意：**

推荐使用OutputStream流，避免使用PrintWriter流，因为OutputStream流是字节流，可以处理任意类型的数据，而PrintWriter流是字符流，只能处理字符数据，如果用字符流处理字节数据，会导致数据丢失。

##### 3，生成验证码

这个可以当做一个工具类来使用，不必自己写出来，要用的时候复制，然后可以局部的改改代码，就可以变成另外的验证码了。



```java
public class ResponseDemo4 extends HttpServlet {

    public static final int WIDTH = 120; // 生成的图片的宽度
    public static final int HEIGHT = 35; // 生成的图片的高度

    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        // 在内存中创建一张图片
        BufferedImage image = new BufferedImage(WIDTH, HEIGHT, BufferedImage.TYPE_INT_RGB);
        // 得到图片
        Graphics g = image.getGraphics();

        // 1.设置图片的背景色
        setBackGround(g);

        // 2.设置图片的边框
        setBorder(g);

        // 3.在图片上画干扰线
        drawRandomLine(g);

        // 4.在图片上写随机数
        String random = drawRandomNum((Graphics2D) g);
        request.getSession().setAttribute("checkcode", random); // 将随机数存在session中

        /*
         * 5.图形写给浏览器
         */
        response.setContentType("image/jpeg")
        // 发响应头控制浏览器不要缓存图片
        response.setDateHeader("expries", -1);
        response.setHeader("Cache-Control", "no-cache");
        response.setHeader("Pragma", "no-cache");
        ImageIO.write(image, "jpg", response.getOutputStream());

    }

    /*
     * 设置图片的背景色
     */
    private void setBackGround(Graphics g) {
        // 设置颜色
        g.setColor(Color.WHITE);
        // 填充区域
        g.fillRect(0, 0, WIDTH, HEIGHT);
    }

    /*
     * 设置图片的边框
     */
    private void setBorder(Graphics g) {
        // 设置边框颜色
        g.setColor(Color.BLUE);
        // 边框区域
        g.drawRect(1, 1, WIDTH-2, HEIGHT-2);
    }

    /*
     * 在图片上画干扰线
     */
    private void drawRandomLine(Graphics g) {
        // 设置颜色
        g.setColor(Color.GREEN);
        // 设置线条个数并画线
        for(int i=0;i<5;i++) {
            // 生成干扰线随机的起始坐标
            int x1 = new Random().nextInt(WIDTH); // 生成0~WIDTH(不包括WIDTH)的随机数
            int y1 = new Random().nextInt(HEIGHT);

            // 生成干扰线随机的结束坐标
            int x2 = new Random().nextInt(WIDTH);
            int y2 = new Random().nextInt(HEIGHT);

            g.drawLine(x1, y1, x2, y2);
        }
    }

    /*
     * 在图片上写随机数
     */
    private String drawRandomNum(Graphics2D g) {
        // 设置颜色
        g.setColor(Color.RED);
        // 设置字体
        g.setFont(new Font("宋体", Font.BOLD, 20));
        // 常用的中国汉字（汉字区间：[\u4e00-\u9fa5]）
        String base = "\u7684\u4e00\u4e86\u662f\u6211\u4e0d\u5728\u4eba\u4eec\u6709\u6765\u4ed6\u8fd9\u4e0a\u7740\u4e2a\u5730\u5230\u5927\u91cc\u8bf4\u5c31\u53bb\u5b50\u5f97\u4e5f\u548c\u90a3\u8981\u4e0b\u770b\u5929\u65f6\u8fc7\u51fa\u5c0f\u4e48\u8d77\u4f60\u90fd\u628a\u597d\u8fd8\u591a\u6ca1\u4e3a\u53c8\u53ef\u5bb6\u5b66\u53ea\u4ee5\u4e3b\u4f1a\u6837\u5e74\u60f3\u751f\u540c\u8001\u4e2d\u5341\u4ece\u81ea\u9762\u524d\u5934\u9053\u5b83\u540e\u7136\u8d70\u5f88\u50cf\u89c1\u4e24\u7528\u5979\u56fd\u52a8\u8fdb\u6210\u56de\u4ec0\u8fb9\u4f5c\u5bf9\u5f00\u800c\u5df1\u4e9b\u73b0\u5c71\u6c11\u5019\u7ecf\u53d1\u5de5\u5411\u4e8b\u547d\u7ed9\u957f\u6c34\u51e0\u4e49\u4e09\u58f0\u4e8e\u9ad8\u624b\u77e5\u7406\u773c\u5fd7\u70b9\u5fc3\u6218\u4e8c\u95ee\u4f46\u8eab\u65b9\u5b9e\u5403\u505a\u53eb\u5f53\u4f4f\u542c\u9769\u6253\u5462\u771f\u5168\u624d\u56db\u5df2\u6240\u654c\u4e4b\u6700\u5149\u4ea7\u60c5\u8def\u5206\u603b\u6761\u767d\u8bdd\u4e1c\u5e2d\u6b21\u4eb2\u5982\u88ab\u82b1\u53e3\u653e\u513f\u5e38\u6c14\u4e94\u7b2c\u4f7f\u5199\u519b\u5427\u6587\u8fd0\u518d\u679c\u600e\u5b9a\u8bb8\u5feb\u660e\u884c\u56e0\u522b\u98de\u5916\u6811\u7269\u6d3b\u90e8\u95e8\u65e0\u5f80\u8239\u671b\u65b0\u5e26\u961f\u5148\u529b\u5b8c\u5374\u7ad9\u4ee3\u5458\u673a\u66f4\u4e5d\u60a8\u6bcf\u98ce\u7ea7\u8ddf\u7b11\u554a\u5b69\u4e07\u5c11\u76f4\u610f\u591c\u6bd4\u9636\u8fde\u8f66\u91cd\u4fbf\u6597\u9a6c\u54ea\u5316\u592a\u6307\u53d8\u793e\u4f3c\u58eb\u8005\u5e72\u77f3\u6ee1\u65e5\u51b3\u767e\u539f\u62ff\u7fa4\u7a76\u5404\u516d\u672c\u601d\u89e3\u7acb\u6cb3\u6751\u516b\u96be\u65e9\u8bba\u5417\u6839\u5171\u8ba9\u76f8\u7814\u4eca\u5176\u4e66\u5750\u63a5\u5e94\u5173\u4fe1\u89c9\u6b65\u53cd\u5904\u8bb0\u5c06\u5343\u627e\u4e89\u9886\u6216\u5e08\u7ed3\u5757\u8dd1\u8c01\u8349\u8d8a\u5b57\u52a0\u811a\u7d27\u7231\u7b49\u4e60\u9635\u6015\u6708\u9752\u534a\u706b\u6cd5\u9898\u5efa\u8d76\u4f4d\u5531\u6d77\u4e03\u5973\u4efb\u4ef6\u611f\u51c6\u5f20\u56e2\u5c4b\u79bb\u8272\u8138\u7247\u79d1\u5012\u775b\u5229\u4e16\u521a\u4e14\u7531\u9001\u5207\u661f\u5bfc\u665a\u8868\u591f\u6574\u8ba4\u54cd\u96ea\u6d41\u672a\u573a\u8be5\u5e76\u5e95\u6df1\u523b\u5e73\u4f1f\u5fd9\u63d0\u786e\u8fd1\u4eae\u8f7b\u8bb2\u519c\u53e4\u9ed1\u544a\u754c\u62c9\u540d\u5440\u571f\u6e05\u9633\u7167\u529e\u53f2\u6539\u5386\u8f6c\u753b\u9020\u5634\u6b64\u6cbb\u5317\u5fc5\u670d\u96e8\u7a7f\u5185\u8bc6\u9a8c\u4f20\u4e1a\u83dc\u722c\u7761\u5174\u5f62\u91cf\u54b1\u89c2\u82e6\u4f53\u4f17\u901a\u51b2\u5408\u7834\u53cb\u5ea6\u672f\u996d\u516c\u65c1\u623f\u6781\u5357\u67aa\u8bfb\u6c99\u5c81\u7ebf\u91ce\u575a\u7a7a\u6536\u7b97\u81f3\u653f\u57ce\u52b3\u843d\u94b1\u7279\u56f4\u5f1f\u80dc\u6559\u70ed\u5c55\u5305\u6b4c\u7c7b\u6e10\u5f3a\u6570\u4e61\u547c\u6027\u97f3\u7b54\u54e5\u9645\u65e7\u795e\u5ea7\u7ae0\u5e2e\u5566\u53d7\u7cfb\u4ee4\u8df3\u975e\u4f55\u725b\u53d6\u5165\u5cb8\u6562\u6389\u5ffd\u79cd\u88c5\u9876\u6025\u6797\u505c\u606f\u53e5\u533a\u8863\u822c\u62a5\u53f6\u538b\u6162\u53d4\u80cc\u7ec6";

        StringBuffer sb = new StringBuffer();
        int x = 5;
        // 控制字数
        for(int i=0;i<4;i++) {

            int degree = new Random().nextInt()%30; //生成-30-30范围的随机数

            String ch = base.charAt(new Random().nextInt(base.length()))+"";
            sb.append(ch);
            // 写入字之前，设置好旋转
            g.rotate(degree*Math.PI/180, x, 20); // 设置字体旋转角度
            g.drawString(ch, x, 20);
            // 这次旋转不能影响下一次的旋转，所以要将上一次的旋转清掉，转回去
            g.rotate(-degree*Math.PI/180, x, 20);
            x+=30;
        }
        return sb.toString();
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }

}
```

运行结果如下：

![img](https:////upload-images.jianshu.io/upload_images/5824016-409dbe79d1a078f8.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/763/format/webp)

2.jpg

##### 4，设置响应头控制浏览器的行为

**1，定时刷新页面**



```cpp
response.setHeader("refresh", "5");//设置refresh响应头控制浏览器每隔5秒钟刷新一次
```

**2，禁止缓存当前文档内容**



```bash
response.setDateHeader("expries", -1);

response.setHeader("Cache-Control", "no-cache");

response.setHeader("Pragma", "no-cache");
```

**3，重定向**

什么是重定向：一个web资源收到客户端请求后，通知客户端去访问另外一个web资源，这称之为请求重定向。

例如：



```java
public class RedirectResponse extends HttpServlet{
 
    private static final long serialVersionUID = 3903946972744326948L;
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //endRedirect方法实现请求重定向
        resp.sendRedirect(req.getContextPath() + "/index.jsp");
    }
}
```

访问：[http://localhost:8081/ServletDemo/RedirectResponse.html](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8081%2FServletDemo%2FRedirectResponse.html)，浏览器会跳转到：[http://localhost:8081/ServletDemo/index.jsp](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8081%2FServletDemo%2Findex.jsp)

![img](https:////upload-images.jianshu.io/upload_images/5824016-dc96c3faac7987b4.png?imageMogr2/auto-orient/strip|imageView2/2/w/387/format/webp)

image

### 四、web工程中URL地址的推荐写法

在JavaWeb开发中，只要是写URL地址，那么建议最好以`/`开头，也就是使用相对路径的方式，那么这个`/`到底代表什么呢？可以用如下的方式来记忆`/`：**如果"/"是给服务器用的，则代表当前的web工程，如果"/"是给浏览器用的，则代表webapps目录。**

##### 3.1、"/"代表当前web工程的常见应用场景

**①.ServletContext.getRealPath(String path)获取资源的绝对路径**



```bash
/**
* 1.ServletContext.getRealPath("/download/1.JPG")是用来获取服务器上的某个资源，
* 那么这个"/"就是给服务器用的，"/"此时代表的就是web工程
 * ServletContext.getRealPath("/download/1.JPG")表示的就是读取web工程下的download文件夹中的1.JPG这个资源
* 只要明白了"/"代表的具体含义，就可以很快写出要访问的web资源的绝对路径
*/
this.getServletContext().getRealPath("/download/1.JPG");
```

**②.在服务器端forward到其他页面**



```dart
 /**
 * 2.forward
  * 客户端请求某个web资源，服务器跳转到另外一个web资源，这个forward也是给服务器用的，
 * 那么这个"/"就是给服务器用的，所以此时"/"代表的就是web工程
 */
 this.getServletContext().getRequestDispatcher("/index.jsp").forward(request, response);
```

**③.使用include指令或者<jsp:include>标签引入页面**



```php
 <%@include file="/jspfragments/head.jspf" %>
 <jsp:include page="/jspfragments/demo.jsp" />
```

此时"/"代表的都是web工程。

##### 3.2、"/"代表webapps目录的常见应用场景

**①.使用sendRedirect实现请求重定向**



```bash
 response.sendRedirect("/JavaWeb_HttpServletResponse_Study_20140615/index.jsp");
```

服务器发送一个URL地址给浏览器，浏览器拿到URL地址之后，再去请求服务器，所以这个"/"是给浏览器使用的，此时"/"代表的就是webapps目录，`/JavaWeb_HttpServletResponse_Study_20140615/index.jsp`这个地址指的就是`webapps\JavaWeb_HttpServletResponse_Study_20140615\index.jsp`

`response.sendRedirect("/项目名称/文件夹目录/页面");`这种写法是将项目名称写死在程序中的做法，不灵活，万一哪天项目名称变了，此时就得改程序，所以推荐使用下面的灵活写法：

将



```bash
 response.sendRedirect("/JavaWeb_HttpServletResponse_Study_20140615/index.jsp");
```

这种写法改成



```bash
response.sendRedirect(request.getContextPath()+"/index.jsp");
```

`request.getContextPath()`获取到的内容就是`/JavaWeb_HttpServletResponse_Study_20140615`，这样就比较灵活了，使用`request.getContextPath()`代替"/项目名称"，推荐使用这种方式，灵活方便！

**②.使用超链接跳转**



```xml
 <a href="/JavaWeb_HttpServletResponse_Study_20140615/index.jsp">跳转到首页</a>
```

这是客户端浏览器使用的超链接跳转，这个"/"是给浏览器使用的，此时"/"代表的就是webapps目录。

使用超链接访问web资源，绝对路径的写法推荐使用下面的写法改进：



```xml
<a href="${pageContext.request.contextPath}/index.jsp">跳转到首页</a>
```

这样就可以避免在路径中出现项目的名称，使用**${pageContext.request.contextPath}**取代"/JavaWeb_HttpServletResponse_Study_20140615"

**③.Form表单提交**



```xml
 <form action="/JavaWeb_HttpServletResponse_Study_20140615/servlet/CheckServlet" method="post">    
         <input type="submit" value="提交">
 </form>
```

这是客户端浏览器将form表单提交到服务器，所以这个"/"是给浏览器使用的，此时"/"代表的就是webapps目录。

对于form表单提交中action属性绝对路径的写法，也推荐使用如下的方式改进：



```xml
 <form action="${pageContext.request.contextPath}/servlet/CheckServlet" method="post">
          <input type="submit" value="提交">
 </form>
```

${pageContext.request.contextPath}得到的就是"/JavaWeb_HttpServletResponse_Study_20140615"

${pageContext.request.contextPath}的效果等同于request.getContextPath()，两者获取到的都是"/项目名称"

**④.js脚本和css样式文件的引用**



```xml
  <%--使用绝对路径的方式引用js脚本--%>
  <script type="text/javascript" src="${pageContext.request.contextPath}/js/index.js"></script>
  <%--${pageContext.request.contextPath}与request.getContextPath()写法是得到的效果是一样的--%>
  <script type="text/javascript" src="<%=request.getContextPath()%>/js/login.js"></script>
  <%--使用绝对路径的方式引用css样式--%>
  <link rel="stylesheet" href="${pageContext.request.contextPath}/css/index.css" type="text/css"/>
```

综合范例：



```xml
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <title>"/"代表webapps目录的常见应用场景</title>
    <%--使用绝对路径的方式引用js脚本--%>
    <script type="text/javascript" src="${pageContext.request.contextPath}/js/index.js"></script>
    <%--${pageContext.request.contextPath}与request.getContextPath()写法是得到的效果是一样的--%>
    <script type="text/javascript" src="<%=request.getContextPath()%>/js/login.js"></script>
    <%--使用绝对路径的方式引用css样式--%>
      <link rel="stylesheet" href="${pageContext.request.contextPath}/css/index.css" type="text/css"/>
  </head>
  
  <body>
      <%--form表单提交--%>
       <form action="${pageContext.request.contextPath}/servlet/CheckServlet" method="post">
           <input type="submit" value="提交">
       </form>
       <%--超链接跳转页面--%>
       <a href="${pageContext.request.contextPath}/index.jsp">跳转到首页</a>
  </body>
</html>
```

### 五、response细节问题

getOutputStream和getWriter方法分别用于得到输出二进制数据、输出文本数据的ServletOuputStream、Printwriter对象。
 　
 getOutputStream和getWriter这两个方法互相排斥，调用了其中的任何一个方法后，就不能再调用另一方法。
 　
 Servlet程序向ServletOutputStream或PrintWriter对象中写入的数据将被Servlet引擎从response里面获取，Servlet引擎将这些数据当作响应消息的正文，然后再与响应状态行和各响应头组合后输出到客户端。
 　
 Serlvet的service方法结束后，Servlet引擎将检查getWriter或getOutputStream方法返回的输出流对象是否已经调用过close方法，如果没有，Servlet引擎将调用close方法关闭该输出流对象。

**参考资料**

- [https://segmentfault.com/a/1190000004122719?utm_source=debugrun&utm_medium=referral](https://links.jianshu.com/go?to=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000004122719%3Futm_source%3Ddebugrun%26utm_medium%3Dreferral)
- [https://blog.csdn.net/yerenyuan_pku/article/details/51892392](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fyerenyuan_pku%2Farticle%2Fdetails%2F51892392)
- [https://www.cnblogs.com/Zender/p/7636906.html](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2FZender%2Fp%2F7636906.html)
- [https://www.cnblogs.com/xdp-gacl/p/3791993.html](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fxdp-gacl%2Fp%2F3791993.html)
- [http://tool.oschina.net/apidocs/apidoc?api=javaEE6](https://links.jianshu.com/go?to=http%3A%2F%2Ftool.oschina.net%2Fapidocs%2Fapidoc%3Fapi%3DjavaEE6)



作者：程序员欧阳
链接：https://www.jianshu.com/p/8bc6b82403c5
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。