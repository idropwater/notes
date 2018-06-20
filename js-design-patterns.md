# js 设计模式

- 概念： 大家都知道设计经验有很重要的价值，内行的设计者都知道：不是解决任何问题都要从头开始，他们更愿意复用以前使用过的方案。我们也经常会遇到-已经解决过了一个问题但就是不能确切知道在什么地方或是如何解决的。so设计模式就是将设计经验以人们能够有效利用的形式记录下来

- 按照目的分类：创建型（与对象的创建有关）、结构型（处理类与对象的组合）、行为模式（对类和对象怎样交互和怎样分配职责进行描述）

- 按照范围分类：类模式（处理类与子类关系，编译时刻就确定下来了）、对象模式（处理对象之间关系，运行时刻是可以变化的）
	
## 创建型
-  单例模式singleton
   - 意图：保证一个类只有一个实例，并提供一个访问它的全局访问点
   - 实现：先检查是否已经创建实例，如果有直接返回实例，如果没有先创建实例再返回
        
	```
	function Singleton () {
	    let instance
	    if (instance !== undefined) {
	        return instance
	    }
	    return new Singleton()
	}
	```
- 构造函数模式constructor
  - 意图：用于创建特定的对象
  - 实现：使用new调用构造函数，this指代新创建的对象

	```
	function constructor (a, b) {
	    this.a = a
	    this.b = b
	    this.getSum = function () {
	        return this.a + this.b
	    }
	}
	```

- 原型模式prototype
  - 意图：js通过原型继承， 子类通过原型链往上查找属性和方法
  - 实现：Object.create可实现原型继承

	```
	const parent = {
	    x: ‘x’,
	    getX: function () {}
	}
	
	const child = Object.create(parent)
	
	console.log(child.x) // x
	```

- 工厂方法factory
   - 意图：定义一个用于创建对象的接口，让子类决定实例化哪一个类，使其创建过程延迟到子类进行
   - 实现：在工厂方法中根据条件去判断应该实例化那一个类

	```
	const product = {}
	
	product.circle = () => {}

	product.square = () => {}
	
	product.factory (type) {
		if (type == undefined)
			return null
		if (type === 'CIRCLE') {
			return new . product.circle()
		} else if (type === 'SQUARE') {
			return new product.square()
		}
	}
	```

## 结构型
- 装饰者decorator
   - 意图：动态地给一个对象添加一些额外的职责
   - 实现：es6 修饰器（Decorator）函数，不能用于函数

	```
	@testable
	class MyTestableClass {
	  // ...
	}
	
	function testable(target) {
	  target.isTestable = true;
	}
	
	MyTestableClass.isTestable // true

	```

- 代理Proxy
   - 意图：为其他对象提供一种代理以控制对这个对象的访问。可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截
   - 实现：es6 Proxy

   ```
   // es6 Proxy
   const obj = new Proxy({}, {
	  get: function (target, key, receiver) {
	    console.log(`getting ${key}!`);
	    return Reflect.get(target, key, receiver);
	  },
	  set: function (target, key, value, receiver) {
	    console.log(`setting ${key}!`);
	    return Reflect.set(target, key, value, receiver);
	  }
	});
	
	
	obj.count = 1
	//  setting count!
	++obj.count
	//  getting count!
	//  setting count!
	//  2
   ```
   
   ```
	/*代理实现原理*/
	function ProxyCopy(target,handle){
	  const targetCopy = clone(target); // 浅拷贝
	  Object.keys(targetCopy).forEach(function(key){
	    Object.defineProperty(targetCopy, key, {
	      get: function() {
	        return handle.get && handle.get(target,key);
	      },
	      set: function(newVal) {
	        handle.set && handle.set();
	        target[key] = newVal;
	      }
	    });
	  })
	  return targetCopy;
	}
	
	const person = {name:''};
	const personCopy = new ProxyCopy(person,{
	  get: function (target,key) {
	    console.log('get方法被拦截。。。');
	    return target[key];
	  },
	  set: function (target,key,value) {
	    console.log('set方法被拦截。。。')
	  }
	})
	person.name = 'arvin';  // 未有拦截日志打出
	personCopy.name = 'arvin';  // set方法被拦截。。。
	console.log(person.name);   // 未有拦截日志打出
	console.log(personCopy.name);   // get方法被拦截。。。
   ```

## 行为模式
- 迭代器Iterator
	- 意图：提供一种方法顺序访问一个聚合对象中的各个元素
	- 实现：es6 遍历器（Iterator）

	```
	// es6 遍历器（Iterator）
	class RangeIterator {
	  constructor(start, stop) {
	    this.value = start;
	    this.stop = stop;
	  }
	
	  [Symbol.iterator]() { return this; }
	
	  next() {
	    var value = this.value;
	    if (value < this.stop) {
	      this.value++;
	      return {done: false, value: value};
	    }
	    return {done: true, value: undefined};
	  }
	}
	
	function range(start, stop) {
	  return new RangeIterator(start, stop);
	}
	
	for (var value of range(0, 3)) {
	  console.log(value); // 0, 1, 2
	}
	```
	```
	// 迭代器实现原理
	var it = makeIterator(['a', 'b']);

	it.next() // { value: "a", done: false }
	it.next() // { value: "b", done: false }
	it.next() // { value: undefined, done: true }
	
	function makeIterator(array) {
	  var nextIndex = 0;
	  return {
	    next: function() {
	      return nextIndex < array.length ?
	        {value: array[nextIndex++], done: false} :
	        {value: undefined, done: true};
	    }
	  };
	}
	```

- 观察者observer
	- 意图：叫发布订阅模式,定义对象间的一对多多依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新

	```
	function Observer () {
		this.handlerList = [];
	}
	
	Observer.prototype = {
		subscribe: function (obs) {
			this.handlerList.push(obs);
			console.log('add observer');
		},
		
		unsubscribe: function (obs) {
		   	for (let ii=0, length = this.handlerList.length; ii<length; ii++ ) {
		    	if(this.handlerList[ii] === obs) {
		          this.handlerList.splice(ii--,1);
		          length--;
		          console.log('removed observer');
		      	}
		    }
		},
		
	  notify: function(obs, context) {
	    const bindingContext = context || window;
	    this.handlerList.forEach(function(hanlder){
	        hanlder.call(bindingContext, obs);
	    });
	  }
	}
	
	const evenHanlder = function (item) {
		console.log('fired', item);
	}
	
	const observer = new Observer();
	
	observer.subscribe(evenHanlder); // add observer
	
	observer.notify('event1'); // fired event1
	
	observer.unsubscribe(evenHanlder); // removed observer
	```
	
	
	
	
	
	
	
	
	