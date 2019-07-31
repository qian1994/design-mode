# 设计模式

* 单一原则

    一个类或者一个接口，负责一项职责

* 开闭原则

    一个软件实体，类， 模板， 函数应该对外扩展打开， 对外修改关闭

* 里氏替换原则

    子类继承父类方法， 不能覆盖父类的非抽象方法
    子类可以增加自己特有的方法
    当子类的方法重载父类的方法时， 方法的形参要比父类方法输入参数更加宽松
    当子类的方法实现父类的抽象方法时， 方法的返回值要比父类更加严格

* 依赖倒置原则

    底层模块精良都要有抽象类或者接口，或者两者都有
    变量的声明尽量是抽象类或者接口
    使用继承时遵循李氏替换原则

* 接口隔离原则

    一个接口只服务一个子模块或者雨雾逻辑， 服务定制
    通过业务逻辑压缩接口中的public方法， 让接口看起来更加精悍
    已经被污染了的接口，尽量修改， 如果变更风险太大，用适配器模式进行转化
    根据具体的业务，深入了解逻辑， 用心感知去控制设计思路

* 迪米特原则

    一个对象应该对其他对象保持最少的了解， 核心精神就是： 不要和陌生人说话， 通俗之意就是一个对象对自己需要耦合关联的调用类应该知道的少。每个类都尽量减少对其他类的依赖

## 单例模式

用于创建全局可用，全局唯一的对象
实现方案，如下
* 使用自身方法

    ```
    var Singleton = function(name) {
        this.instance = null;
        this.name = name
    }  
    Singleto.prototype.getName = function() {
        return this.name
    }

    Singleton.prototype.getInstance = function(name) {
        if(!this.instance) {
            this.instance = new Singleleton(name)
        }
        return this.instance
    }
    var a = Singleton.getInstance('chen')
    var b = Singleton.getInstance('zhe')
    console.log(a===b) // true

    这种方法的缺地是一定要使用Singleto.getInstance() 来获取单例，而不能使用new
    ```

* 闭包

    ```
    var Singleto = (function(){
        var instance = null;
        fucntion single(name) {
            if(!instance) {
                instance = this
            }
            return instance
        }
        single.prototype = {}
        return single
    })()

    var a = new Singleton('chen')
    var b = new Singleton('zhe')

    console.log(a === b) // true
    无法像类一样使用， 添加属性，或删除属性

    ```
* 单例代理

    ```
    var Action = function(name) {
        this.name = name
    }

    Action.prototype = function () {}

    var ProxySingletoAction = (function(){
        var install = null
        return function(name) {
            if(!instance) instance = new Action(name);
            return instance
        }
    })

    var a = new ProxySingletonAction('chen')
    var b = new ProxySingletonAction('zhe')
    有点： 将类的实际作用于单例模式的使用分离开来，提高内聚
    缺点： 不够通用，对于行的类又要重复写相同的代码
    ```

* 通用写法

    ```
    var getSingle = (function(){
        var single
        return function(fn) {
            return single || (single = fn.apply(this, arguments))
        }
    })()

    var createSingleDiv = getSingle(function() {
        return document.createElement('div')
    })

    ```
总结： 单例模式适用于全局对象，而且是能够共享的对象。这里的共享是说能够被修改，获取等。

项目实例： 1： 全局的统计行为，

##策略模式

封装一些列的算法，并且各个可以替换；不只是算法，规则架构也可以作为策略来封装

使用场景： 一个旅游类， 接受各种出行策略类

事件的回调就是使用策略模式

```
    var tween = {
        liner : function(t, b, c, d) { return b + c * t/d},
        easeIN: function(t, b, c, d) { return b + c * (t/d) * (t/d)},
        easeOut: function(t, b, c, d) {return b + c * ((t/d -1) * (t/d -1) +1)}
    }

    var Animation = function(el) {
        this.el = el;
        this.startTime = 0;
        this.start = 0;
        this.end = 0;
        this.props = '';
        this.easing = null;
        this.duration = 0;
    }

    Animation.prototype.run = function(props, end, duration, easeing) {
        this.startTime = Date.now()
    }

    Animation.prototype.step = function() {
        var elapse = Date.now() - this.startTime
    }

    Animation.prototype.update = function(pos) {
        this.el.style[this.prop] = pos + 'px'
    }

    var div = document.getElementById('div')
    var anim = new Animation(div);
    anim.start('right', 500, 1000, 'easeOUt')
    
```

## 代理模式

    通过引入一个新的层， 在不改动原有类的情况下为原有类新增功能，解耦。

使用场景： 虚拟代理， 汇总任务， http请求，缓存代理，缓存结果

结果： 提高类的内聚性， 新功能放在类的外面，以后不需要时，也不用修改原类

注意： 代理类与原类的API要保持一致， 外部调用不能也不应该知道调用的是代理类，还是原类

```
var cacheFn = function(fn) {
    var cache = {}

    return function() {
        var args = []
        for(var i = 0 l = arguments.length; i< l; i++) {
            args.push(arguments[i])
        }

        if(cache[args.toString()]) return cache[args.toString()]
        return cache[args.toString()]  = fn.apply(this.args)
    }
}

function fotorial(num) {
    if(num === 0) return 1
    var result = num
    for(var i = 1, l = num; i<l; i++) {
        result = result * i
    }

    return result;
}

var cachedFactorial = cacheFn(fotorial)
```
##订阅发布模式

    好处，被动接受。就是提前订阅了一个事件，等到这个函数改变，或者什么条件下。该事件能够主动触发。
![vue](https://img2018.cnblogs.com/blog/1292376/201903/1292376-20190326215129176-236538799.png)

``` 
vue 订阅发布模式

function defineReactive(data, key, val) {
    observe(val); //遍历所有子类
    Object.defineProperty(data, key, {
        enumerable: true,
        configurable: true,
        get: function() {
            return val;
        };
        set: function(newVal) {
            val = newVal
            console.log('属性' + key + '已经被监听了，现在值为：“' + newVal.toString() + '”');
        }
    })
}

function observe(data) {
    if(!dara || typeof data !== 'object') {
        return
    }

    Object.keys(data).forEach(function(key) => {
        defineReactive(data, key, data[key]);
    })
};

var library = {
    bookl: {
        name: '
    },
    book2: ''
}

observe(library);
library.book.name = 'vue';
library.hook2 = '没有此书'
```
[vue + 数据发布 + 订阅发布者](https://www.cnblogs.com/ceceliahappycoding/p/10604209.html)

##命令模式
```
分离任务的请求者和执行者

var setCommand = function(button, command) {
    button.onclick = function() {
        conmmand.execute();
    }
}

var SomeCommand = function(receiver) {
    this.receiver = receiver
}

SomeCOmmand.prototype.undo = function() {
    this.receiver.undoSomething
}

receiver = { doSomeTIng: function() {}, undoSomething: function() {}}

var command = new SomeCommand(receiver)

setCommand(button, command)
```

## 模板模式
    父类规定一系列的抽象方法， 子类继承并实现，从而分离了不变（按顺序执行这些方法）与变化
    （这些方法的具体实现）, 生命周期函数

```
var liFeCircle = function(config) {
    var onCreate = config.onCreate || null
    var onUpdata = config.onUpdate || null
    var onDestroy = config.onDestroy || null

    return {
        init: function(options) {
            Object.merge(this, options)
        }
    }
}
```

## 中介模式
    解除各个对象相互之间的联系，仅仅与唯一一个中介者联系， 从而实现解耦，符合最少知识原则。
    但同时， 也会创建一个大包揽的中介者， 难以维护， 与不符合单一职责原则。因此使用中介模式需要慎重

## 享元模式

    通过划分内部状态，与外部状态来减少需要创建对象的数量， 共享对象。与其他模式不一样的是，享元模式针对的是性能问题
* 内部状态通常是可以被共享的属性， 例如性别， 岁数， 等级之类可以划分出少数几个类别的属性
* 内部状态就是各不相同的属性， 例如大小， 面积之类有很多取值的属性
* 其实内外状态的区分的关键就是该属性的取值那个最少， 选最少那个作为内部， 其他外部。这样就是的共享的对象变得最少

```
var Modal = function(sex) {
    this.sex = sex;
}

Modal.prototype.takePhoto = function(index) {
    console.log('sex=' + this.sex + '; cloth=' + this.clothes[index])
}

Modal.prototype.wear = function(clothes) { this.clothes = clothes}

var maleModal = new Modal('male')
maleModal.wear = (['red', 'green', 'blue'])
var femaleModal = new Modal('female')
maleModal.wear(['dress', 'skirt', 'pants'])

for(var i = 0, l < 3; i< l ; i++) {
    modalModal.takePhoto(i)
    femalModal.takePhoto(i)
}
```
对象池技术

```
var objectPoolFactory  = function(createFn) {
    var pool = []
    return {
        create: function() {
            return obj = pool.length === 0 
                ? createFn.apply(this, arguments) 
        },
        recover: function(obj) {
            pool.push(obj)
        }
    }
}

var iframeFactory = objectPoolFactory(function() {
    var iframe = docuemnt.createElement('iframe')
    document.body.appendchild(iframe)

    iframe.onload = function() {
        iframe.onload = null
        iframeFactory.recover(iframe)
    }
    return iframe
})

var iframe1 = iframeFactory.create()

iframe1.src = 'http://www.baidu.com';
setTimeout(function() {
    var iframe1 = iframeFactory.create();
    iframe2.src = 'http://www.qq.com'
}, 5000)
```
## 状态模式
    使用状态机时， 对每一个状态都独立封装成类（对象）， 各个状态类复写同一个抽象方法， 
    使用该方法来执行状态机

不变： 状态总要变化；状态的变换都是通过同一个方法， 状态有各自的行为
变化： 状态类的细节

结果： 消除switch语句， 不改变原有代码的情况，新增状态

```
var Light = function() {
    this.currState = FSM.off;
    this.button = null;
}

Light.prototype.init = function() {
    this.button = document.createElement('button')
    this.button.innerHTML = 'off';
    document.body.append(this.button);
    that = this
    this.button.onclick = function() {
        that.currState.toggle.call(that)
    }
}

var FSM = {
    off: {
        toggle: function() {
            console.log('turn off light')
            this.currState = FSM.on
        }
    },
    on: {
        toggle: function() {
            console.log('turn on light')
            this.currState = FSM.off
        }
    }
}

var light = new Light();
light.init()
```

## 职责链模式
    解除任务请求者与任务执行者之间的复杂关系，任务从任意请求者开始逐级传递， 直至叨叨最终执行者或者不再传递，

不变： 有一系列任务要执行， 并且需要逐级更新<br/>
变化： 具体任务细节， 任务的执行顺序

```
var Chain = function() {
    this.fn = fn;
    this.successor = null
}

Chain.prototype.setNext = function(successor) {
    this.successor = successor
    return chain
}

Chain.prototype.next = function() {
    this.successor 
    && this.successor.start.apply(this.successor, arguments)
}

Chain.prototype.start = function() {
    this.fn.apply(this, arguments);
}

var first = new Chain(function() {
    console.log(1)
    this.next()
})

var second = new Chain(function(){
    console.log(2)
    var that = this
    var pause = 0
    setTimeout(function() {
        this.next()
    }, 1000)
})

var third = new Chain(function() {
    console.log(2)
})

first.setNext(second).setNext(third)
first.start()
```

## 装饰器模式

    不修改原函数的前提下， 在函数执行的前后增加新功能，适用于只针对于特定场景的功能
    使用装饰器， 原函数可以保存高内聚

不变： 原函数
变： 新增功能

结果：原函数保持高内聚，可以各处通用；对于特殊的功能通过装饰器来增加，不影响原函数。与职责链相似，同样可以实现AOP

```
var before = function(originFn, beforeFn) {
    return function() {
        beforeFn.apply(this, arguments);
        return originFn.apply(this, arguments);
    }
}

var after = function(originFn, afterFn) {
    return function() {
        var ret = originFn.apply(this, arguments);
        after.apply(this, arguments);
        return ret
    }
}

var obj = {msg: 'hello', say: function() {console.log(this.msg)}}
obj.say = before(obj.say, function() {console.log('before')})
obj.say = after(obj.say, function() {console.log('after')})
```
总结： 设计模式，让我们分离变化的， 保持不变的。
