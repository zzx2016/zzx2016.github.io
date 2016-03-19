---
title: Picasso图片加载库
date: 2016-03-19 14:48:48
tags: Android
---

#### Picasso开源库
做过`ListView`和`GridView`加载图片的，都没少为出现OOM以及图片缓存而烦恼，Picasso的出现让我们摆脱了窘境，Picasso是由一家名叫square的公司开源的，这家公司的开源项目不止这一个，Android上比较优秀的okhttp，retrofit框架都是出自这家公司，也算得上是良心企业了，下面是来自[官网](http://square.github.io/picasso/)的翻译。
<!--more-->

#### 介绍
Picasso允许你只用简单的一行代码就能完成对网络图片的加载，代码如下：
`Picasso.with(context).load("http://i.imgur.com/DvpvklR.png").into(imageView);`
注：我觉得Picasso的语法风格很符合我们的语言表达，如上代码可以翻译成Picasso在（with）当前上下文中加载（load）一张图片到（into）imageView控件。这样的代码写起来很舒服。
许多Android上常见的图片加载问题都被Picasso自动处理了，例如：
1. 在`adapter`中处理图像的回收和取消下载
2. 使用很少的内存进行复杂的图像转换
3. 自动处理内存缓存和磁盘缓存
![](http://182.92.8.217/picture/30)

#### 特性
1. Adapter中的加载
在Adapter中自动检测复用和取消下载,例如:
```Java
@Override
public void getView(int position, View convertView, ViewGroup parent) {
    SquaredImageView view = (SquaredImageView) convertView;
    if (view == null) {
        view = new SquaredImageView(context);
    }
    String url = getItem(position); //仅需要一句话搞定    
    Picasso.with(context).load(url).into(view);}
```

2. 图像变换
转换图像以便更好的适配布局和减少存储空间，代码如下：
`Picasso.with(context).load(url).resize(50, 50).centerCrop().into(imageView);`
你也可以自定义更高效的转换方法，代码如下：
```Java
public class CropSquareTransformation implements Transformation {       
        @Override
        public Bitmap transform(Bitmap source) {
            int size = Math.min(source.getWidth(), source.getHeight());   
            int x = (source.getWidth() - size) / 2;
            int y = (source.getHeight() - size) / 2;
            Bitmap result = Bitmap.createBitmap(source, x, y, size, size);
            if (result != source) {
                source.recycle();
            }
            return result;
        }
        @Override
        public String key() {
            return "square()";
        }
}
```
只需要传递一个Bitmap实例到transform方法。

3. 设置占位图片
Picasso可以设置下载前和下载出错时的图像，代码如下：
`Picasso.with(context).load(url).placeholder(R.drawable.user_placeholder).error(R.drawable.user_placeholder_error).into(imageView);`
在下载出错的图像被设置前，Picasso会尝试三次请求

4. 资源加载
加载的图片来源还可以来自Resources, assets, files以及content providers，代码如下：
```Java
Picasso.with(context).load(R.drawable.landing_screen).into(imageView1);
Picasso.with(context).load("file:///android_asset/DvpvklR.png").into(imageView2);
Picasso.with(context).load(new File(...)).into(imageView3);
```

5. 调试标识
在开发阶段，我们可以通过调用setIndicatorsEnabled(true)方法，设置Picasso可以根据图片来源的不同在图片上做出不同颜色的标记。
![](http://182.92.8.217/picture/29)

#### 使用
1. jar包的方式
[下载jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar)，直接导入即可使用。
2. maven的方式
```XML
<dependency>
    <groupid>com.squareup.picasso</groupid>           
    <artifactid>picasso</artifactid>
    <version>2.5.2</version>
</dependency>
```
3. gradle的方式
`compile 'com.squareup.picasso:picasso:2.5.2'`
