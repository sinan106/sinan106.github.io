---
title: Mybatis-plus_hello
date: 2020-10-18 11:38:14
tags:
- mybatis
categories:
- hello
---
# 1. 配置文件

#### config.MyBatisPlus-Config

```java
@Configuration // 标记为配置文件
@MapperScan("com.zhouxug.hello.dao")  // 扫描DAO层文件
public class MyBatisPlusConfig {
    
    
 	/**
     *  分页插件 分页具体使用会在后续讲述
     */
    @Bean
    public PaginationInterceptor paginationInterceptor () {
        return new PaginationInterceptor();
    }
}
```

#### dao.BlogMapper 被扫描文件

```java
@Repository // 作为仓库 
public interface BlogMapper extends BaseMapper<Blog> {
    //继承自 BaseMapper<T> 泛型
}
```



# 2. 自动填充

#### config.MyMetaObjectHandler

 ```java
 /**
  * 需实现 MetaObjectHandler 中 insertFill 和 updateFill 方法
  * 使用时就要取消掉数据库的自动填充
  */
 @Component
 public class MyMetaObjectHandler implements MetaObjectHandler {
     
     // 插入时填充
     @Override
     public void insertFill(MetaObject metaObject) {
         
         // setFieldValByName 
         // 第一个参数是你要填充字段的  实体名称  ！！！
         // 第二个为填充值
         // 第三个为元数据
         this.setFieldValByName("blogPostTime", new Date(), metaObject);
         this.setFieldValByName("blogUpdateTime", new Date(), metaObject);
     }
 
     // 更新时填充
     @Override
     public void updateFill(MetaObject metaObject) {
         this.setFieldValByName("blogUpdateTime", new Date(), metaObject);
     }
 }
 ```



# 3. 分页插件

#### config.MyBatisPlus-Config

```java
// 在config中配置分页插件 
@Bean
public PaginationInterceptor paginationInterceptor () {
	return new PaginationInterceptor();
}
```

#### 具体使用

```java
    // 使用Page来指定分页
	// current 为当前第几页 从一开始
	// size 为每一页的行数
	Page<Attachment> page = new Page<>(current, size);
    QueryWrapper<Attachment> wrapper = new QueryWrapper<>();
    wrapper.orderByDesc("upload_time");

	// 使用mapper 中的selectPage 来传入wrapper 和 page 参数 从而达到分页
    IPage<Attachment> attachmentIPage = attachmentMapper.selectPage(page, wrapper);
    List<Attachment> records = attachmentIPage.getRecords();
```



# 4. 自定义SQL

#### 1. 注解方式

#### dao.UserMapper 声明并定义方法

```java
// 在Mapper文件中直接定义方法
@Repository
public interface UserMapper extends BaseMapper<User> {

    @Update("update user set user_name = #{user_name}, description = #{description}, icon = #{icon} where email = #{email}")
    void updateUserInfo(@Param("user_name") String userName,
                        @Param("description") String description,
                        @Param("icon") String icon,
                        @Param("email") String email);
}
```

#### 最后通过service.Impl 通过mapper调用

```java
userMapper.updateUserInfo();
```

#### 2. XML方式

#### application.yml 指定mapper.xml位置

```yml
mybatis-plus:
	# 如果是放在src/main/java目录下 classpath:/com/\*/*/mapper/\*Mapper.xml*
	# 如果是放在resource目录 classpath:/mapper/**.xml*
	mapper-locations: classpath:/mapper/**.xml
```

#### dao.UserMapper 声明方法
```java
// 在Mapper文件中直接定义方法
@Repository
public interface UserMapper extends BaseMapper<User> {
	List<User> selectByName(@Param("name") String name);
}
```

#### UserMapper.xml 定义SQL

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.zhouxug.hello.dao.UserMapper">

    <select id="selectByName" resultType="com.example.demo.model.User">
        SELECT * FROM user where name = #{name}
    </select>

</mapper>
```

#### 最后通过service.Impl 通过mapper调用

```java
userMapper.updateUserInfo();
```



# 5. 时间查询

```java
// apply 拼接sql 语句
// 使用date_format(数据库字段名， '%Y'/'%m'/'%d')
wrapper.apply("date_format(post_time, '%Y') = {0}", "2020");
List<Blog> blogList = blogMapper.selectList(wrapper);
```



# 6. 遇到的一些坑

#### 1. Update User表中的userName时，sql拼接会缺失userName

```java
// 主要代码
User user = new User();
user.setUserName("new");
wrapper.eq("email", "@example.com");
userMapper.update(user, wrapper);

// sql :  update user ser where email = { } 
// 应当为 ： update user ser user_name = “new” where email = { }
```

##### 解决方式：使用自定义sql : updateUserInfo

#### dao.UserMapper

```java
@Repository
public interface UserMapper extends BaseMapper<User> {

    @Update("update user set user_name = #{user_name}, description = #{description}, icon = #{icon} where email = #{email}")
    void updateUserInfo(@Param("user_name") String userName,
                        @Param("description") String description,
                        @Param("icon") String icon,
                        @Param("email") String email);
}
```

#### 最后通过service.Impl 通过mapper调用

```java
userMapper.updateUserInfo();
```

#### 2. wrapper.select 获取部分字段时实体类与数据库映射失败

```java
// 如果实体类与数据库字段是驼峰式对应，则没有问题
// 但是如果是别名，哪怕设置了 @TableField(value = "") 仍会无法映射
 wrapper.select("post_time");
// sql : SELECT post_time FROM blog
// 应当为 : SELECT post_time AS blogPostTime FROM blog
// 这里post_time别名为 blogPostTime 并且设置了@TableField(value = "blogPostTime") 但是仍有问题
```

##### 解决方法1：使用select(Class<T> entityClass, Predicate<TableFieldInfo> predicate)

```java
// 第一个参数为查询的实体类
// 第二个参数为lambda表达式 
// getColumn().equals() 是数据库中的字段名
// getProperty().equals() 是实体类中的字段名
// 连接多个字段使用 && 连接
// !表示排除这个字段 反之为需要这个字段
wrapper.select(Blog.class, i -> !i.getColumn().equals("update_time")
        && !i.getProperty().equals("blogContent"));

wrapper.select(Blog.class, i -> i.getColumn().equals("post_time"))；
```

##### 解决方法2：使用select(String column)手动拼接字段

```java
wrapper.select("post_time AS blogPostTime");

// sql : SELECT post_time AS blogPostTime FROM blog 
// 感觉 select(column)是直接拼接进去的
```

##### 题外话：distinct的使用

```java
// 直接加在 数据库字段名前即可 
// 但是目前不知道 select(Class, Predicate) 如何使用
select("distinct post_time AS blogPostTime");
```

#### 3. 主键自增问题

```java
public class Tag {

    // 这里的type = IdType.AUTO自增是指依靠数据库自增，而不是mp来填充
    // 所以数据库id一定要填自增
    @TableId(type = IdType.AUTO)
    private Integer tagId;

    private String tagName;
}
```
















