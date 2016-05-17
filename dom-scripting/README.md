## DOM Scripting DOM 编程

### 访问和修改DOM元素

+ innerHTML 与 DOM 方法比较

> 多年来，在 web 开发者社区已经对此问题进行了许多讨论：更新页面时，使用虽不标准却被良好支持
的 innerHTML 属性更好呢，还是使用纯 DOM 方法，如 document.createElement ()更好呢？如果不考虑标准
问题，它们的性能如何？答案是：性能差别不大，但是，在所有浏览器中， innerHTML 速度更快一些，除
了最新的基于 WebKit 的浏览器（ Chrome 和 Safari）。

+ 节点克隆

使用 DOM 方法更新页面内容的另一个途径是克隆已有 DOM 元素，而不是创建新的——即使用
element.cloneNode()（ element 是一个已存在的节点）代替 document.createElement();

> 在大多数浏览器上，克隆节点更有效率，但提高不太多。

+ HTML Collections HTML 集合

> 正如 DOM 标准中所定义的那样， HTML 集合是一个“虚拟存在，意味着当底层文档更新时，它们将自动更新”

+ 元素节点

> DOM 属性诸如 childNode， firstChild，和 nextSibling 不区分元素节点和其他类型节点，如注释节点和文
本节点（这两个标签之间往往只是一些空格）。**在许多情况下，只有元素节点需要被访问，所以在循环中，
似乎应当对节点返回类型进行检查，过滤出非元素节点。这些检查和过滤都是不必要的 DOM 操作**。

| Property               |     Use  as a replacement for   |
|-----------------------:|--------------------------------:|
| children               |     ChildNodes                  |
| childElementCouont     |     ChildNodes.length           |
| firstElementChild      |     fisrtChild                  |
| lastElementChild       |     lastChild                   |
| nextElementSibling     |     nextSibling                 |
| previousElementSibling |     previousSibling             |

+ querySelector && querySelectorAll

> 函数 querySelectorAll()接收一个 CSS 选择器字符串参数并返回一个 NodeList——由符合条件的节点构成的类数组对象。此函数不返回
HTML 集合，所以返回的节点不呈现文档的“存在性结构”。这就避免了本章前面提到的 HTML 集合所固有
的性能问题（以及潜在的逻辑问题）。

### 重绘(repaint)与重排(reflow)

+ 触发重排版的情况

添加或删除可见的 DOM 元素

元素位置改变

内容改变，例如，文本改变或图片被另一个不同尺寸的所替代

元素尺寸改变（因为边距，填充，边框宽度，宽度，高度等属性改变）

最初的页面渲染

浏览器窗口改变尺寸

*根据改变的性质，渲染树上或大或小的一部分需要重新计算。某些改变可导致重排版整个页面：例如，当一个滚动条出现时。*

+ 查询并刷新渲染树改变

> 因为计算量与每次重排版有关，大多数浏览器通过队列化修改和批量显示优化重排版过程。然而，你可
能（经常不由自主地）强迫队列刷新并要求所有计划改变的部分立刻应用。获取布局信息的操作将导致刷
新队列动作，这意味着使用了下面这些方法：

**offsetTop, offsetLeft, offsetWidth, offsetHeight**

**scrollTop, scrollLeft, scrollWidth, scrollHeight**

**clientTop, clientLeft, clientWidth, clientHeight**

**getComputedStyle() (currentStyle in IE)（在 IE 中此函数称为 currentStyle）**

> 在改变风格的过程中，最好不要使用前面列出的那些属性。任何一个访问都将刷新渲染队列，即使你正
在获取那些最近未发生改变的或者与最新的改变无关的布局信息。考虑下面这个例子，它改变同一个风格
属性三次（这也许不是你在真正的代码中所见到的，不过它孤立地展示出一个重要话题）：

```javascript
var computed,
	tmp = '',
	bodystyle = document.body.style;
if (document.body.currentStyle) { // IE, Opera
	computed = document.body.currentStyle;
} else { // W3C
	computed = document.defaultView.getComputedStyle(document.body, '');
}
// inefficient way of modifying the same property
// and retrieving style information right after
bodystyle.color = 'red';
tmp = computed.backgroundColor;
bodystyle.color = 'white';
tmp = computed.backgroundImage;
bodystyle.color = 'green';
tmp = computed.backgroundAttachment;
```

在这个例子中， body 元素的前景色被改变了三次，每次改变之后，都导入 computed 的风格。导入的属
性 backgroundColor, backgroundImage, 和 backgroundAttachment 与颜色改变无关。然而，浏览器需要刷新
渲染队列并重排版，因为 computed 的风格被查询而引发。比这个不讲效率的例子更好的方法是不要在布局信息
改变时查询它。如果将查询 computed 风格的代码搬到末尾，代码看起来将是这个样子：

```javascript
bodystyle.color = 'red';
bodystyle.color = 'white';
bodystyle.color = 'green';
tmp = computed.backgroundColor;
tmp = computed.backgroundImage;
tmp = computed.backgroundAttachment;
```

+ 最小化重绘和重排版

Style changes 改变风格

```javascript
var el = document.getElementById('mydiv');
el.style.borderLeft = '1px';
el.style.borderRight = '2px';
el.style.padding = '5px';
```

这里改变了三个风格属性，每次改变都影响到元素的几何属性。在这个糟糕的例子中，它导致浏览器重
排版了三次。大多数现代浏览器优化了这种情况只进行一次重排版，但是在老式浏览器中，或者同时有一
个分离的同步进程（例如使用了一个定时器），效率将十分低下。如果其他代码在这段代码运行时查询布
局信息，将导致三次重布局发生。而且，此代码访问 DOM 四次，可以被优化。

一个达到同样效果而效率更高的方法是：将所有改变合并在一起执行，只修改 DOM 一次。可通过使用
cssText 属性或者修改CSS类名称实现：
```javascript
var el = document.getElementById('mydiv');
el.style.cssText = 'border-left: 1px; border-right: 2px; padding: 5px;';

// or

var el = document.getElementById('mydiv');
el.className = 'active';
```

+ 批量修改 DOM

当你需要对 DOM 元素进行多次修改时，你可以通过以下步骤减少重绘和重排版的次数：

1. 从文档流中摘除该元素

2. 对其应用多重改变

3. 将元素带回文档中

方法：

① 隐藏元素，进行修改，然后再显示它。

② 使用一个文档片断在已存 DOM 之外创建一个子树，然后将它拷贝到文档中。

③ 将原始元素拷贝到一个脱离文档的节点中，修改副本，然后覆盖原始元素。

```javascript
// 为演示脱离文档操作，考虑这样一个链接列表，它必须被更多的信息所更新

<ul id="mylist">
	<li><a href="http://phpied.com">Stoyan</a></li>
	<li><a href="http://julienlecomte.com">Julien</a></li>
</ul>

var data = [{
	"name": "Nicholas",
	"url": "http://nczonline.net"
},{
	"name": "Ross",
	"url": "http://techfoolery.com"
}];

// 下面是一个通用的函数，用于将新数据更新到指定节点中

function appendDataToElement(appendToElement, data) {
	var a, li;
	for (var i = 0, max = data.length; i < max; i++) {
		a = document.createElement('a');
		a.href = data[i].url;
		a.appendChild(document.createTextNode(data[i].name));
		li = document.createElement('li');
		li.appendChild(a);
		appendToElement.appendChild(li);
	}
};

// 将数据更新到列表而不管重排版问题，最明显的方法如下

var ul = document.getElementById('mylist');
appendDataToElement(ul, data);
```
使用这个方法，然而， data 队列上的每个新条目追加到 DOM 树都会导致重排版。

**优化方法**

一、改变 display 属性，临时从文档上移除`<ul>`元素然后再恢复它

```javascript
var ul = document.getElementById('mylist');
ul.style.display = 'none';
appendDataToElement(ul, data);
ul.style.display = 'block';
```

二、另一种减少重排版次数的方法是：在文档之外创建并更新一个文档片断，然后将它附加在原始列表上。
文档片断是一个轻量级的 document 对象，它被设计专用于更新、移动节点之类的任务。文档片断一个便
利的语法特性是当你向节点附加一个片断时，实际添加的是文档片断的子节点群，而不是片断自己。下面
的例子减少一行代码，只引发一次重排版，只触发“存在 DOM”一次。

```javascript
var fragment = document.createDocumentFragment();
appendDataToElement(fragment, data);
document.getElementById('mylist').appendChild(fragment);
```
三、首先创建要更新节点的副本，然后在副本上操作，最后用新节点覆盖老节点

```javascript
var old = document.getElementById('mylist');
var clone = old.cloneNode(true);
appendDataToElement(clone, data);
old.parentNode.replaceChild(clone, old);
```
> 推荐尽可能使用文档片断（第二种解决方案）因为它涉及最少数量的 DOM 操作和重排版。唯一潜在的
缺点是，当前文档片断还没有得到充分利用，开发者可能不熟悉此技术。

+ 缓冲布局信息

> 浏览器通过队列化修改和批量运行的方法，尽量减少重排版次数。当你查询布局信息如偏移量、滚动条
位置，或风格属性时，浏览器刷队列并执行所有修改操作，以返回最新的数值。最好是尽量减少对布局信
息的查询次数，查询时将它赋给局部变量，并用局部变量参与计算。

+ 将元素提出动画流

> 重排版有时只影响渲染树的一小部分，但也可以影响很大的一部分，甚至整个渲染树。浏览器需要重排
版的部分越小，应用程序的响应速度就越快。所以当一个页面顶部的动画推移了差不多整个页面时，将引
发巨大的重排版动作，使用户感到动画卡顿。渲染树的大多数节点需要被重新计算，它变得更糟糕。

> 使用以下步骤可以避免对大部分页面进行重排版：
1、页面顶部可以“折叠/展开”的元素称作“动画元素”，用绝对坐标对它进行定位，当它的尺寸改变时，就
不会推移页面中其他元素的位置，而只是覆盖其他元素。
2、展开动作只在“动画元素”上进行。这时其他元素的坐标并没有改变，换句话说，其他元素并没有因为“动
画元素”的扩大而随之下移，而是任由动画元素覆盖。
3、 “动画元素”的动画结束时，将其他元素的位置下移到动画元素下方，界面“跳”了一下。

### DOM事件处理用户响应

+ 事件托管

### 总结

最小化 DOM 访问，在 JavaScript 端做尽可能多的事情

在反复访问的地方使用局部变量存放 DOM 引用

小心地处理 HTML 集合，因为他们表现出“存在性”，总是对底层文档重新查询。将集合的 length 属性缓
存到一个变量中，在迭代中使用这个变量。如果经常操作这个集合，可以将集合拷贝到数组中

如果可能的话，使用速度更快的 API，诸如 querySelectorAll()和 firstElementChild

注意重绘和重排版；批量修改风格，离线操作 DOM 树，缓存并减少对布局信息的访问

动画中使用绝对坐标，使用拖放代理

使用事件托管技术最小化事件句柄数量