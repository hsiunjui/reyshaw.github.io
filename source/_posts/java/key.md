---
title: 创建密钥
date: 2016-11-04 15:35:59
tags: 加解密
categories: Java
---
>Java实现秘钥的创建。

<!--more-->
# 代码
```java
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectOutputStream;
import java.security.Key;
import java.security.KeyFactory;
import java.security.KeyPair;
import java.security.KeyPairGenerator;
import java.security.PublicKey;
import java.security.spec.X509EncodedKeySpec;
import javax.crypto.KeyGenerator;

public class KeyGeneratorDemo {
	public static void main(String[] args) {
		try{
			//产生单钥加密的密钥
			KeyGenerator keyGenerator = KeyGenerator.getInstance("DESede"); 	//采用EDSede算法
			keyGenerator.init(168); 				//选择DESede算法，密钥长度为112位或168位
			Key myKey = keyGenerator.generateKey();
			System.out.println("得到单钥加密密钥");
			//产生双钥的密钥对(keypair)
			KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("RSA");	 //采用RSA算法
			keyPairGenerator.initialize(1024);  					//指定密钥长度为1024位
			KeyPair keyPair = keyPairGenerator.generateKeyPair(); 	//生成密钥对
			System.out.println("生成张三的公钥对");
			//保存公钥的字节数组
			File f = new File("pulbicKey.dat");
			FileOutputStream fout = new FileOutputStream(f);
			fout.write(keyPair.getPublic().getEncoded()); 	//得到公钥的字节数组
			fout.close();
			System.out.println("保存公钥到文件："+f.getAbsolutePath());
			//用java对象序列化保存私钥，通常应对私钥加密后再保存
			ObjectOutputStream oout = new ObjectOutputStream(new FileOutputStream("privateKey.dat"));
			oout.writeObject(keyPair.getPrivate());  	//序列化私密
			oout.close();
			System.out.println("保存私钥到：privateKey.dat");
			FileInputStream fin = new FileInputStream("pulbicKey.dat");  //此处要写明路径
			ByteArrayOutputStream baout = new ByteArrayOutputStream(); 
			int aByte = 0;
			while((aByte = fin.read()) != -1){
				baout.write(aByte);
			}
			fin.close();
			byte[] keyBytes = baout.toByteArray();
			baout.close();
			//从字节数组解密公钥
			X509EncodedKeySpec x509KeySpec = new X509EncodedKeySpec(keyBytes);
			KeyFactory keyFactory = KeyFactory.getInstance("RSA");
			PublicKey pubKey = keyFactory.generatePublic(x509KeySpec);	 //解码公钥
			System.out.println("从文件中成功得到公钥");
		}catch(Exception e){
			e.printStackTrace();
		}
	}

}
```
