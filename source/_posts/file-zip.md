---
title : 如何压缩/解压Zip文件？
date: 2019-05-16 16:19:45
img: https://s1.ax1x.com/2022/12/07/zcIznI.jpg
tags:
 - IO
 - JAVA
 - ZIP
categories: 
 - 后端
 - Java
keywords:
 - java
 - io
 - 压缩/解压Zip文件
---
> java.util.zip包提供了用于读取和编写zip和gzip文件格式的类。在这篇文章中我们将学习如何使用java.util.zip.ZipInputStream和java.util.zip.ZipFile解压zip文件，如何以zip格式压缩文件和目录。

> ZipInputStream - 此类用于按顺序读取zip条目。 
> ZipFile - 此类在内部使用随机访问文件来读取zip条目。 
> 使用java.util.zip.ZipInputStream解压缩zip文件，ZipOutputStream类用于将zip格式的数据写入输出流，zip文件包含多个由ZipEntry类表示的条目。 

示例一：

```java
// 使用java.util.zip.ZipInputStream解压缩zip文件

package com.yiibai.tutorial.io;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.zip.ZipEntry;
import java.util.zip.ZipInputStream;
/** * @author yiibai */
public class UnzipFileExample1 { 

    public static void main(String[] args) { 
        File zipFile = new File("Test.zip"); 
        File output = new File("Unzip"); 
        FileInputStream fileInputStream = null; 
        ZipInputStream zipInputStream = null; 
        try { fileInputStream = new FileInputStream(zipFile); 
            zipInputStream = new ZipInputStream(fileInputStream); 
            // Get ZipEntry ZipEntry entry = zipInputStream.getNextEntry(); 
            while (entry != null) { 
                if (entry.isDirectory()) { 
                    // Create directory 
                    File dir = new File(output entry.getName()); 
                    if (!dir.exists()) { 
                        dir.mkdirs(); 
                    }
                } else {
                    // Read zipEntry and write to a file. 
                    File file = new File(output entry.getName());FileOutputStream fileOutputStream = new FileOutputStre(file);
                    int i; 
                    byte[] data = new byte[1024]; 
                    while ((i = zipInputStream.read(data)) != -1) {                 fileOutputStream.write(data 0 i); 
                    } 
                    fileOutputStream.close(); 
                } 
                // Get next entry 
                entry = zipInputStream.getNextEntry();
            }
        } catch (IOException e) { 
            e.printStackTrace(); 
        } finally {
            try { 
                if (fileInputStream != null) { fileInputStream.close(); 
                } 
                if (zipInputStream != null) { zipInputStream.close(); } 
            } catch (IOException e) { 
                e.printStackTrace(); 
            } 
        }
    }
} 


```

示例二：

```java
// 使用java.util.zip.ZipFile解压缩zip文件

package com.yiibai.tutorial.io;

import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.Enumeration;
import java.util.zip.ZipEntry;
import java.util.zip.ZipFile;
/** * @author yiibai */
public class UnzipFileExample2 {
    public static void main(String[] args) { 
        File output = new File("Unzip2");
        ZipFile zipFile = null;
        try {
            zipFile = new ZipFile(new File("Test.zip"));
            Enumeration entries = zipFile.entries();
            while (entries.hasMoreElements()) {
                ZipEntry entry = (ZipEntry) entries.nextElement();
                if (entry.isDirectory()) {
                    // Create directory
                    File dir = new File(output entry.getName());
                    if (!dir.exists()) {
                        dir.mkdirs();
                    }
                } else {
                    // Get zipEntry name and create an output stream. 
                    File file = new File(output entry.getName()); FileOutputStream fileOutputStream = new FileOutputStream(file); 
                    InputStream inputStream = zipFile.getInputStream(entry);
                    // Read Zip file entry 
                    int i; 
                    byte[] data = new byte[1024];
                    while ((i = inputStream.read(data)) != -1) { 
                        fileOutputStream.write(data 0 i); 
                    }
                    fileOutputStream.close();
                    inputStream.close();
                }
            }
        } catch (IOException e) {
            e.printStackTrace(); 
        } finally { 
            try { 
                if (zipFile != null) { 
                    zipFile.close(); 
                } 
            } catch (IOException e) { 
                e.printStackTrace();
            } 
        } 
    } 
}

```

压缩文件的示例：

```java
package com.yiibai.tutorial.io; 

import java.io.File; 
import java.io.FileInputStream; 
import java.io.FileOutputStream; 
import java.io.IOException; 
import java.util.zip.ZipEntry; 
import java.util.zip.ZipOutputStream; 

/** * @author yiibai */ 
public class ZipFileExample { 
    public static void main(String[] args) { 
        // Output zip file 
        File zipfile = new File("MyFile.zip"); 
        // Input file to be compressed 
        File inFile = new File("file.txt"); 
        FileInputStream fileInputStream = null; 
        FileOutputStream fileOutputStream = null; 
        ZipOutputStream zipOutputStream = null; 
        try { 
            fileInputStream = new FileInputStream(inFile); 
            fileOutputStream = new FileOutputStream(zipfile); 
            zipOutputStream = new ZipOutputStream(fileOutputStream); 
            // Create ZipEntry 
            ZipEntry entry = new ZipEntry(inFile.getName()); 
            // Set position of stream to the start of entry data zipOutputStream.putNextEntry(entry); 
            // Write data to 
            zipOutputStream byte[] data = new byte[1024]; 
            int i; 
            while ((i = fileInputStream.read(data)) != -1) { 
                zipOutputStream.write(data 0 i); 
            } 
            // Closes the current ZIP entry and positions the stream for writing 
            // the next entry 
            zipOutputStream.closeEntry(); 
        } catch (IOException e) {
            e.printStackTrace(); 
        } finally { 
            try { 
                if (fileInputStream != null) { 
                    fileInputStream.close(); 
                } if (zipOutputStream != null) { 
                    zipOutputStream.close(); 
                } 
            } catch (IOException e) { 
                e.printStackTrace(); 
            } 
        }
    } 
} 

```
压缩目录中的所有文件和子目录

```java
// 以递归方式遍历目录中的所有文件，并使用ZipOutputStream将其写入zip文件。

package com.yiibai.tutorial.io; 

import java.io.File; 
import java.io.FileInputStream; 
import java.io.FileOutputStream; 
import java.io.IOException; 
import java.util.zip.ZipEntry; 
import java.util.zip.ZipOutputStream; 
/** * @author yiibai */ 

public class ZipDirectoryExample { 
    public static void main(String[] args) { 
        File zipFile = new File("MyZip.zip"); 
        File directory = new File("D:/Work/java"); 
        FileOutputStream fileOutputStream = null; 
        ZipOutputStream zipOutputStream = null; 
        try { 
            fileOutputStream = new FileOutputStream(zipFile); 
            zipOutputStream = new ZipOutputStream(fileOutputStream); 
            // Create zip file createZipFile(zipOutputStream directory directory.getAbsolutePath()); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } finally { 
            try { 
                if (zipOutputStream != null) { 
                    zipOutputStream.close(); 
                } 
            } catch (IOException e) { 
                e.printStackTrace(); 
            } 
        } 
    } 
    /** * @param zipOutputStream * @param directory * @throws IOException */ private static void createZipFile(ZipOutputStream zipOutputStream File directory String zipEntryPath) throws IOException { 
        File[] files = directory.listFiles(); 
        for (File file : files) { 
            if (file.isDirectory()) { 
                createZipFile(zipOutputStream file zipEntryPath); 
            } else { 
                FileInputStream fileInputStream = new FileInputStream(file); 
                // Create zipEntry 
                String filePath = file.getAbsolutePath(); 
                ZipEntry entry = new ZipEntry(filePath.replace(zipEntryPath + File.separator "")); 
                // Set position of stream to the start of entry data zipOutputStream.putNextEntry(entry); 
                // Write data to zip output stream 
                byte data[] = new byte[1024]; 
                int i; 
                while ((i = fileInputStream.read(data)) != -1) { 
                    zipOutputStream.write(data 0 i); 
                } 
                // Closes the current ZIP entry zipOutputStream.closeEntry(); 
                // Close file stream 
                fileInputStream.close(); 
            }
        }
    } 
} 
```


原文出自[易百教程](https://www.yiibai.com/) 原文链接：
https://www.yiibai.com/geek/detail/447
https://www.yiibai.com/geek/detail/441