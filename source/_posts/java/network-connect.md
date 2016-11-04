---
title: 一对一网络通信
date: 2016-11-04 15:31:29
tags: socket
categories: Java
---
>Java实现一对一网络通信。

<!--more-->
# 服务端
```java
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.net.ServerSocket;
import java.net.Socket;


public class Server {
	public static void main(String[] args) {
		try{
			System.out.println("等待连接...");
			ServerSocket sket = new ServerSocket(5700);
			Socket ctc = sket.accept();
			System.out.println("连接到"+ctc.getInetAddress().getHostName());
			DataInputStream ifc = new DataInputStream(ctc.getInputStream());
			DataOutputStream otc = new DataOutputStream(ctc.getOutputStream());
			String str;
			double radius,area;
			boolean flag = true;
			while(flag){
				str = ifc.readUTF();
				if(!str.equals("bye")){
					radius = Double.parseDouble(str);
					System.out.println("接收到的半径值为："+radius);
					area = radius * radius * Math.PI;
					str = Double.toString(area);
					otc.writeUTF(str);
					otc.flush();
					System.out.println("圆的面积"+str+"已发送");
				}else{
					flag = false;
					otc.writeUTF("bye");
					otc.flush();
				}
			}
			ifc.close();
			otc.close();
			sket.close();
		}catch(Exception e){
			e.printStackTrace();
		}
	}

}
```

# 客户端
```java
import java.io.BufferedReader;
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.Socket;
import java.net.UnknownHostException;

public class Client {
	public static void main(String[] args) {
		try {
			Socket s = new Socket("localhost",5700);
			DataInputStream ifs = new DataInputStream(s.getInputStream());
			DataOutputStream ots = new DataOutputStream(s.getOutputStream());
			System.out.print("please input radius send to server end by bye:");
			String outStr, inStr;
			boolean flag =  true;
			BufferedReader buf = new BufferedReader(new InputStreamReader(System.in));
			while(flag){
				outStr = buf.readLine();
				ots.writeUTF(outStr);
				ots.flush();
				inStr = ifs.readUTF();
				if(!inStr.equals("bye")){
					System.out.println("从服务器返回的结果是："+inStr);
				}else{
					flag = false;
				}
			}
			ifs.close();
			ots.close();
			s.close();
		} catch (UnknownHostException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}

	}
}
```
# 一对多
- 如果是实现一对多关系的话，把服务器端设置成一个线程启动。

