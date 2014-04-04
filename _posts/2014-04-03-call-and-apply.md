---
layout: post
title: JS中的call,apply
---

JS中每个函数都包含两个非继承而来的方法，apply(),call()用途都是在特定的作用域中调用函数，实际上等于设置函数体内this对象的值


> 1.方法定义
> 
call方法: 
>
>语法：call([thisObj[,arg1[, arg2[,   [,.argN]]]]]) 
定义：调用一个对象的一个方法，以另一个对象替换当前对象。 
说明： 
call 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。 
如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。 

>apply方法：
> 
语法：apply([thisObj[,argArray]]) 
定义：应用某一对象的一个方法，用另一个对象替换当前对象。 
说明： 
如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。 
如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。

两个方法均接收两个参数：一个是在其中运行函数的作用域，另一个是参数数组

注意：有两种情况需要注意

- 传null或undefined时，将是JS执行环境的全局变量
- 浏览器中是window，其它环境（如node）则是global

>2.常用实例

a.


	function add(a,b)  
	{  
    	alert(a+b);  
	}  
	function sub(a,b)  
	{  
	    alert(a-b);  
	}  
  
	add.call(sub,3,1);

这个例子中的意思就是用 add 来替换 sub，add.call(sub,3,1) == add(3,1) ，所以运行结果为：alert(4); // 注意：js 中的函数其实是对象，函数名是对 Function 对象的引用。

b.

	function Animal(){    
	    this.name = "Animal";    
	    this.showName = function(){    
	        alert(this.name);    
	    }    
	}    
	  
	function Cat(){    
	    this.name = "Cat";    
	}    
	   
	var animal = new Animal();    
	var cat = new Cat();    
	    
	//通过call或apply方法，将原本属于Animal对象的showName()方法交给对象cat来使用了。    
	//输入结果为"Cat"    
	animal.showName.call(cat,",");    
	//animal.showName.apply(cat,[]);  

call 的意思是把 animal 的方法放到cat上执行，原来cat是没有showName() 方法，现在是把animal 的showName()方法放到 cat上来执行，所以this.name 应该是 Cat

c.实现继承

	function Animal(name){      
	    this.name = name;      
	    this.showName = function(){      
	        alert(this.name);      
	    }      
	}      
	    
	function Cat(name){    
	    Animal.call(this, name);    
	}      
	    
	var cat = new Cat("Black Cat");     
	cat.showName();
Animal.call(this) 的意思就是使用 Animal对象代替this对象，那么 Cat中不就有Animal的所有属性和方法了吗，Cat对象就能够直接调用Animal的方法以及属性了.

d、多重继承
	function Class10()  
	{  
	    this.showSub = function(a,b)  
	    {  
	        alert(a-b);  
	    }  
	}  
	  
	function Class11()  
	{  
	    this.showAdd = function(a,b)  
	    {  
	        alert(a+b);  
	    }  
	}  
	  
	function Class2()  
	{  
	    Class10.call(this);  
	    Class11.call(this);  
	}  

很简单，使用两个 call 就实现多重继承了
当然，js的继承还有其他方法，例如使用原型链。说了call ，当然还有 apply，这两个方法基本上是一个意思，区别在于 call 的第二个参数可以是任意类型，而apply的第二个参数必须是数组，也可以是arguments

e.扩充函数运行的作用域
	window.color = "red";
	var o = { color: "blue"};
	
	function sayColor(){
	        alert(this.color);
	}
	
	sayColor();  //red
	
	sayColor.call(this); //red
	sayColor.call(window); //red
	sayColor.call(o); //blue
call(this)和call(window)是两种显式的在全局作用域中调用函数的方式，均显示“red”
然而运行call(o)时，函数的执行环境不一样了，此时函数体内的this对象指向了o，结果显示“blue”
用call和apply扩充作用域的最大好处就是对象不需要与方法有任何耦合关系

>3.扩展
>
>Array.prototype.slice.call(arguments,0)的应用

a.

Array.prototype：就是Array的原型，一个类凡是通过prototype加的属性，方法，都可以在这个类的对象里找到
内置的类型可以通过prototype找到内置的属性方法
Array.prototype.slice这句就是访问Array的内置方法
因为Array是类名，而不是对象名，所以不能直接用Array.slice

b.

Array.prototype.slice.call(arguments,0) 这句里，就是把 arguments 当做当前对象﻿也就是说 要调用的是 arguments 的slice 方法，后面的 参数 0 也就成了 slice 的第一个参数slice(0)就是获取所有
为什么要这么调用 arguments 的slice 方法呢？就是因为 arguments 不是真的组数，typeof arguments==="Object" 而不是 "Array"
它没有slice这个方法，通过这么Array.prototype.slice.call调用，JS的内部机制应该是 把arguments对象转化为Array
因为Array.prototype.slice.call调用后，返回的是一个数组

>4.apply妙用

 	/*定义一个人类*/  
    function Person(name,age)  
    {  
        this.name=name;  
        this.age=age;  
    }  
    /*定义一个学生类*/  
    functionStudent(name,age,grade)  
    {  
        Person.apply(this,arguments);  
        this.grade=grade;  
    }  
    //创建一个学生类  
    var student=new Student("zhangsan",21,"一年级");  
    //测试  
    alert("name:"+student.name+"\n"+"age:"+student.age+"\n"+"grade:"+student.grade);  
    //大家可以看到测试结果name:zhangsan age:21  grade:一年级

分析: Person.apply(this,arguments);
this:在创建对象在这个时候代表的是student
arguments:是一个数组,也就是[“zhangsan”,”21”,”一年级”];
也就是通俗一点讲就是:用student去执行Person这个类里面的内容,在Person这个类里面存在this.name等之类的语句,这样就将属性创建到了student对象里面

在调用apply方法的时候,第一个参数是对象(this), 第二个参数是一个数组集合, <b>在调用Person的时候,他需要的不是一个数组,但是为什么他给我一个数组我仍然可以将数组解析为一个一个的参数</b>,这个就是apply的一个巧妙的用处,可以将一个数组默认的转换为一个参数列表([param1,param2,param3] 转换为 param1,param2,param3) 这个如果让我们用程序来实现将数组的每一个项,来装换为参数的列表,可能都得费一会功夫,借助apply的这点特性,所以就有了以下高效率的方法:  

a)        Math.max 可以实现得到数组中最大的一项
因为Math.max 参数里面不支持Math.max([param1,param2]) 也就是数组

但是它支持Math.max(param1,param2,param3…),所以可以根据刚才apply的那个特点来解决 var max=Math.max.apply(null,array),这样轻易的可以得到一个数组中最大的一项<b>(apply会将一个数组装换为一个参数接一个参数的传递给方法)</b>

这块在调用的时候第一个参数给了一个null,这个是因为没有对象去调用这个方法,我只需要用这个方法帮我运算,得到返回的结果就行,.所以直接传递了一个null过去

b)        Math.min  可以实现得到数组中最小的一项

同样和 max是一个思想 var min=Math.min.apply(null,array);

c)        Array.prototype.push 可以实现两个数组合并

同样push方法没有提供push一个数组,但是它提供了push(param1,param,…paramN) 所以同样也可以通过apply来装换一下这个数组,即:

	var arr1=new Array("1","2","3");  
	  
	var arr2=new Array("4","5","6");  
	  
	Array.prototype.push.apply(arr1,arr2); 

也可以这样理解,arr1调用了push方法,参数是通过apply将数组装换为参数列表的集合. 