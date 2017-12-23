# JavascriptDesignModel

## Chpater 4:单列模式		
	
- 设计模式思路:		
用一个变量来标志当前是否为某个类创造过对象,如果是，那么下一次获取该类的实例时，就返回之前创建的对象；		
- 设计模式代码

```
	var obj;
	if(!obj){
		obj = xxx
	}
```
		

*******************
### 写法1:简单演示		

		var SingleModel = function(name){
			this.name = name;
			this.instance = null;
		}

		SingleModel.prototype.getName = function(){
			console.log(this.name);
		}

		// getInstance作为构造函数上的方法
		SingleModel.getInstance = function(name){
			if(!this.instance){
				this.instance = new SingleModel(name);
			}
			return this.instance;
		}		
			
		缺点：
		使用者需要使用getInsatnce这个方法，和我们一般的使用new XXX之类的来获取对象不同。
********************

### 写法2：闭包写法
		
		SingleModel.getInstance = (function(){
			var instance = null;
			return function(name){
				if( !instance ){
					instance = new SingleModel(name);
				}
				return instance;
			}
		})()
		缺点：
		同上,只不过用了闭包，优雅很多。
*********
		
### 写法3：使用自执行匿名函数+闭包

		var CreateDiv = (function(){
			// 在这个构造函数中，它主要有2个职责：1.创建对象和执行初始化方法； 2.确保只有一个对象 这并不是一种很好的做法。
			var CreateDiv = function(html){
				if(instance){
					return instance;
				}
				this.html = html;
				this.init();
				return instance = this; //这里的this指向的是实例化对象的CreateDiv
			}

			// init方法 用于创建页面中单一的div节点
			CreateDiv.prototype.init = function(){
				var div = document.createElement('div');
				div.innerHTML = this.html;
				document.body.appendChild(div);
			}
			return CreateDiv; //这句代码的意义是在哪里？
		})();

缺点：
1.不便于阅读； 
2.日后修改起来也蛮麻烦的(从单一实例到多个不同实例)。

************

### 写法4: 使用代理模式

			// 类型1：CreateDiv类
			var CreateDiv = function(html){
				this.html = html;
				this.init();
			}

			// 初始化方法
			CreateDiv.prototype.init = function(){
				var div = document.createElement('div');
				div.innerHTML = this.html;
				document.body.appendChild(div);
			}
		
			//创造另一个类proxyCreeDiv来连接与CreateDiv的关系
			var proxyCreateDiv = (function(){
				var instance;
				return function(html){
					if( !instance ){
						instance = new CreateDiv(html);
					}
					return instance;
				}
			})()

			// 只要使用代理类的方法就可以
			var a = new proxyCreateDiv('hello');
			var b = new proxyCreateDiv('world');
			console.log(a === b);	//输出true

***********	
			
### 单例模式的实战			

- 方案1 创造一个div(窗口)

```
		var loginLayer = (function(){
			var div = document.createElement('div');
			div.innerHTML = '我是登录';
			div.style.display = 'none';
			document.body.appendChild(div);
			return div;
		})();

		document.getElementById('loginBtn').onclick = function(){
			loginLayer.style.display = 'block';
		}
```
这个div在页面初始化的时候就已经存在，如果用户没有点击，那么岂不是会浪费性能。


- 方案2 在需要的时候创造一个div 但这个div创造的时候是独一无二的
		

```
		var createLoginLayer = function(){
			var div = document.createElement('div');
			div.innerHTML = '我是登录';
			div.style.display = 'none';
			document.body.appendChild(div);
			return div;
		};

		document.getElementById('loginBtn').onclick = function(){
			//在点击的时候就new一个新的对象
			var loginLayer = createLoginLayer();
			var loginLayer2 = createLoginLayer();
			//输出false 都在实例化一个不同的dom对象
			console.log(loginLayer === loginLayer2);
			loginLayer.style.display = 'block';
		}
```

- 方案3：在需要的时候创造一个div 但这个div是之前就创建的	

```
		var createLoginLayer = (function(){
			var div;
			return function(){
				// 判断对象是否唯一
				if(!div){
					// 创建对象
					div = document.createElement('div');
					div.innerHTML = '我是登录';
					div.style.display = 'none';
					document.body.appendChild(div);
				}
				return div;
			}
		})();

		document.getElementById('loginBtn').onclick = function(){
			var loginLayer = createLoginLayer();
			var loginLayer2 = createLoginLayer();
			//输出true 创建了一个一模一样的div
			console.log(loginLayer === loginLayer2);			loginLayer.style.display = 'block';
		}
```
勉勉强强达到了需求，但是能否给一个结构更清晰的代码么？还是违反了单一职责的原则。所以接下来我们还是需要优化！

- 方案4

```
		//创造div的方法
		var creatDiv = function(){
			var div = document.createElement('div');
			div.innerHTML = '我是登录';
			div.style.display = 'none';
			document.body.appendChild(div);
			return div;
		};

		//单例模式的判断逻辑
		var getSingle = function(fn){
			//单例模式的核心判断逻辑
			var result;
			return result || (result = fn.apply(this,arguments));
		};

		// 创造一个单一的元素类
		var creatSingleDiv = getSingle(creatDiv);
			document.getElementById('btn').onclick = function(){
			var longinDom = new creatSingleDiv();
			longinDom.style.display = 'block';
		}
```


