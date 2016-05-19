---
layout: post
category : java
tagline: "切换jdk版本"
tags : [Java]
---
{% include JB/setup %}
	由于工作中项目比较老，现在用的还是JDK1.5，平时自己想捣鼓点其他东西的时候，版本太低会导致很多问题，安装了1.6和1.7的JDK每次想用的时候还得去更改环境变量，不胜其烦，今天在网上找到一个办法，通过批处理文件来完成环境变量的切换，下面给做个记录
	@echo off 
	:init 
	set JAVA_HOME_1_5=C:\Program Files (x86)\Java\jdk1.5.0_15
	set JAVA_HOME_1_6=E:\software\jdk
	set JAVA_HOME_1_7=E:\software\jdk7\jdk7
	set Eclipse_EXE=E:\安装包\eclipse\eclipse.exe
 
	:start 
	echo JDK 版本: 
	java -version 
	ping 127.0.0.1 -n 2 -w 1000 > nul 
	echo. 
	echo ============================================= 
	echo jdk版本列表 
	echo 1.7 
	echo 1.6
	echo 1.5 
	echo ============================================= 
	
	:select
	set /p opt=请选择jdk版本： 
	if %opt%==1.7 ( 
		start  /I /WAIT /B wmic ENVIRONMENT where name='JAVA_HOME' set VariableValue="%JAVA_HOME_1_7%" >nul 
		rem reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v JAVA_HOME /t reg_sz /d "%JAVA_HOME_1_7%" /f
		goto success 
	) 
	if %opt%==1.6 ( 
		start /I /WAIT /B wmic ENVIRONMENT where name='JAVA_HOME' set VariableValue="%JAVA_HOME_1_6%" >nul 
		rem reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v JAVA_HOME /t reg_sz /d "%JAVA_HOME_1_6%" /f
		goto success 
	)
	if %opt%==1.5 ( 
		start /I /WAIT /B wmic ENVIRONMENT where name='JAVA_HOME' set VariableValue="%JAVA_HOME_1_5%" >nul 
		rem reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v JAVA_HOME /t reg_sz /d "%JAVA_HOME_1_5%" /f
		goto success 
	) 
	echo 选择的版本错误,请重新选择！ 
	PAUSE 
	goto start 
	
	:success 
	echo. 
	echo 设置环境变了成功. 
	ping 127.0.0.1 -n 2 -w 1000 > nul 
	taskkill /f /im eclipse.exe 
	ping 127.0.0.1 -n 3 -w 1000 > nul 
	start %Eclipse_EXE%

### 遇到的问题
	将上面的内容复制，新建一个bat文件copy进去，执行一下，选择JDK版本，提示设置成功，新开一个cmd窗口，用java -version查看jdk版本，发现jdk并没有变化，再输入echo %JAVA_HOME% 发现确实没有变化，但是自己去环境变量里看，明明已经改过来了，为什么没有生效？
	
	双击环境变量JAVA_HOME然后一路点确定，再打开一个cmd窗口，java -version，现在版本变了
	具体为什么没有生效，还需要研究一下

Please take a look at [{{ site.categories.api.first.title }}]({{ BASE_PATH }}{{ site.categories.api.first.url }})
or jump right into [Usage]({{ BASE_PATH }}{{ site.categories.usage.first.url }}) if you'd like.