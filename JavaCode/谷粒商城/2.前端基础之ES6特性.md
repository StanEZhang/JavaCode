# 一、变量

```js
// var 声明的变量往往会越域
// let 声明的变量有严格局部作用域
{
var a = 1;
let b = 2;
}
console.log(a); // 1
console.log(b); // ReferenceError: b is not defined


// var 可以声明多次
// let 只能声明一次
var m = 1
var m = 2
let n = 3
// let n = 4
console.log(m) // 2
console.log(n) // Identifier 'n' has already been declared


// var 会变量提升
// let 不存在变量提升
console.log(x); // undefined
var x = 10;
console.log(y); //ReferenceError: y is not defined
let y = 20;


// 1. 声明之后不允许改变
// 2. 一但声明必须初始化，否则会报错
const a = 1;
a = 3; //Uncaught TypeError: Assignment to constant variable.
```



# 二、结构表达式

## 1.数组解构

```js
let arr = [1,2,3];
//以前我们想获取其中的值，只能通过角标。ES6 可以这样：
const [x,y,z] = arr;// x，y，z 将与 arr 中的每个位置对应来取值
// 然后打印
console.log(x,y,z);
```



## 2.对象解构

```js
const person = {
name: "jack",
age: 21,
language: ['java', 'js', 'css']
}
// 解构表达式获取值，将 person 里面每一个属性和左边对应赋值
const { name, age, language } = person;
// 等价于下面
// const name = person.name;
// const age = person.age;
// const language = person.language;
// 可以分别打印
console.log(name);
console.log(age);
console.log(language);
//扩展：如果想要将 name 的值赋值给其他变量，可以如下,nn 是新的变量名
const { name: nn, age, language } = person;
console.log(nn);
console.log(age);
console.log(language);
```



# 三、字符串扩展

## 1.几个新的api

```js
let str = "hello.vue";
console.log(str.startsWith("hello"));//true
console.log(str.endsWith(".vue"));//true
console.log(str.includes("e"));//true
console.log(str.includes("hello"));//true
```



## 2.字符串模板

模板字符串相当于加强版的字符串，用反引号 `,除了作为普通字符串，还可以用来定义多行字符串，还可以在字符串中加入变量和表达式。

```js
// 1、多行字符串
let ss = `
<div>
<span>hello world<span>
</div>
`
console.log(ss)


// 2、字符串插入变量和表达式。变量名写在 ${} 中，${} 中可以放
入 JavaScript 表达式。
let name = "张三";
let age = 18;
let info = `我是${name}，今年${age}了`;
console.log(info)


// 3、字符串中调用函数
function fun() {
return "这是一个函数"
}
let sss = `O(∩_∩)O 哈哈~，${fun()}`;
console.log(sss); // O(∩_∩)O 哈哈~，这是一个函数
```

# 四、函数优化

## 1.函数参数默认值

```js
//在 ES6 以前，我们无法给一个函数参数设置默认值，只能采用变通写法：
function add(a, b) {
// 判断 b 是否为空，为空就给默认值 1
b = b || 1;
return a + b;
}
// 传一个参数
console.log(add(10));


//现在可以这么写：直接给参数写上默认值，没传就会自动使用默认值
function add2(a , b = 1) {
return a + b;
}
// 传一个参数
console.log(add2(10));
```



## 2.不定参数

不定参数用来表示不确定参数个数，形如，...变量名，由...加上一个具名参数标识符组成。

具名参数只能放在参数列表的最后，并且有且只有一个不定参数。

```js
function fun(...values) {
console.log(values.length)
}
fun(1, 2) //2
fun(1, 2, 3, 4) //4
```



## 3.箭头函数

ES6 中定义函数的简写方式

- 一个参数时

```js
//以前声明一个方法
// var print = function (obj) {
// console.log(obj);
// }
// 可以简写为：
var print = obj => console.log(obj);
// 测试调用
print(100);

```

- 多个参数时

```js
// 两个参数的情况：
var sum = function (a, b) {
return a + b;
}
// 简写为：
//当只有一行语句，并且需要返回结果时，可以省略 {} , 结果会自动返回。
var sum2 = (a, b) => a + b;
//测试调用
console.log(sum2(10, 10));//20


// 代码不止一行，可以用`{}`括起来
var sum3 = (a, b) => {
c = a + b;
return c;
};
//测试调用
console.log(sum3(10, 20));//30
```



## 4.实战：箭头函数结合解构表达式

```js
//需求，声明一个对象，hello 方法需要对象的个别属性
//以前的方式：
const person = {
name: "jack",
age: 21,
language: ['java', 'js', 'css']
}
function hello(person) {
console.log("hello," + person.name)
}
//现在的方式
var hello2 = ({ name }) => { console.log("hello," + name) };
//测试
hello2(person);
```



# 五、对象优化

## 1.新增的API

ES6 给 Object 拓展了许多新的方法，如：
- `keys(obj)`：获取对象的所有 key 形成的数组
- `values(obj)`：获取对象的所有 value 形成的数组
- `entries(obj)`：获取对象的所有 key 和 value 形成的二维数组。格式：`[[k1,v1],[k2,v2],...]` 
- `assign(dest, ...src) `：将多个 src 对象的值 拷贝到 dest 中。（第一层为深拷贝，第二层为浅拷贝）

`assign`:分派，布置(工作、任务等);指定，确定;转让，让与（财产、权利）;分配(某物);委派，派遣;把…归因 ( 于 ) ，把…归属 ( 于 )

```js
const person = {
name: "jack",
age: 21,
language: ['java', 'js', 'css']
}
console.log(Object.keys(person));//["name", "age", "language"]
console.log(Object.values(person));//["jack", 21, Array(3)]
console.log(Object.entries(person));//[Array(2), Array(2), Array(2)]

const target = { a: 1 };
const source1 = { b: 2 };
const source2 = { c: 3 };
//Object.assign 方法的第一个参数是目标对象，后面的参数都是源对象。
Object.assign(target, source1, source2);
console.log(target)//{a: 1, b: 2, c: 3}
```



## 2.声明对象的简写

```js
const age = 23
const name = "张三"
// 传统
const person1 = { age: age, name: name }
console.log(person1)
// ES6：属性名和属性值变量名一样，可以省略
const person2 = { age, name }
console.log(person2) //{age: 23, name: "张三"}
```



## 3.对象的函数属性简写

```js
let person = {
name: "jack",
// 以前：
eat: function (food) {
console.log(this.name + "在吃" + food);
},
    
// 箭头函数版：这里拿不到 this
eat2: food => console.log(person.name + "在吃" + food),
    
// 简写版：
eat3(food) {
console.log(this.name + "在吃" + food);
}
}
person.eat("apple");
```



## 4.对象拓展运算符

拓展运算符（...）用于取出参数对象所有可遍历属性然后拷贝到当前对象。

```js
// 1、拷贝对象（深拷贝）
let person1 = { name: "Amy", age: 15 }
let someone = { ...person1 }
console.log(someone) //{name: "Amy", age: 15}
// 2、合并对象
let age = { age: 15 }
let name = { name: "Amy" }
let person2 = { ...age, ...name } //如果两个对象的字段名重复，后面对象字段值会覆盖前面对象的字段值
console.log(person2) //{age: 15, name: "Amy"}
```

# 六、map和reduce

**数组**中新增了 map 和 reduce 方法。

## 1.map

`map()`：接收一个函数，将原数组中的所有元素用这个函数处理后放入新数组返回。

```js
let arr = ['1', '20', '-5', '3'];
console.log(arr)
arr = arr.map(s => parseInt(s));
console.log(arr)
```



## 2.reduce

语法：`arr.reduce(callback,[initialValue])`

reduce 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素。

callback （执行数组中每个值的函数，包含四个参数）

1、previousValue （上一次调用回调返回的值，或者是提供的初始值（initialValue））

2、currentValue （数组中当前被处理的元素）

3、index （当前元素在数组中的索引）

4、array （调用 reduce 的数组）

initialValue （作为第一次调用 callback 的第一个参数。）

```js
const arr = [1,20,-5,3];
//没有初始值：
console.log(arr.reduce((a,b)=>a+b));//19
console.log(arr.reduce((a,b)=>a*b));//-300
//指定初始值：
console.log(arr.reduce((a,b)=>a+b,1));//20
console.log(arr.reduce((a,b)=>a*b,0));//-0
```

# 七、Promise

在 JavaScript 的世界中，所有代码都是单线程执行的。由于这个“缺陷”，导致 JavaScript 的所有网络操作，浏览器事件，都必须是异步执行。异步执行可以用回调函数实现。一旦有一连串的 ajax 请求 a,b,c,d... 后面的请求依赖前面的请求结果，就需要层层嵌套。这种缩进和层层嵌套的方式，非常容易造成上下文代码混乱，我们不得不非常小心翼翼处理内层函数与外层函数的数据，一旦内层函数使用了上层函数的变量，这种混乱程度就会加剧......总之，这种层叠上下文的层层嵌套方式，着实增加了神经的紧张程度。

案例：用户登录，并展示该用户的各科成绩。在页面发送两次请求：

1.查询用户，查询成功说明可以登录

2.查询用户成功，查询科目

3.根据科目的查询结果，获取去成绩

分析：此时后台应该提供三个接口，一个提供用户查询接口，一个提供科目的接口，一个提供各科成绩的接口，为了渲染方便，最好响应 json 数据。在这里就不编写后台接口了，而是提供三个 json 文件，直接提供 json 数据，模拟后台接口：

```json
user.json：
{ 
    "id": 1, 
    "name": "zhangsan", 
    "password": "123456"
}

user_corse_1.json:
{ 
    "id": 10, 
    "name": "chinese"
}

corse_score_10.json:
{ 
    "id": 100, 
    "score": 90
}
```



```js
//回调函数嵌套的噩梦：层层嵌套。
$.ajax({
	url: "mock/user.json",
	success(data) {
		console.log("查询用户：", data);
		$.ajax({
			url: `mock/user_corse_${data.id}.json`,
			success(data) {
				console.log("查询到课程：", data);
				$.ajax({
					url: `mock/corse_score_${data.id}.json`,
					success(data) {
						console.log("查询到分数：", data);
					},
					error(error) {
						console.log("出现异常了：" + error);
					}
				});
			},
			error(error) {
				console.log("出现异常了：" + error);
			}
		});
	},
	error(error) {
		console.log("出现异常了：" + error);
	}
});
```

我们可以通过 Promise 解决以上问题。

## 1.Promise语法

```js
const promise = new Promise(function (resolve, reject) {
	// 执行异步操作
	if (/* 异步操作成功 */) {
		resolve(value);// 调用 resolve，代表 Promise 将返回成功的结果
	} else {
		reject(error);// 调用 reject，代表 Promise 会返回失败结果
	}
});
```

使用箭头函数可以简写为：

```js
const promise = new Promise((resolve, reject) =>{
	// 执行异步操作
	if (/* 异步操作成功 */) {
		resolve(value);// 调用 resolve，代表 Promise 将返回成功的结果
	} else {
		reject(error);// 调用 reject，代表 Promise 会返回失败结果
	}
});
```

这样，在 promise 中就封装了一段异步执行的结果。



## 2.处理异步结果

如果我们想要等待异步执行完成，做一些事情，我们可以通过 promise 的 then 方法来实现。

如果想要处理 promise 异步执行失败的事件，还可以跟上 catch：

```js
promise.then(function (value) {
	// 异步执行成功后的回调
}).catch(function (error) {
	// 异步执行失败后的回调
})
```



## 3.Promise改造以前嵌套方式

```js
new Promise((resolve, reject) => {
	$.ajax({
		url: "mock/user.json",
		success(data) {
			console.log("查询用户：", data);
			resolve(data.id);
		},
		error(error) {
			console.log("出现异常了：" + error);
		}
	});
}).then((userId) => {
	return new Promise((resolve, reject) => {
		$.ajax({
			url: `mock/user_corse_${userId}.json`,
			success(data) {
				console.log("查询到课程：", data);
				resolve(data.id);
			},
			error(error) {
				console.log("出现异常了：" + error);
			}
		});
	});
}).then((corseId) => {
	console.log(corseId);
    
	$.ajax({
		url: `mock/corse_score_${corseId}.json`,
		success(data) {
			console.log("查询到分数：", data);
		},
		error(error) {
			console.log("出现异常了：" + error);
		}
	});
});
```



## 4.优化处理

优化：通常在企业开发中，会把 promise 封装成通用方法，如下：封装了一个通用的 get 请求方法；

```js
let get = function (url, data) { // 实际开发中会单独放到 common.js 中
	return new Promise((resolve, reject) => {
		$.ajax({
			url: url,
			type: "GET",
			data: data,
			success(result) {
				resolve(result);
			},
			error(error) {
				reject(error);
			}
		});
	})
}

// 使用封装的 get 方法，实现查询分数
get("mock/user.json").then((result) => {
	console.log("查询用户：", result);
	return get(`mock/user_corse_${result.id}.json`);
}).then((result) => {
	console.log("查询到课程：", result);
	return get(`mock/corse_score_${result.id}.json`)
}).then((result) => {
	console.log("查询到分数：", result);
}).catch(() => {
	console.log("出现异常了：" + error);
});
```

通过比较，我们知道了 Promise 的扁平化设计理念，也领略了这种上层设计带来的好处。

我们的项目中会使用到这种异步处理的方式。

# 八、模块化

## 1.什么是模块化

模块化就是把代码进行拆分，方便重复利用。类似 java 中的导包：要使用一个包，必须先导包。而 JS 中没有包的概念，换来的是模块。
模块功能主要由两个命令构成：`export`和`import`。	

- `export`命令用于规定模块的对外接口。
- `import`命令用于导入其他模块提供的功能。

## 2.export

比如我定义一个 js 文件:hello.js，里面有一个对象。

```js
const util = {
	sum(a,b){
		return a + b;
	}
}
```

我可以使用 export 将这个对象导出：

```js
const util = {
	sum(a,b){
		return a + b;
	}
}
export {util};
```

当然，也可以简写为：

```js
export const util = {
	sum(a,b){
		return a + b;
	}
}
```

`export`不仅可以导出对象，一切 JS 变量都可以导出。比如：基本类型变量、函数、数组、对象。

当要导出多个值时，还可以简写。比如我有一个文件：user.js：

```js
var name = "jack"
var age = 21
export {name,age}
```

**省略名称**

上面的导出代码中，都明确指定了导出的变量名，这样其它人在导入使用时就必须准确写出变量名，否则就会出错。

因此 js 提供了`default`关键字，可以对导出的变量名进行省略。

例如：

```js
// 无需声明对象的名字
export default {
	sum(a,b){
		return a + b;
	}
}
```

这样，当使用者导入时，可以任意起名字。

## 3.import

使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过`import`命令加载这个模块。

例如我要使用上面导出的 util：

```js
// 导入 util
import util from 'hello.js'
// 调用 util 中的属性
util.sum(1,2)
```

要批量导入前面导出的 name 和 age：

```js
import {name, age} from 'user.js'
console.log(name + " , 今年"+ age +"岁了")
```

