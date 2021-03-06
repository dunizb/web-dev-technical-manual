# 第6节 构造函数

## 一、什么是构造函数

构造函数就是初始化一个实例对象，对象的prototype属性是继承一个实例对象。

两个点：

1. 初始化数据；
2. 在JS中给对象添加属性用的，初始化属性值用的

构造函数默认函数首字母大写。

构造函数并没有显示返回任何东西。new 操作符会自动创建给定的类型并返回他们，当调用构造函数时，new会自动创建this对象，且类型就是构造函数类型。

也可以在构造函数中显示调用return.如果返回的值是一个对象，它会代替新创建的对象实例返回。如果返回的值是一个原始类型，它会被忽略，新创建的实例会被返回。

```js
function Person( name){
   this.name =name;
}
var p1=new Person('John');
```

等同于：

```js
function person(name ){
    Object obj =new Object();
    obj.name =name;
    return obj;
}
var p1= person("John");
```

因为构造函数也是函数，所以可以直接被调用，但是它的返回值为undefine，此时构造函数里面的this对象等于全局this对象。this.name其实就是创建一个全局的变量name。在严格模式下，当你补通过new 调用Person构造函数会出现错误。

![](http://files.jb51.net/file_images/article/201512/2015122793806486.png?2015112793835)

也可以在构造函数中用Object.defineProperty\(\)方法来帮助我们初始化：

```js
function Person( name){
    Object.defineProperty(this, "name"{
      get :function(){
         return name;
      },
       set:function (newName){
        name =newName;
      },
      enumerable :true, //可枚举，默认为false
       configurable:true //可配置
   });
}  
var p1=new Person('John');
```

在构造函数中使用原型对象

```js
//比直接在构造函数中写的效率要高的多
Person.prototype.sayName= function(){
  console.log(this.name);
};
```

但是如果方法比较多的话，大多人会采用一种更简洁的方法：直接使用一个对象字面形式替换原型对象，如下：

```js
Person.prototype ={
    sayName :function(){
       console.log(this.name);
    },
    toString :function(){
       return "[Person "+ this.name+"]" ;
    }
};
```

这种方式非常流行，因为你不用多次键入Person.prototype，但有一个副作用你一定要注意：

![](http://files.jb51.net/file_images/article/201512/2015122793855680.png?2015112793947)

使用字面量形式改写了原型对象改变了构造函数的属性，因此他指向Object而不是Person。这是因为原型对象具有一个constructor属性，这是其他对象实例所没有的。当一个函数被创建时，它的prototype属性也被创建，且该原型对象的constructor属性指向该函数。当使用对象字面量形式改写原型对象时，其constructor属性将被置为泛用对象Object.为了避免这一点，需要在改写原型对象的时候手动重置constructor,如下：

```js
Person.prototype ={
     constructor :Person,

     sayName :function(){
        console.log(this.name);
     },        
     toString :function(){
        return "[Person "+ this.name+"]" ;
     }
};
```

## 二、构造函数创建对象的过程

以下面代码为例：

```js
var p = new Person();
```

首先，运算符new 创建了一个对象，类似于{}，是一个没有任何（自定义）成员的对象；

* 使用new 创建对象，那么对象的类型就是创建他的构造函数名
* 使用{}无论如何都是Object类型，相当于new Object

然后，调用构造函数，为其初始化成员；

* 构造函数在调用的一开始，有一个赋值操作，即this = 刚刚创建出来的对象。
* 因此在构造函数中this表示刚刚创建出来的对象。

然后，在构造函数中 利用 对象的动态特性 为其对象添加成员；

最后，如果无返回值或者返回一个非对象值，则将新对象返回；如果返回值是一个新对象的话那么直接直接返回该对象。

## 三、分析构造函数执行过程

分析如下代码执行过程：

```js
function Student( name, age ) {
  this.name = name;
  this.age = age;
}
Student.prototype = {
  sayHello: function () {
     console.log( 'hello' );
  }
};
var p = new Student( '张三', 19 );
p.sayHello();
```

1. 首先预解释的过程，申明构造函数Student，同时创建神秘对象Student.prototype.
2. 第一句话执行第5行代码，重新覆盖原型对象，原来默认的原型对象不再被指向。
3. 创建对象，new创建对象，然后将对象的引用交给构造函数的this。
4. 在构造函数的内部，利用对象的动态特性给刚刚创建出来的对象提供属性name和age。
   1. 构造函数在调用的时候传入参数“张三”和19.
   2. 因此对象的属性name为张三，age为19.
   3. 对象完成初始化后返回对象的“地址”给变量p
5. 执行调用方法，p调用sayHello方法
   1. 首先在P所指向的对象中查找sayHello方法
   2. 然后在其原型对象Student.prototype中查找（是新的原型对象）
   3. 在新的原型对象中有sayHello，因此调用它
   4. 在该方法中打印hello
   5. 方法调用结束

---

**参考文章**

* [JavaScript构造函数详解](http://www.jb51.net/article/77039.htm)
* [我遇到的前端面试题2017](https://blog.dunizb.com/2017/09/08/interview-questions-2017/#9-当new-Foo-时发生了什么)



