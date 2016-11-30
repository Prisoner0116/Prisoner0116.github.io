---
layout: post
title:  Map及其实现类HashMap,LinkedHashMap的区别
category: blog
description: 项目迁移，JDK从1.5升级到1.6，项目出了一个小问题，java类将一个hashmap传到页面上，页面上利用js遍历给元素赋值，赋值的时候，顺序都乱了，但是在以前的生产上没有问题
---


### 问题描述

最近项目迁移，jdk从1.5升级到了1.6，项目出了一个小问题(如下图所示)，页面解析后台传过来的hashmap给元素赋值，赋值的时候顺序都乱了

正确的
![rightpic](/images/map/rightpic.png)

错误的
![wrongpic](/images/map/wrongpic.png)

js解析hashmap代码如下：

	function pageLoad()
	{
		var infoAll = document.getElementById('infoAll').value; 
		infoAll = infoAll.substring(1,infoAll.length-1);
		if(infoAll == "")
		{
			return;
		}	
		var personOrEnter = document.getElementById('personOrEnter').value;
		var info = infoAll.split(',');
		if(personOrEnter=="0")
		{
			document.getElementById('qyjhzzc').innerHTML=(info[0].split('='))[1];
			document.getElementById('qyjhbhc').innerHTML=(info[1].split('='))[1];
		}
		else
		{
			//问题就出在这里，这里的代码是按照固定顺序去取值的
			document.getElementById('grjhhh').innerHTML=(info[0].split('='))[1];
			document.getElementById('grzsz').innerHTML=(info[2].split('='))[1];
			document.getElementById('grzbj').innerHTML=(info[3].split('='))[1];
			document.getElementById('grjhzt').innerHTML=(info[1].split('='))[1];
		}
	}

java类中infoAll的主要代码如下：

	infoAll = new HashMap();
	infoAll.put("GRJHBH", rspBody.getChild("GRJHBH").getValue());
	infoAll.put("GRJHZT", grjhzt);
	infoAll.put("GRZHZZC", rspBody.getChild("GRZHZZC").getValue());//个人账户总资产
	infoAll.put("GRZHBJZZC", rspBody.getChild("GRZHBJZZC").getValue());//个人账户本金总资产

为什么以前写的代码是可行的呢？写一个类测试一下（jdk1.5）

	package org.yujl;
	import java.util.HashMap;
	import java.util.Iterator;
	import java.util.Map;

	public class MapTest {
		public static void main(String[] args) {
			Map<String, String> map = new HashMap<String, String>();
			for(int i =0;i<5;i++){
				map.put(String.valueOf(i), "value"+i);
			}
			
	/*//		第一种：普遍使用，二次取值
			System.out.println("通过Map.keySet遍历key和value：");
			for (String key : map.keySet()) {
				System.out.println("key= "+ key + " and value= " + map.get(key));
				}
			*/
			//第二种
			System.out.println("通过Map.entrySet使用iterator遍历key和value：");
			Iterator<Map.Entry<String, String>> it = map.entrySet().iterator();
			while (it.hasNext()) {
				Map.Entry<String, String> entry = it.next();
				System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
			}
			
	//		第三种：推荐，尤其是容量大时
			System.out.println("通过Map.entrySet遍历key和value");
			for (Map.Entry<String, String> entry : map.entrySet()) {
				System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
			}
			//第四种
			System.out.println("通过Map.values()遍历所有的value，但不能遍历key");
				for (String v : map.values()) {
					System.out.println("value= " + v);
			}
		}
	}

运行结果：

	通过Map.entrySet使用iterator遍历key和value：
	key= 3 and value= value3
	key= 2 and value= value2
	key= 0 and value= value0
	key= 4 and value= value4
	key= 1 and value= value1

不管运行多少次，结果都是一样的。是不是遍历的方法不对呢？又换了几种遍历方法还是一样的结果。也就是说遍历hashmap的时候顺序是随机的，但是运行结果都是一样的，这样就可以解释为什么以前的代码为什么可行，那项目迁移之后为什么会报错呢，在jdk1.6下面试一下

运行结果：
	
	通过Map.entrySet使用iterator遍历key和value：
	key= 3 and value= value3
	key= 2 and value= value2
	key= 1 and value= value1
	key= 0 and value= value0
	key= 4 and value= value4
	
在1.6下面执行多次，结果只有一个。发现1.5和1.6下面的运行结果是不一样的，这也就是为什么程序会出错的原因，为什么jdk升级之后存出顺序就不一样了，hash值并没有变

有时间可以好好学习一下[hashmap原理]

解决方案：用map的另一个实现LinkedHashMap。LinkedHashMap继承了HashMap,最大的区别就是LinkedHashMap遍历的时候能够按照put的顺序给出结果。LinkedHashMap保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的（先进先出），页面上取值的时候可以按照固定顺序去取值了

[hashmap原理]: http://www.cnblogs.com/chenssy/p/3521565.html


