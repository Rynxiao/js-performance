## Data Access 数据访问

在javascript中，有四种基本的数据访问位置

1. 直接量(直接量仅仅代表自己，而不存储于特定位置。 JavaScript 的直接量包括：字符串，数字，布尔值，对象，
数组，函数，正则表达式，具有特殊意义的空值，以及未定义)

2. 变量(开发人员使用 var 关键字创建用于存储数据值)

3. 数组项(具有数字索引，存储一个 JavaScript 数组对象)

4. 对象成员(具有字符串索引，存储一个 JavaScript 对象)

#### Identifier Resolution Performance 标识符识别性能

> 总的趋势是，对所有浏览器来说，一个标识符所处的位置越深，读写它的速度就越慢。所以，函数中局部
变量的访问速度总是最快的，而全局变量通常是最慢的（优化的 JavaScript 引擎在某些情况下可以改变这
种状况）。**请记住，全局变量总是处于运行期上下文作用域链的最后一个位置，所以总是最远才能触及的。**
通过以上信息，在没有优化 JavaScript 引擎的浏览器中，最好尽可能使用局部变量。一个好的经验法则
是：用局部变量存储本地范围之外的变量值，如果它们在函数中的使用多于一次。考虑下面的例子：

```javascript
function initUI(){
	var bd = document.body,
	links = document.getElementsByTagName("a"),
	i = 0,
	len = links.length;
	while(i < len){
		update(links[i++]);
	}
	document.getElementById("go-btn").onclick = function(){
		start();
	};
	bd.className = "active";
}
```

> 此函数包含三个对 document 的引用， document 是一个全局对象。搜索此变量，必须遍历整个作用域链，
直到最后在全局变量对象中找到它。你可以通过这种方法减轻重复的全局变量访问对性能的影响：首先将
全局变量的引用存储在一个局部变量中，然后使用这个局部变量代替全局变量。例如，上面的代码可以重
写如下：

```javascript
function initUI(){
	var doc = document,
	bd = doc.body,
	links = doc.getElementsByTagName_r("a"),
	i = 0,
	len = links.length;
	while(i < len){
		update(links[i++]);
	}
	doc.getElementById("go-btn").onclick = function(){
		start();
	};
	bd.className = "active";
}
```
#### Scope Chain Augmentation 改变作用域链

+ with表达式

> 当代码流执行到一个 with 表达式时，运行期上下文的作用域链被临时改变了。一个新的可变对象将被
创建，它包含指定对象的所有属性。此对象被插入到作用域链的前端，意味着现在函数的所有局部变量都
被推入第二个作用域链对象中，所以访问代价更高了,例如：

```javascript
function initUI(){
	with (document){ //avoid!
		var bd = body,
		links = getElementsByTagName_r("a"),
		i = 0,
		len = links.length;
		while(i < len){
			update(links[i++]);
		}
		getElementById("go-btn").onclick = function(){
			start();
		};
		bd.className = "active";
	}
}
```

> 通过将 document 对象传递给 with 表达式，一个新的可变对象容纳了 document 对象的所有属性，被插入
到作用域链的前端。这使得访问 document 的属性非常快，但是访问局部变量的速度却变慢了，例如 bd 变
量。正因为这个原因，最好不要使用 with 表达式。正如前面提到的，只要简单地将 document 存储在一个
局部变量中，就可以获得性能上的提升。

+ try-catch

> 当 try 块发生错误时，程序流程自动转入 catch 块，并将异常对象推入作用域链前端的一个
可变对象中。在 catch 块中，函数的所有局部变量现在被放在第二个作用域链对象中。例如：

```javascript
try {
	methodThatMightCauseAnError();
} catch (ex){
	alert(ex.message); //scope chain is augmented here
}
```


