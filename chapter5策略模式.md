## Chpater 5:策略模式		
	
- 设计模式思路:		
定义一系列的算法，把它们一个个封装起来，并且可以使他们可以相互替换。
- 设计模式代码

```
	var obj;
	if(!obj){
		obj = xxx
	}
```
	
>> 设计模式主题		
>> **将不变的部分和变的部分**隔开是每个设计模式共同的主题。	

*******************
场景:写一个绩效考核成绩与奖金的行数
### 1.菜鸟写法	

		function calcuteSlary(performanceLevel,salary){
			if( performanceLevel == 'A' ){
				return salary * 4;
			}
			if( performanceLevel == 'B' ){
				return salary * 3;
			}
			if( performanceLevel == 'C' ){
				return salary * 2;
			}
			if( performanceLevel == 'D' ){
				return salary * 1;
			}
		}			
				
缺点：		
1. 整体函数比较“庞大”，包含了许多if-else语句，需要涵盖众多逻辑；	
2. 代码缺乏弹性，如需修改，那么需要深入函数内部实现；	
3. 代码的复用性也不太好，如果其他地方用到这些计算奖金的方法，需要重复粘贴。
********************

### 写法2：使用组合函数改善
		
		// 使用组合函数改善
		function PerformanceA(salary){
			return salary * 4;
		}

		function PerformanceB(salary){
			return salary * 3;
		}

		function PerformanceC(salary){
			return salary * 2;
		}

		function PerformanceD(salary){
			return salary * 1;
		}

		function calcuteSlary(performanceLevel,salary){
			if( performanceLevel == 'A' ){
				return PerformanceA(salary);
			}
			if( performanceLevel == 'B' ){
				return PerformanceB(salary);
			}
			if( performanceLevel == 'C' ){
				return PerformanceC(salary);
			}
			if( performanceLevel == 'D' ){
				return PerformanceD(salary);
			}
		}		
		
缺点：
解决了代码的复用性，但是函数内依旧非常庞大。
*********
		
### 写法3：使用策略模式改善

		var PerformanceA = function(){};

		PerformanceA.prototype.calculate = function( salary ){
			return salary * 4;
		}

		var PerformanceB = function(){};

		PerformanceB.prototype.calculate = function( salary ){
			return salary * 3;
		}

		var PerformanceC = function(){};

		PerformanceC.prototype.calculate = function( salary ){
			return salary * 2;
		}

		var PerformanceD = function(){};

		PerformanceD.prototype.calculate = function( salary ){
			return salary * 1;
		}

		//定义奖金类
		function Bonus(){
			this.salary = null; //原始工资
			this.strategy = null; //奖金策略
		}

		Bonus.prototype.setSalary = function( salary ){
			this.salary = salary;
		}

		Bonus.prototype.setStrategy = function( Strategy ){
			this.Strategy = Strategy;
		}

		Bonus.prototype.getBonus = function(){
			return this.strategy.calculate( this.salary );
		}
这段代码时基于面向对象的思想写的，下一个写法我们基于传统的javascript写法。

************

### 写法4: 一种更简单/优雅的javascript写法

		var strategies = {
			"A": function( salary ){
				return salary * 4;
			},
			"B": function( salary ){
				return salary * 3;
			},
			"C": function( salary ){
				return salary * 2;
			},
			"D": function( salary ){
				return salary * 1;
			}
		}

		var calculateBonus = function( performance, salary ){
			return strategies[ performance ]( salary );
		}
***********	
			
### 策略模式的实战	
场景：让小球进行运动（使用策略模式写一个小球运动的动画类）		

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


