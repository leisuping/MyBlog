---
title: 使用 jQuery.fileDownload.js 插件实现Ajax下载文件
date: 2019-05-17 12:36:45
tags:
 - JS
 - JQ
categories: 
 - 文件下载
 - 前端
 - JS
---
&emsp;近期做项目过程中有一个需求，根据文档的地址从服务器上下载该文档。分两种情况，第一种是单个文件的下载（使用文件流输出到前台页面），第二种是多个文件实现批量下载。

思路一：单个文件下载，获取文件流响应到前台页面。
&emsp;&emsp;&emsp;&emsp;多个文件批量下载，获取文件路径集合，循环下载。（效率低，显然是不可取的）

思路二：单个文件下载，写一个工具类获取文件流响应到页面同时封装文件打包zip方法。
&emsp;&emsp;&emsp;&emsp;多个文件批量下载，将获取到的文件路径打包成zip，再使用文件流响应到前台页面。

原理：（从磁盘读到内存然后从内存写入网络，客服端接收流保存）
&emsp;&emsp;&emsp;1、获取文件路径，使用文件输入流将文件输入到内存
&emsp;&emsp;&emsp;2、使用输出流将文件输出到页面
&emsp;&emsp;&emsp;3、页面接收到响应流在回调中给出提示

问题：
1、返回文件名中文乱码
解决：<pre><code>response.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(downFile.getName(), "UTF-8"));</code></pre>

2、因为原生的ajax请求是无法进行文件上传下载的（无法处理流格式的参数），所以我想到了用form表单模拟提交。但是，使用form表单提交在请求结束之后前台无法获得回调函数。

```
var url = rootPath + "hncaBusinessinfo/downLoadPdf";
var form = $("&lt;form&gt;&lt;/form&gt;").attr("action", url).attr("method", "post");
form.append($("&lt;input&gt;&lt;/input&gt;").attr("type", "hidden").attr("name", "PATHSIGNFORM").attr("value", path));
form.appendTo('body').submit().remove();
```

解决：
使用jQuery.filedownload.js来实现文件下载的回调（页面需要先引用 jQuery.fileDownload.js）。
```
var url = rootPath + "hncaBusinessinfo/downLoadPdf";
var index = layer.msg();
$.fileDownload(url, {
    httpMethod : 'POST',
    data : encodeURI(path),
    prepareCallback : function(url) {
        index = layer.msg('文件正在下载中，请稍后...', {icon: 16,shade: [0.5, '#f5f5f5'],scrollbar: false,offset: 'auto', time:10000});
    },
    successCallback : function(url) {
        layer.close(index);
    },
    failCallback : function(html, url) {
        myAlert("下载失败");
    }
});

```

3、在使用了jQuery.filedownload.js来下载文件，但是页面下载成功后回调没有反应。
解决：刚开始找了很久，一直以为是前台代码写错了后面才发现使用jQuery.filedownload.js下载文件页面要获取回调函数后台必须要设置头部信息。

```
response.setHeader("Set-Cookie", "fileDownload=true; path=/");

没有设置这个头部信息，页面还是没办法执行回调函数的呢！

```
#### 话不多说，直接来工具类的代码：

```
/**
    * 单个PDF文件下载（单个文件下载）
    * @param response 响应流
    * @param fileName 文件名（单个文件下载无需设置此属性值）
    * @param urlStr PDF路径
    * @return
    */
public static String downLoadPdfByUrl(HttpServletResponse response,String fileName,String urlStr){
    // 获取一个文件
    File downFile = new File(urlStr);
    // 配置文件下载
    response.setHeader("content-type", "application/octet-stream");
    response.setContentType("application/octet-stream");
    // 实现文件下载
    byte[] buffer = new byte[1024]; // 控制一次输出的流量
    FileInputStream fis = null; // 文件输入流 【输入到服务器内存】
    BufferedInputStream bis = null; // 文件输入缓冲流
    try {
        //页面下载成功回调函数需设置头信息
        response.setHeader("Set-Cookie", "fileDownload=true; path=/");
        // 下载文件能正常显示中文
        response.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(downFile.getName(), "UTF-8"));
        // 将本地的文件 输出 到 内存中
        fis = new FileInputStream(downFile);
        // 添加缓冲 【提高读写性能】
        bis = new BufferedInputStream(fis);
        // 获取网络 输出流 （从 response 中获取）
        OutputStream os = response.getOutputStream();
        // 读取规定流量 的 文件流
        int i = bis.read(buffer);
        while (i != -1) { // 如果还有文件流 （使用while 循环读取 文件流）
            os.write(buffer, 0, i); // 输出到网络中
            i = bis.read(buffer); // 再读一段文件流
        }
        System.out.println("Download the song successfully!");
    } catch (Exception e) {
//            e.printStackTrace();
        return "系统找不到指定的文件，PDF文件下载失败！";
    }
    finally {
        // 为了提高服务器性能，读写流用完后一定要关闭
        if (bis != null) {
            try {
                bis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (fis != null) {
            try {
                fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    return "PDF文件下载成功！";
}

```

#### 多文件下载（批量下载），下载的是zip格式

```
/**
    * 批量下载打包zip压缩包
    * @param response
    * @param names
    * @param paths
    */
public static void downloadZip(HttpServletResponse response,String[] names,String [] paths) {
    //存放--服务器上zip文件的目录
    String directory = "D:\\repositoryPdf\\"+"pdf";
    File directoryFile=new File(directory);
    if(!directoryFile.isDirectory() && !directoryFile.exists()){
        directoryFile.mkdirs();
    }
    //设置最终输出zip文件的目录+文件名
    SimpleDateFormat formatter  = new SimpleDateFormat("yyyy年MM月dd日HH时mm分ss秒");
    String zipFileName = formatter.format(new Date())+".zip";
    String strZipPath = directory+"\\"+zipFileName;

    ZipOutputStream zipStream = null;
    FileInputStream zipSource = null;
    BufferedInputStream bufferStream = null;
    File zipFile = new File(strZipPath);
    try{
        //构造最终压缩包的输出流
        zipStream = new ZipOutputStream(new FileOutputStream(zipFile));
        for (int i = 0; i<paths.length ;i++){
            //解码获取真实路径与文件名
            String realFileName = java.net.URLDecoder.decode(names[i],"UTF-8");
            String realFilePath = java.net.URLDecoder.decode(paths[i],"UTF-8");
            File file = new File(realFilePath);
            //考虑文件不存在时如何处理
            if(file.exists())
            {
                zipSource = new FileInputStream(file);//将需要压缩的文件格式化为输入流
                /**
                    * 压缩条目不是具体独立的文件，而是压缩包文件列表中的列表项，称为条目，就像索引一样这里的name就是文件名,
                    * 文件名和之前的重复就会导致文件被覆盖
                    */
                ZipEntry zipEntry = new ZipEntry(realFileName);//在压缩目录中文件的名字
                zipStream.putNextEntry(zipEntry);//定位该压缩条目位置，开始写入文件到压缩包中
                bufferStream = new BufferedInputStream(zipSource, 1024 * 10);
                int read = 0;
                byte[] buf = new byte[1024 * 10];
                while((read = bufferStream.read(buf, 0, 1024 * 10)) != -1)
                {
                    zipStream.write(buf, 0, read);
                }
            }
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        //关闭流
        try {
            if(null != bufferStream) bufferStream.close();
            if(null != zipStream){
                zipStream.flush();
                zipStream.close();
            }
            if(null != zipSource) zipSource.close();
        } catch (IOException e) {
//                e.printStackTrace();
            System.out.println("PDF文件下载失败！");
        }
    }
    //判断系统压缩文件是否存在：true-把该压缩文件通过流输出给客户端后删除该压缩文件  false-未处理
    if(zipFile.exists()){
        downLoadPdfByUrl(response,zipFileName,strZipPath);
        zipFile.delete();
    }
}

```
#### controller代码

##### 判断该文件路径是否有效

```
@RequestMapping("hncaBusinessinfo/fileExists")
@Pass
@ResponseBody
@ApiOperation(value="判断文件是否存在", notes="返回文件是否存在消息" ,httpMethod="POST")
public DataRes fileExists(HncaBusinessinfo hncaBusinessinfo,HttpServletRequest request,HttpServletResponse response){
    String[] paths = hncaBusinessinfo.getPATHSIGNFORM().split(",");
    //记录未找到路径的文件个数
    int count = 0;
    if(paths.length==1){
        File file = new File(paths[0]);
        if(!file.exists())
            return DataRes.error("1","找不到该文件！");
    }else{
        for(String path:paths){
            File file = new File(path);
            if(!file.exists()){
                count++;
            }
        }
        if(count>0&&count!=paths.length){
            return DataRes.error("404","其中包含"+count+"个无效路径文件，是否继续下载？");
        }else if(count==paths.length){
            return DataRes.error("1","所选的"+paths.length+"个文件路径皆为无效路径！");
        }
    }
    return DataRes.success("确认下载您选中的"+paths.length+"个文件！");
}

```

##### 下载文件

```
/**
* 批量下载PDF文档->hnca_businessinfo
*/
@RequestMapping("hncaBusinessinfo/downLoadPdf")
@Pass
@ResponseBody
@ApiOperation(value="下载PDF文档", notes="批量下载签字表" ,httpMethod="POST",produces = "text/html;charset=UTF-8")
public String downLoadPdf(@RequestBody String path,HttpServletRequest request,HttpServletResponse response){
    if(path!=null&&!path.isEmpty())
        path = path.replace("=undefined","");
    String[] paths = new String[0];
    try {
        paths = URLDecoder.decode(path, "utf-8").split(",");
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }
    //判断是批量下载还是单个下载
    if(paths.length==1){
        //直接调用单个PDF文档下载
        DownloadPdf.downLoadPdfByUrl(response,"",paths[0]);
    }else{
        //根据PDF下载地址获取单个PDF文档名称（path的最后一个/到.pdf） lastIndexOf("f")
        String[] names = new String[paths.length];
        for(int i = 0;i<paths.length;i++){
            names[i] = paths[i].substring(paths[i].lastIndexOf("\\")+1,paths[i].indexOf(".pdf"))+UUID.randomUUID()+".pdf";
        }
        //将PDF文档打包为zip文件提供给用户下载
        if(paths.length==names.length)
            DownloadPdf.downloadZip(response,names,paths);
    }
    return "PDF文件下载成功！";
}

```


[参考](https://blog.csdn.net/zf18234031156/article/details/83744097)

[参考](https://blog.csdn.net/weixin_41029960/article/details/82585082)

