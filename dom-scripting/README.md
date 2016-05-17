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
本节点（这两个标签之间往往只是一些空格）。在许多情况下，只有元素节点需要被访问，所以在循环中，
似乎应当对节点返回类型进行检查，过滤出非元素节点。这些检查和过滤都是不必要的 DOM 操作。

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

### 重绘与重排

### DOM事件处理用户响应