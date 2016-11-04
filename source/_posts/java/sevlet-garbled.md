---
title: servlet中文乱码
date: 2016-11-04 15:47:58
tags: 乱码
categories: Java
---
>servlet中中文乱码的解决方案。

<!--more-->
# request 中的中文乱码
- post方式提交
    在获得提交表单信息之前调用request.setCharactersEncoding("UTF-8");
- get方式提交
    在Tomcat的server.xml中配置
       <Service>
          <Connector URIEncoding="UTF-8">
                 ...
          </Connector>
      </Service>

# reponse中的中文乱码
- 在通过response获得输出流之前调用response.setCharacterEncoding("UTF-8"); 
