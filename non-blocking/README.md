## non-blocking scripts 非阻塞脚本

>尽管下载一个大 JavaScript 文件只产生一次 HTTP 请求，却会锁定浏览器一大段时间。为避开这种情况，你
需要向页面中逐步添加 JavaScript，某种程度上说不会阻塞浏览器。

+ Deferred Scripts 延期脚本

在脚本script标签上加上"defer"字段，即表明此脚本为一个延期加载的脚本，该脚本触发时间为：在DOM加载之后，在页面Load之前

问题：在书中说明了只要在script中加上了"defer"字段，都会被认为是延期脚本，但在实际测试中，发现只有在外部引入的脚本上加入"defer"字段才会有延期脚本的效果，只有在这种情况下，才能出现脚本的并行加载情况

**defer支持情况**
![canIuse_defer](./screensnap/canIuse.png)

**测试环境**
Chrome 49/ FireFox 45/ IE exploer 10

**结果**
```javascript
	<script src="js/defer.js" defer></script>
	<script src="js/normal.js"></script>
	<script>
		window.onload = function() {
			console.log("load");
		}
	</script>
```

控制台依次打印：normal -> defer -> load，浏览器并行加载js
![callback](./screensnap/load.png)

如果去掉 defer 字样， 控制台打印结果为： defer -> normal -> load

另外如果代码为直接内嵌
```javascript
	<script defer>
		console.log("defer");
	</script>
	<script>
		console.log("normal");
	</script>
	<script>
		window.onload = function() {
			console.log("load");
		}
	</script>
```

控制台打印结果为： defer -> normal -> load， 发现并未出现书中的defer情况

+ Dynamic Script Elements 动态脚本元素

>此技术的重点在于：无论在何处启动下载，文件的下载和运行都不会阻塞其他页面处理过程。你甚至可以将这些代码放在
<head>部分而不会对其余部分的页面代码造成影响（除了用于下载文件的 HTTP 连接）。

```javascript
	function loadScript(url, callback) {
		var script = document.createElement("script");
		if(script.readyState) { // IE
			script.onreadystatechange = function() {
				if(script.readyState === 'loaded' || script.readyState === 'complete') {
					script.onreadystatechange = null;
					callback();
				}
			}
		} else {	// Others
			script.onload = function() {
				callback;
			};
		}
		script.src = url;
		document.getElementsByTagName_r("head")[0].appendChild(script);
	}
```
>你可以在页面中动态加载很多 JavaScript 文件，但要注意，浏览器不保证文件加载的顺序。所有主流浏
览器之中，只有 Firefox 和 Opera 保证脚本按照你指定的顺序执行。其他浏览器将按照服务器返回它们的次
序下载并运行不同的代码文件。你可以将下载操作串联在一起以保证他们的次序，如下：

```javascript
loadScript("file1.js", function() {
	loadScript("file2.js", function() {
		loadScript("file3.js", function() {
			alert("All files are loaded!");
		});
	})
})
```
>如果多个文件的次序十分重要，更好的办法是将这些文件按照正确的次序连接成一个文件。独立文件可
以一次性下载所有代码（由于这是异步进行的，使用一个大文件并没有什么损失）。




