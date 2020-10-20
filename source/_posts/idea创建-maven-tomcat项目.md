---
title: idea创建 maven tomcat项目
date: 2020-10-19 20:32:47
tags:
- maven
- tomcat
categories:
- java
- idea
---

# 1. 新建maven项目 选择webapp
![新建](https://img-blog.csdnimg.cn/20200715162502382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjExODkz,size_16,color_FFFFFF,t_70)
# 2. 导包后Edit Configurations,选择tomcat 和 jdk 如有报错 fix 即可,war包部署选择 war:exploded，URL是选择每次运行时默认打开的链接，可以和后续的Application Context配置一样
![在这里插入图片描述](https://img-blog.csdnimg.cn/202007151626513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjExODkz,size_16,color_FFFFFF,t_70)
# 3. 接着打开Deployment，里面就已经有个war包了，==然后修改 Application Context== 这就是你url的访问路径
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200715163050658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjExODkz,size_16,color_FFFFFF,t_70)

# 4.运行即可，会显示index.jsp的内容