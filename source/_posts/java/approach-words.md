---
title: 逼近的文字
date: 2016-11-04 15:25:45
tags: applet
categories: Java
---
>Java applet实现不断逼近的文字。

<!--more-->
# 代码展示
```java
import java.applet.Applet;
import java.awt.Color;
import java.awt.Font;
import java.awt.FontMetrics;
import java.awt.Graphics;
import java.awt.Image;


public class WordsToLarge extends Applet implements Runnable{
    private Image myImage;
    private Graphics myGraphic;
    private Font font;
    private String myString;
    private Thread myThread;
    private int fontSize;
    //初始化顺序....
    public void init(){                               // 1.init()
        this.setSize(350,300);
        myImage = createImage(350,300);
        myGraphic = myImage.getGraphics();
        myString = "Java Applet小应用程序";
        font = new Font("华文楷体",Font.BOLD,8);
    }
    public void start(){// 2. start();
        if(myThread == null){
            myThread = new Thread(this);
            myThread.start();
        }
    }
    public void update(Graphics g){
        paint(g);
    }
    public void paint(Graphics g){// 3.paint()
        myGraphic.setColor(Color.blue);
        myGraphic.fillRect(0, 0, getSize().width, getSize().height);
        font = new Font("TimesRoman",Font.BOLD,fontSize);
        myGraphic.setFont(font);
        myGraphic.setColor(Color.yellow);
        FontMetrics fm = myGraphic.getFontMetrics(font);
        int fontHeight = fm.getHeight();
        int w;
        int baseLine = getSize().height/2 + fontHeight /2;
        w = fm.stringWidth(myString);
        w = (getSize().width - w) / 2;
        myGraphic.drawString(myString, w,baseLine-=20);
        g.drawImage(myImage,0,0,this);
        fontSize++;
    }

    @Override
    public void run() {
        while(true){
            repaint();
            if(fontSize > getSize().height){
                fontSize = 0;
            }
            try{
                myThread.sleep(100);
            }catch(Exception e){
                e.printStackTrace();
            }
        }
    }
    //终止顺序 stop()....   destroy();
}
```

