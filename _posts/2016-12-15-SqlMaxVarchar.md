---
layout: post
title: oracle数据库中varchar使用max()函数及varchar排序问题解决方法
category: blog
description:  oracle数据库中，varchar类型数据使用max函数如何获取最大值及varchar类型数据如何排序
---


### varchar使用max函数

在oracle中，如果对varchar类型求max会发现9比10大，这是因为数据类型的原因，我们需要将varchar转换成number类型。

第一种方法是使用to_number()函数。

	select max(to_number(a.clerkid)) from CIF_CLERKINFO a ;

第二种方法将需要使用max()函数的字段后面加0，比如字段：field是要使用max()函数的字段

	select max(a.clerkid+0) from CIF_CLERKINFO a ;

第三种方法是使用cast函数

	select max(cast(a.clerkid as integer)) from CIF_CLERKINFO a ;

### varchar排序 

在oracle中，如果要对varchar类型的字段进行order by操作，那么首先必须得将他们转化成数字类型，比如int类型，这就需要用到cast关键字

	select * from CIF_CLERKINFO a order by cast(a.clerkid as integer) desc;
	
