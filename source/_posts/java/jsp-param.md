---
title: JSP获取简单参数
date: 2016-11-04 16:04:28
tags: jsp
categories: Java
---
>JSP操作简单表单。

<!--more-->
# 代码示例
```jsp
<%@ page language="java" contentType="text/html" pageEncoding="utf-8"%>
<%@ page import="java.util.*"%>
<html>
	<head>
		<title>JSP Get Parameters</title>
	</head>
	<body>
		<form name="form" action="this.jsp" method="post">
			NAME:<input type="text" name="uname"><br>
			SEX:<input type="radio" name="sex" value="male" CHECKED>male
			<input type="radio" name="sex" value="female">female<br>
			CITY:<select name="city">
				<option value="bj">BEIJING</option>
				<option value="tj">TIANJIN</option>
				<option value="sh">SHANGHAI</option>
				<option value="cq">CHONGQING</option>
			</select><br>
			HOB:	<input type="checkbox" name="**inst" value="sing">SING
			<input type="checkbox" name="**inst" value="dance">DANCE
			<input type="checkbox" name="**inst" value="swing">SWING
			<input type="checkbox" name="**inst" value="book">BOOK
			<input type="checkbox" name="**inst" value="travel">TRAVEL<br>
			INTRODUCE:<textarea cols="30" rows="3" name="note"></textarea><br>
			<input type="hidden" name="uid" value="1">
			<input type="submit" value="SUBMIT">
			<input type="reset" value="RESET">
		</form>
	<hr/>
<%
	request.setCharacterEncoding("GBK");
	Enumeration enu = request.getParameterNames();
%>
		<table border="1">
			<tr>
				<td>参数名称</td>
				<td>参数内容</td>
			</tr>
<%
		while(enu.hasMoreElements()){
			String paramName = (String) enu.nextElement();
%>
			<tr>
				<td><%=paramName%></td>
				<td>
<%				
				if(paramName.startsWith("**")){
					String paramValue[] = request.getParameterValues(paramName);
					for(int x = 0; x < paramValue.length; x++){
%>
						<%=paramValue[x]%>
<%
					}
				}else{
					String paramValue = request.getParameter(paramName);
%>
					<%=paramValue%>		
<%
				}		
%>
				</td>
			</tr>
<%
		}
%>
		</table>	
	</body>
</html> 
```

# 效果显示 
    NAME:    <input>
    SEX:male female
    CITY:     <select>
    HOB:	SING DANCE SWING BOOK TRAVEL
    INTRODUCE:    <textarea>
         <submit>    <reset>
# 获取的参数
参数名称|参数内容 
-------|-------    
uname|name
sex|male
city|cq
**ins|sing dance swing book travel
note|introduce someting
uid|1
 
# 主要类和方法
    Enumerration;
    HttpServletRequest; 
    request.getParameterNames();    
    request.getParameter(paramName);
    request.getParameterValues(paramName); 

# 其他
    request.getHeaderNames();
    request.getHeader(headerName); 
    request.getMethod() ; 
    request.getRemoteAddr() ; 
    request.getServletPath() ;
    request.getContextPath() ; 