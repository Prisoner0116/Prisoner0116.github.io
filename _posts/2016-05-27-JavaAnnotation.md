---
layout: post
title:  java注解
category: blog
description: java注解笔记
---

声明：这是一篇从[mkyong]翻译过来的文章，查看原文请访问[mkyong]
在这篇教程里，你将会看到如何创建两个自定义的注解`@Test`和`@TesterInfo`来模拟一个简单的单元测试框架

PS:这个单元测试的例子参考自官方的[Java annotation article]

1.@Test 注解

`@interface`这个标识告诉Java，这是一个标准的注解，定义好了之后，你就可以在方法级别(method level)使用这个注解，像这样`@Test(enable=false)`

	//Test.java
	package com.mkyong.test.core;

	import java.lang.annotation.ElementType;
	import java.lang.annotation.Retention;
	import java.lang.annotation.RetentionPolicy;
	import java.lang.annotation.Target;

	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.METHOD) //can use in method only.
	public @interface Test {
		
		//should ignore this test?
		public boolean enabled() default true;
		
	}

### 注意：
方法声明不能有任何参数或抛出子句。返回类型是局限于原语，字符串类、枚举、注释，和前面的类型数组。 

2. @TesterInfo 注解

`@TesterInfo` 可以在class层面使用，用来存储测试员的详细信息。这个注解展示了另外几种返回类型 --枚举、数组和字符串
	//TesterInfo.java
	package com.mkyong.test.core;

	import java.lang.annotation.ElementType;
	import java.lang.annotation.Retention;
	import java.lang.annotation.RetentionPolicy;
	import java.lang.annotation.Target;

	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.TYPE) //on class level
	public @interface TesterInfo {

		public enum Priority {
		   LOW, MEDIUM, HIGH
		}

		Priority priority() default Priority.MEDIUM;
		
		String[] tags() default "";
		
		String createdBy() default "Mkyong";
		
		String lastModified() default "03/01/2014";

	}

3. 单元测试例子

创建一个简单的单元测试的例子，这个例子中将使用到我们前面自定义的注解 `@Test`和`@TesterInfo`

	//TestExample.java
	package com.mkyong.test;

	import com.mkyong.test.core.Test;
	import com.mkyong.test.core.TesterInfo;
	import com.mkyong.test.core.TesterInfo.Priority;

	@TesterInfo(
		priority = Priority.HIGH, 
		createdBy = "mkyong.com",  
		tags = {"sales","test" }
	)
	public class TestExample {

		@Test
		void testA() {
		  if (true)
			throw new RuntimeException("This test always failed");
		}

		@Test(enabled = false)
		void testB() {
		  if (false)
			throw new RuntimeException("This test always passed");
		}

		@Test(enabled = true)
		void testC() {
		  if (10 > 1) {
			// do nothing, this test always passed.
		  }
		}

	}

4. Java 反射机制- 阅读注解

下面这个方法将会展示怎么样使用Java反射API来阅读并且处理自定义的注解

	//RunTest.java
	package com.mkyong.test;

	import java.lang.annotation.Annotation;
	import java.lang.reflect.Method;

	import com.mkyong.test.core.Test;
	import com.mkyong.test.core.TesterInfo;

	public class RunTest {

	  public static void main(String[] args) throws Exception {

		System.out.println("Testing...");

		int passed = 0, failed = 0, count = 0, ignore = 0;

		Class<TestExample> obj = TestExample.class;

		// Process @TesterInfo
		if (obj.isAnnotationPresent(TesterInfo.class)) {

			Annotation annotation = obj.getAnnotation(TesterInfo.class);
			TesterInfo testerInfo = (TesterInfo) annotation;

			System.out.printf("%nPriority :%s", testerInfo.priority());
			System.out.printf("%nCreatedBy :%s", testerInfo.createdBy());
			System.out.printf("%nTags :");

			int tagLength = testerInfo.tags().length;
			for (String tag : testerInfo.tags()) {
				if (tagLength > 1) {
					System.out.print(tag + ", ");
				} else {
					System.out.print(tag);
				}
				tagLength--;
			}

			System.out.printf("%nLastModified :%s%n%n", testerInfo.lastModified());

		}

		// Process @Test
		for (Method method : obj.getDeclaredMethods()) {

			// if method is annotated with @Test
			if (method.isAnnotationPresent(Test.class)) {

				Annotation annotation = method.getAnnotation(Test.class);
				Test test = (Test) annotation;

				// if enabled = true (default)
				if (test.enabled()) {

				  try {
					method.invoke(obj.newInstance());
					System.out.printf("%s - Test '%s' - passed %n", ++count, method.getName());
					passed++;
				  } catch (Throwable ex) {
					System.out.printf("%s - Test '%s' - failed: %s %n", ++count, method.getName(), ex.getCause());
					failed++;
				  }

				} else {
					System.out.printf("%s - Test '%s' - ignored%n", ++count, method.getName());
					ignore++;
				}

			}

		}
		System.out.printf("%nResult : Total : %d, Passed: %d, Failed %d, Ignore %d%n", count, passed, failed, ignore);

		}
}

输出

	Testing...

	Priority :HIGH
	CreatedBy :mkyong.com
	Tags :sales, test
	LastModified :03/01/2014

	1 - Test 'testA' - failed: java.lang.RuntimeException: This test always failed 
	2 - Test 'testC' - passed 
	3 - Test 'testB' - ignored

	Result : Total : 3, Passed: 1, Failed 1, Ignore 1

相关链接：

* [维基百科：Java 注解]
* [官方文档：注解]
* [ElementType]
* [RetentionPolicy]


[mkyong]: http://www.mkyong.com/java/java-custom-annotations-example/ "mkyong"
[Java annotation article]:http://docs.oracle.com/javase/1.5.0/docs/guide/language/annotations.html "Java annotation article"
[维基百科：Java 注解]:https://en.wikipedia.org/wiki/Java_annotation "维基百科：Java 注解"
[官方文档：注解]: http://docs.oracle.com/javase/1.5.0/docs/guide/language/annotations.html "官方文档：注解"
[ElementType]:http://docs.oracle.com/javase/7/docs/api/java/lang/annotation/ElementType.html "ElementType"
[RetentionPolicy]:http://docs.oracle.com/javase/7/docs/api/java/lang/annotation/RetentionPolicy.html "RetentionPolicy"
