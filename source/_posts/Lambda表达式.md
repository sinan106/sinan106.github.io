---
title: Lambda表达式
date: 2020-10-19 20:29:46
tags:
- lambda
categories:
- java
---

# 基础
Lambda 一个重要用法是简化某些*匿名内部类*，或者说是对*函数式接口*的实现。
> 函数式接口(Functional Interface)是一个有且仅有一个抽象方法，但是可以有多个非抽象方法的接口。(@Functional interface修饰)

简单例子:
```java
// 1. 不需要参数,返回值为 5  
() -> {return 5}  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
(x) -> {return 2 * x}  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(int x, int y) -> {return x – y}  

//4. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> {System.out.print(s)}
```
省略写法：
1.  省略参数类型
```java
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> {return x – y} 
```
2. 单个参数省略小括号(无参时要写小括号)
```java
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> {return 2 * x}  
```
3. 无返回且仅有一条语句，省略大括号
```java
//4. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```
4. 有返回且仅有一条语句，省略return 和 大括号
```java
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
```
# 进阶
1. 静态方法调用（通过类调用）
```java
	//接口
	@FunctionalInterface
	public interface ReMultiple {
	    int show(int a, int b); //唯一的抽象方法
	}
	
	//方法
	public class syntax3 {
	    private static int fun1(int a, int b) {
	        return 2 * a + b;
	    }
	}
	
	// 可以直接用类名加方法名调用
	ReMultiple lam3 = syntax3::fun1;
	System.out.println(lam3.show(2, 3)); // out: 7
```
2. Java8 常用内置函数式接口
```java
	ArrayList<Integer> list = new ArrayList<>();
	Collections.addAll(list, 1,2,3,4,5,6);
	
	//1.Predicate 参数T 返回值boolean 用于判定
	// 若为偶数则返回真
	Predicate<Integer> predicate = t -> t%2==0;
	// removeIf(Predicate)(将list中的每个对象都通过Predicate，若为真则删除)
	list.removeIf(predicate);
	list.forEach(System.out::print); // out: 135 因为246为偶数 返回真 被删除了

	//2. Consumer 参数T 无返回 用于消费某值
	Consumer<Integer> consumer = System.out::print;
    list.forEach(consumer); //123456
    
	//3. Function 参数T 输出R 用于类型转换
	String str = "aaaaaa";
    Function<String, Integer> function = String::length;
    System.out.println(function.apply(str)); //out: 6
```
