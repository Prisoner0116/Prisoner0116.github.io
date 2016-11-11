---
layout: post
title:  设计模式--单例模式 
category: blog
description: 对于单例模式的理解
---


### 单例模式特点

* 单例类只能有一个实例。
* 单例类必须自己创建自己的唯一实例。
* 单例类必须给所有其他对象提供这一实例。

从具体实现角度来说，就是以下三点：

* 单例模式的类只提供`私有的构造函数`
* 类定义中含有一个该类的`静态私有对象`
* 该类提供了一个`静态的公有的函数`用于创建或获取它本身的静态私有对象。
	
### 代码示例（java）

上面提到的特点，可以从下面的代码中看出来

下面这个代码是从配置文件中读取信息，这里使用了单例模式，减少了系统开销


	import java.io.File;
	import java.io.FileInputStream;
	import java.util.Properties;

	import org.apache.log4j.Logger;

	import cn.com.agree.cjp.teller.pub.check.ClassLoaderUtil;

	public class GlobeConfig
	{
		private static GlobeConfig instance;

		private static Logger logger = Logger.getLogger(GlobeConfig.class);

		private Properties prop = null;

		private GlobeConfig()
		{

			try
			{
				prop = new Properties();
				String filename = ClassLoaderUtil.getExtendResource(
						"../conf/config.properties").getPath();

				System.out.println("filename " + filename);
				logger.error("filename " + filename);

				prop.load(new FileInputStream(new File(filename)));
			} catch (Exception e)
			{
				logger.error(e);
			}

		}

		public static GlobeConfig getInstance()
		{
			if (instance == null)
			{
				instance = new GlobeConfig();
			}
			return instance;
		}

		public String getValue(String key)
		{
			return prop.getProperty(key);
		}

		public static void main(String[] args)
		{
			String ss = GlobeConfig.getInstance().getValue("SQLServerDriver");
			System.out.println("ss=" + ss);
		}

	}