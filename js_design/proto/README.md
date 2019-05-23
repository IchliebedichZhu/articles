# JS设计模式之原型模式

---------------------

### 基于原型的面向对象语言
  JavaScript是一门**基于原型**的面向对象语言，它的对象系统是使用原型模式来搭建的。  
  原型模式是用于**创建对象**的一种模式，它不需要先指定它的类型，然后通过类来创建这个对象。  
  而是找到一个对象，通过**克隆**来创建一个一模一样的对象。  
  
  假如我们在一个游戏的兵工厂里面批量生产战争机器人🤖，每个机器人都有默认的血量100和等级1。  
  现在我们的间谍在敌方工厂中窃取到一些核心技术并且运用到我方机器人中，每个默认的参数变为血量200和等级2。
  
  那么使用**克隆**的原型模式可以这样写
```javascript
    var Machine = function(){
        this.blood = 100;
        this.level = 1;
    }
    
    var machine = new Machine();
    machine.blood = 200;
    machine.level = 2;
    
    //兼容Object.create
    Object.create = Object.create || function (obj){
        var Fun = function(){};
        Fun.prototype = obj;
        return new Fun();
    };
    
    var cloneMachine = Object.create(machine);
    
    console.log(cloneMachine);
```

  输出
 
![](https://user-gold-cdn.xitu.io/2019/5/21/16ad768531c8bb38?w=632&h=116&f=png&s=11965)
  这样，我们每次调用 ``` Object.create(machine) ``` 就相当于生产出一个战争机器人，并且生产出来的机器人都是升级后的机器人🤖️  
  如果不使用原型模式，我们则需要先保存血量和等级，然后再设置这些信息到新的机器人上面。
  
---------------------
  
### 原型编程的基本规则

1. **所有数据都是对象**  
    JavaScript引入了两套机制：基本类型和对象类型，因为基本类型的存在，所以在JS中只是**绝大部分**数据是对象。  
当然有些基本类型也可以转成对象类型，例如：
```javascript 
    new String('test');
    new Number(666);
    new Boolean(true);
```

2. **要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它**   

    MDN中原型与原型链和有这样一段描述  
    > 几乎所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例。  
    >
    > 在调用 `new Object()` 时引擎会从 `Object.prototype`出克隆一个对象出来，整个过程都是由JS引擎负责实现

    来看一段代码
```javascript
    function CreateObject(){
        var obj = new Object(),                     //从Object.prototype克隆一个新对象
            Constructor = [].shift.call(arguments); //取出传入的第一个参数
        obj.__proto__ = Constructor.prototype;      //将新对象原型指向外部传入的构造器原型
        
        var result = Constructor.apply(obj,arguments); //给新对象设置属性
        
        return typeof result === 'object' ? result : obj; //确保返回的是一个对象
    }
    
    function Person(name){
        this.name = name;
    }
    
    Person.prototype.getName = function(){
        return this.name;
    }
    
    var x = CreateObject(Person,'shirley');
    
    console.log(x.name);
    console.log(x.getName());
    console.log(Object.getPrototypeOf(x) === Person.prototype);
```

没错，这是模拟new操作符的代码，想了解更多可以[点这里](https://juejin.im/post/5c11f3aaf265da61441feaec)。

在`new`构造一个对象的第一步就是先从`Object.prototype`中克隆出一个对象，然后再在这个对象中进行一些其他操作。


3. **对象会记住它的原型。**  

    上面代码中有这句 `obj.__proto__ = Constructor.prototype;`
    

4. **如果对象无法响应某个请求，它会把这个请求委托给它自己的原型。**  
    

---------------------

### 写在最后
从去年上班摸鱼的时候无意中发现的掘金，到现在为止在掘金混了也有大半年了，感觉这里是在摸鱼的同时也能学习到技术的地方（沸点摸鱼是才是真正的目的！！！）。  
看过很多技术文章却还没有写过文章，最近感觉自己对学习有些放松了，所以想写一些技术文章来巩固一下自己的基础并且梳理一下JS的知识...（本文是小弟长这么大第一次在网上发的文章，加之小弟也是学渣一枚，如有错误请指出，望大佬们轻喷！！）

---------------------

### 参考
> 《JavaScript设计模式与开发实践》   
>  [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain) 