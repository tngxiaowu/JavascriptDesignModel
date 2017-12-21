# JavascriptDesignModel


		// 1个简单的单例模式写法
		// 设计模式特点：
		// 用一个变量来标志当前是否为某个类创造过对象
		// 如果是，那么下一次获取该类的实例时，就返回之前创建的对象；
		var SingleModel = function(name){
			this.name = name;
			this.instance = null;
		}

		SingleModel.prototype.getName = function(){
			console.log(this.name);
		}

		//写法1：常规写法
		SingleModel.getInstance = function(name){
			//用一个变量来标志当前是否为某个类创造过对象
			//如果是，那么下一次获取该类的实例时，就返回之前创建的对象；
			if(!this.instance){
				this.instance = new SingleModel(name);
			}
			return this.instance;
		}

		//写法2：更高级的闭包写法
		SingleModel.getInstance = (function(){
			var instance = null;
			return function(name){
				if( !instance ){
					instance = new SingleModel(name);
				}
				return instance;
			}
		})()

		//缺点 这是一个“不透明”的类，使用者必须知道这是一个单例模式的类，而且需要用SingleModel.getInstance来获取对象
		var a = SingleModel.getInstance('Seven1');// SingleModel {name: "Seven1", instance: null}
		var b = SingleModel.getInstance('Seven2');// SingleModel {name: "Seven1", instance: null}
		var c = SingleModel.getInstance('Seven3');// SingleModel {name: "Seven1", instance: null}

		console.log(a);
		console.log(b);
		console.log(c);

		//一个透明类的编写
		var CreateDiv = (function(){
			//instance是局部变量
			var instance;
			console.log(instance);
			var CreateDiv = function(html){
				// 如果存在实例 那么返回之前已经创造好的实例 如果没有，那么继续
				if(instance){
					return instance;
				}
				this.html = html;
				this.init();
				//这里的this指向的是实例化对象的CreateDiv
				return instance = this;
			}

			CreateDiv.prototype.init = function(){
				var div = document.createElement('div');
				div.innerHTML = this.html;
				document.body.appendChild(div);
			}

			return CreateDiv;
		})();

		var a = new CreateDiv('hello');
		var b = new CreateDiv('world');
		console.log('a',a);
		console.log('b',b);
		console.log(a === b);
		//缺点：1.不便于阅读； 2.日后修改起来也蛮麻烦的，从单一实例到多个不同实例

		//用代理实现单例模式
			var CreateDiv = function(html){
				this.html = html;
				this.init();
			}

			CreateDiv.prototype.init = function(){
				var div = document.createElement('div');
				div.innerHTML = this.html;
				document.body.appendChild(div);
			}
		

			//创造出proxyCreeDiv来连接与CreateDiv的关系
			var proxyCreateDiv = (function(){
				var instance;
				return function(html){
					if( !instance ){
						instance = new CreateDiv(html);
					}
					return instance;
				}
			})()

			var a = new proxyCreateDiv('hello');
			var b = new proxyCreateDiv('world');

			console.log('a',a);
			console.log('b',b);
			console.log(a === b);