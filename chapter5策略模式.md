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
场景1：让小球进行运动（使用策略模式写一个小球运动的动画类）		

场景2：表单校验		
写法1：

```
		var registerForm = document.getElementById('registerForm');
		registerForm.onsubmit = function(){
			// 验证姓名不为空
			if(registerForm.userName.value ){
				console.log('用户名不为空')
				return false
			}
			// 验证密码长度
			if(registerForm.password.value.length < 6 ){
				console.log('密码长度小于6位')
				return false
			}

			// 验证手机号为符合格式
			if( !/ /.test(registerForm.telPhone.value)  ){
				console.log('手机号格式不正确')
				return false
			}
		}

```
缺点非常明显，复用性差，维护不宜。		

写法2：使用策略模式		

```
	var stratgeies = {
			'isEmptyValue' : function( val ,errorMsg ){
				if( val === '' ){
					return errorMsg;
				}
			},
			'minLenth' : function( val , length ,errorMsg ){
				if( val.length < length ){
					return errorMsg;
				}
			},
			'isMolie' : function( val, errorMsg ){
				if(!reg.test(val)){
					return errorMsg;
				}
			}
		} 

		//计算校验规则的方法
		var validataFunc = function(){
			var validator = new Validator();
			// 往类里面添加方法
			validator.add(registerForm.userName.value, 'isEmptyValue' ,'用户名不为空')
			validator.add(registerForm.password.value.length, 'minLenth:6' ,'密码长度不小于6')
			validator.add(registerForm.telPhone.value, 'isMolie' ,'手机格式不正确')

			var errorMsg = validator.start()
			return errorMsg;
		}

		var Validator = function(){
			this.cache = [];
		}

		Validator.prototype.add = function(dom,rule,errorMsg){
			var ary = rule.split(':');
			this.cache.push(function(){
				var strategy = ary.shift();
				ary.unshift( dom.value );
				ary.push( errorMsg );
				return stratgeies[ strategy ].apply(dom,ary);
			})
		}

		Validator.prototype.start = function(){
			for(var i = 0 , validatorFunc; validatorFunc = this.cache[ i++ ];){
				var msg = validatorFunc();
				if(msg){
					return msg;
				}
			}
		}

```

非常优雅的写法，给大神跪了。


