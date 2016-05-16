## Data Access 数据访问

在javascript中，有四种基本的数据访问位置

1. 直接量(直接量仅仅代表自己，而不存储于特定位置。 JavaScript 的直接量包括：字符串，数字，布尔值，对象，
数组，函数，正则表达式，具有特殊意义的空值，以及未定义)

2. 变量(开发人员使用 var 关键字创建用于存储数据值)

3. 数组项(具有数字索引，存储一个 JavaScript 数组对象)

4. 对象成员(具有字符串索引，存储一个 JavaScript 对象)

> 总的趋势是，对所有浏览器来说，一个标识符所处的位置越深，读写它的速度就越慢。所以，函数中局部
变量的访问速度总是最快的，而全局变量通常是最慢的（优化的 JavaScript 引擎在某些情况下可以改变这
种状况）。请记住，全局变量总是处于运行期上下文作用域链的最后一个位置，所以总是最远才能触及的。
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


