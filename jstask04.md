# 本题涉及的一些知识点整理
-------
写在开头：

本人Js小白一枚！虽然近两年看了不少关于Js的文档，例如最近在系统地看一本书叫：《JavaScript高级程序设计（第3版）》，但由于工作内容基本都是围绕着html和css，很少需要去写Js，所以知识点基本也是一边看一边忘的状态。

颇为忐忑的发表了这篇笔记，里面很多应该都是最基本应该知道的，只是觉得这些最基本的曾经学习过的方法在实际写代码的时候却一点想不起来，实在是不应该。所以干脆列举出来，毕竟好记性不如烂笔头！

如果下面有理解错误的，望大家能指出，多谢！
## 1.判断值为数字类型的方法
>### 需要考虑数字输入的合法性

可以使用 `!isNaN(val)` 来进行判断value值是否位数字类型，

NAN顾名思义：not a number，

具体例子如下：

**HTML**

	<input id="num" type="text" />
	<button id="left_in">左侧入</button>
	<button id="right_in">右侧入</button>
	<button id="left_out">左侧出</button>
	<button id="right_out">右侧出</button>
		
	<ul id="num_wrap">
			
	</ul>

**JS**

	var num=document.getElementById("num");
	var leftIn=document.getElementById("left_in");
	var rightIn=document.getElementById("right_in");
	var leftOut=document.getElementById("left_out");
	var rightOut=document.getElementById("right_out");
	var numList=document.getElementById("num_wrap");
	
	function insertNum(){
		if(num.value==""){
			alert("请输入一个数字");
		}else if(!isNaN(num.value)){
			var addLi=document.createElement('li');
			addLi.innerHTML=num.value;
			numList.insertBefore(addLi,numList.firstChild);
			num.value="";
		}else{
			alert("格式不对，请您重新输入一个数字！")
		}
	}	

抛开html部分，以及元素定义的部分，直接到判断数字类型的代码

刚开始我只是考虑到了输入的值为非数值的操作，后来测试的过程中发现，如果在未输入任何数据的情况下点击按钮，依然能够插入一个值为空的li标签，

![](https://seven777777.github.io/baidu_ife2017/images/jstask04_md1.png)

测试发现!isNAN()方法会在不输入任何值的情况下，依旧返回true，所以我在代码里增加了一个 `num.value=""` 的判断。

## 2.子节点的插入
* `appendChild()` 方法：可以向节点的子节点列表的末尾添加新的子节点。语法：`appendChild(newchild)`
* `insertBefore()` 方法：可以在已有子节点前插入一个新的子节点。语法：`insertChild(newchild,refchild)`

## 3.childNodes
> 点击"左侧出"，读取并删除队列左侧第一个元素，并弹窗显示元素中数值；

> 点击"右侧出"，读取并删除队列又侧第一个元素，并弹窗显示元素中数值；

写插入数字的需求时，我立刻想到的就是`firstChild()` 和 `lastChild()` 这两个方法，事实证明他们也确实分别完成了从左侧和从右侧插入数字的需求。

但是实现读取值并移除的需求时候却出现了问题，childNodes返回的集合中除了包含了子节点li，同时还包含了空格等一些意想不到的东西，因此会在操作过程中造成错误。

解决方法：利用 `nodeType` 对childNodes返回的集合进行过滤。

但是也有别的方法，除了childNodes以外，还可以使用 `querySelectorAll()` 方法。

+ querySelector()
+ querySelectorAll()

以上两种方法都是接收一个CSS选择符，`querySelector()` 会返回匹配的第一个元素；`querySelectorAll()` 则是返回一个NodeList实例，避免了使用childNodes的`firstChild()` 和 `lastChild()`会出现的问题

但是我最终用的是`getElementsByTagName("li")`方法。具体代码如下：

	/*
	 *点击左侧出、右侧出分别读取并删除队列左右第一个元素，并弹窗显示元素中数值 
	 * */
	var liList=numList.getElementsByTagName("li");
	function removeLeft(){
	//var liNode=numList.querySelectorAll('li');
	//if(confirm("该值为"+numList.firstChild.innerHTML+"你确定要删除？")){
	//numList.firstChild.remove();
	//避免childNodes返回除节点以外其他的值，使用下面两行代码
		if(confirm("该值为"+liList[0].innerHTML+"你确定要删除？")){
			liList[0].remove();
		}else{
			alert("取消删除")
		}
	}

## 4.对比querySelectorAll 和 getElementsBy系列

### 1.W3C标准
* querySelectorAll 属于 W3C 中的 Selectors API 规范
* getElementsBy 系列则属于 W3C 的 DOM 规范

### 2.浏览器兼容
* querySelectorAll：IE 8+、FF 3.5+、Safari 3.1+、Chrome 和 Opera 10+
* getElementsBy系列：IE 9+、FF 3 +、Safari 3.1+、Chrome 和 Opera 9+

### 3.接收参数
* querySelectorAll：CSS 选择符，如：querySelectorAll('.a .c')、querySelectorAll('li')、querySelectorAll('#a li')
* getElementsBy系列：单一的className、tagName 和 name，如：getElementsByClassName('c')、getElementsBytagName('li')

### 4.返回值
* querySelectorAll返回的是一个 Static Node List（静态的）
* getElementsBy系列返回的是一个 Live Node List（动态的）

### 5.性能
getElementsByTagName()要比querySelectorAll()快很多

原因是因为动态和静态NodeList的区别

> 关于两种方法的对比，下面是我的参考：
[点我](https://www.zhihu.com/question/24702250)、[还有我](http://www.cnblogs.com/childsplay/p/5527999.html)

## 5.事件代理
> 点击队列中任何一个元素，则该元素会被从队列中删除

因为`li`标签是动态增加和减少的，所以解决这个问题的时候，我首先想到的就是利用**事件代理**，对li的父级标签绑定事件。

**事件代理**主要用到了JavaScript的两个特性：**事件冒泡** 和 **目标元素**。就不展开说了，因为我自己也还没有理解太透彻。呵呵！（尴尬的笑）

关于最后一个问题的具体实现代码如下：

	/*点击队列中任何一个元素，则该元素会被从队列中删除*/
	numList.addEventListener("click",function(e){
		if(event.target.nodeName.toLowerCase()=="li"){
	//					console.log(event.target);
			numList.removeChild(event.target);
		}
	})

