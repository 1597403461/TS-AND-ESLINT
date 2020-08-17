# TS

## 全局安装ts，即可使用tsc

## 创建ts配置文件 `tsc --init`

## ts基本类型

```js
// 原始类型
let bool: boolean = true;
let num: number = 123;
let str: string = "abc";

// 数组
let arr1: number[] = [1, 2, 3, 43];
let arr2: Array<number> = [1, 2, 3, 4];
let arr3: Array<number | string> = [1, 1, 2, 3, "abc"]; // 联合类型

// 元组（限定了数组元素的类型以及个数）
let tuple: [number, string, boolean] = [1, "1", true];

// 函数
let add = (x: number, y: number): number => x + y;
let add2: (x: number, y: number) => number;

// 对象
let obj: { x: number; y: number } = { x: 1, y: 2 };

// symbol
let s1: symbol = Symbol()

// undefined null
let un: undefined = undefined
let nu: null = null

// 设置strictNullChecks为false即可设置属性为undefined或者null

// 联合类型
let fkx: number | string = '132'

// void（无任何返回值的类型（函数）） 可以让任何表达式返回undefined

// any类型（和js无区别）

// never
```

## 枚举类型

```js

// 数字枚举
enum Role {
    one,
    two,
    three,
}

// 字符串枚举

enum Message {
    success = '成功了',
    Fail = '失败了'
}

```

## 接口

```js
interface Card {
    suit: string;
    card: number;
}
interface deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: deck): () => Card;
}

let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function(this:deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}


let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

console.log("card: " + pickedCard.card + " of " + pickedCard.suit);
```

## 函数

```js
// 函数
let buildName = (x: number, ...rest: number[]): number =>
    x + rest.reduce((pre, cur) => pre + cur);
let sum = buildName(1, 2, 3, 4, 5);
console.log(sum);

// 默认参数
let add6 = (x: number, y = 1, z: number) => x + y + z;
console.log(add6(1, undefined, 3));

// 可选参数
let add7 = (x: number, y = 1, z: number, q?: number) => q && x + y + z + q;
console.log(add7(1, undefined, 3, 4));

// 函数重载
function add8(...rest:number[]):number;
function add8(...rest:string[]):string;
function add8(...rest:any[]):any{
    let first = rest[0]
    if(typeof first === 'string') {
        return rest.join('')
    }
    if(typeof first === 'number') {
        return rest.reduce((pre, cur) => pre + cur);
    }
}
console.log(add8(1,2,3,4,))
console.log(add8('a','b','c'))
```

## 类

```js
// private与protected的区别是protexted可以在继承的类中使用，二者均不可在是实例化外部使用
class Father {
    private color:string = 'red';
    protected sex:string = 'male';
    static height:string = '180cm';// 静态属性不可被实例化使用
    name: string;
    age: number;
    constructor(name: string, age: number) {
        (this.name = name), (this.age = age);
    }
    say(name: string) {
        return this.name + "的名字是" + name + "今年" + this.age + "岁" + this.color;
    }
}
let father = new Father("小狗", 9);

console.log(father.say("汪汪"));

class Son extends Father {
    readonly home:string = '凌源' // 只读，必须赋值
    // food: string;
    // 构造函数的参数也可加入属性，作用是和实例化属性效果一致，可省略实例化属性的定义
    constructor(name: string, age: number, public food: string) {
        super(name, age);
        this.food = food;
    }
    eat(): string {
        return this.name + "吃" + this.food + this.sex;
    }
}

let son = new Son("慧儿", 28, "瓜子");

console.log(son)

```

## 抽象类

```js
// 抽象类（只能被继承不能被实例化的类）

abstract class Animal {
    abstract sleep(): void
}

// let animal = new Animal() 不可被实例化

class Dog extends Animal {
    constructor(public name: string) {
        super()
        this.name = name
    }
    run() { }
    sleep() {
        console.log('dog sleep')
    }
}
let dog = new Dog('doudou')

class Cat extends Animal {
    constructor() {
        super()
    }
    sleep() {
        console.log('cat sleep')
    }
}

let cat = new Cat()

let animals: Animal[] = [dog, cat]

animals.forEach(i => { i.sleep() })
```

## this类型

```js
// this类型

class WorkFlow {
    step1() { return this }
    step2() { return this }
}

class MyFlow extends WorkFlow {
    next() {
        return this
    }
}

// 链式调用
new MyFlow().next().step1().next().step2()
```

## 类类型

```js
// 类类型接口
// 类类型接口只能约束共有成员、也不能约束构造函数
interface Human {
    name: string,
    eat(): void,
}

class Asian implements Human {
    constructor(public name: string) {
        this.name = name
    }
    static age:number = 1
    eat() { }
    sleep() { }
}
```

## 接口的继承

```js
// 接口的继承，一对多继承（接口和类之间可以互相继承）
interface Human {
    name: string,
    eat(): void,
}

interface Man extends Human {
    run(): void
}

interface Child {
    cry():void
}

interface Boy extends Man,Child {

}

let boy:Boy = {
    name:'',
    eat() { },
    run() { },
    cry() { },
}
```

## 泛型

```js
// 泛型
// 不预先定义的数据类型，具体的类型在使用的时候才能确定
// 函数和类可以轻松支持多种类型，增强程序的拓展性
// 不必写多条函数重载，冗长的联合类型生命，增强代码的可读性
// 灵活控制类型之间的约束

function log<T>(value: T): T {
    console.log(value);
    return value;
}
const log: <T>(value:T) => T =(value) => value

// 泛型函数类型

type log = <T>(value: T) => T;

// 泛型接口

interface Log<T = string> {
    (value: T): T;
}

```

## 泛型类、泛型约束

```js
class Log<T> {
    run(value:T):T{
        console.log(value)
        return value
    }
}

let log = new Log<number>()

log.run(1)

let log2 = new Log<string>()

log2.run('1')

let log3 = new Log()

log3.run([]) // 不给定泛型参数类型的时候可以传任意类型参数


// 泛型约束
interface Length {
    length: number
}
function log4<T extends Length>(value: T): T {
    console.log(value,value.length);
    return value;
}
// log4(123) 数值类型无length属性，报错
log4('123')
log4([])
log4({length:4})
```

## 类型兼容性

```js

// 原类型必须具备目标类型的所有属性，成员少的兼容成员多的

/*
 * X（目标类型） = Y（源类型），X 兼容 Y
 */

let s: string = 'a'
// str = null

// 接口兼容性
interface X {
    a: any;
    b: any;
}
interface Y {
    a: any;
    b: any;
    c: any;
}
let x: X = {a: 1, b: 2}
let y: Y = {a: 1, b: 2, c: 3}
x = y
// y = x

// 函数兼容性
type Handler = (a: number, b: number) => void
function hof(handler: Handler) {
    return handler
}

// 1)参数个数
let handler1 = (a: number) => {}
hof(handler1)
let handler2 = (a: number, b: number, c: number) => {}
// hof(handler2)

// 可选参数和剩余参数
let a = (p1: number, p2: number) => {}
let b = (p1?: number, p2?: number) => {}
let c = (...args: number[]) => {}
a = b
a = c
// b = a
// b = c
c = a
c = b

// 2)参数类型
let handler3 = (a: string) => {}
// hof(handler3)

interface Point3D {
    x: number;
    y: number;
    z: number;
}
interface Point2D {
    x: number;
    y: number;
}
let p3d = (point: Point3D) => {}
let p2d = (point: Point2D) => {}
p3d = p2d
// p2d = p23

// 3) 返回值类型
let f = () => ({name: 'Alice'})
let g = () => ({name: 'Alice', location: 'Beijing'})
f = g
// g = f

// 函数重载
function overload(a: number, b: number): number
function overload(a: string, b: string): string
function overload(a: any, b: any): any {}
// function overload(a: any): any {}
// function overload(a: any, b: any, c: any): any {}
// function overload(a: any, b: any) {}

// 枚举兼容性
enum Fruit { Apple, Banana }
enum Color { Red, Yellow }
let fruit: Fruit.Apple = 1
let no: number = Fruit.Apple
// let color: Color.Red = Fruit.Apple

// 类兼容性
class A {
    constructor(p: number, q: number) {}
    id: number = 1
    private name: string = ''
}
class B {
    static s = 1
    constructor(p: number) {}
    id: number = 2
    private name: string = ''
}
class C extends A {}
let aa = new A(1, 2)
let bb = new B(1)
// aa = bb
// bb = aa
let cc = new C(1, 2)
aa = cc
cc = aa

// 泛型兼容性
interface Empty<T> {
    // value: T
}
let obj1: Empty<number> = {};
let obj2: Empty<string> = {};
obj1 = obj2

let log1 = <T>(x: T): T => {
    console.log('x')
    return x
}
let log2 = <U>(y: U): U => {
    console.log('y')
    return y
}
log1 = log2


```

## 交叉类型和联合类型

```js
interface DogInterface {
    run(): void
}
interface CatInterface {
    jump(): void
}
// 交叉类型
let pet: DogInterface & CatInterface = {
    run() {},
    jump() {}
}
// 联合类型
let a: number | string = 1
let b: 'a' | 'b' | 'c'
let c: 1 | 2 | 3

class Dog implements DogInterface {
    run() {}
    eat() {}
}
class Cat  implements CatInterface {
    jump() {}
    eat() {}
}
enum Master { Boy, Girl }
function getPet(master: Master) {
    let pet = master === Master.Boy ? new Dog() : new Cat();
    // pet.run()
    // pet.jump()
    pet.eat()
    return pet
}

interface Square {
    kind: "square";
    size: number;
}
interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}
interface Circle {
    kind: "circle";
    radius: number;
}
type Shape = Square | Rectangle | Circle
function area(s: Shape) {
    switch (s.kind) {
        case "square":
            return s.size * s.size;
        case "rectangle":
            return s.height * s.width;
        case 'circle':
            return Math.PI * s.radius ** 2
        default:
            return ((e: never) => {throw new Error(e)})(s)
    }
}
console.log(area({kind: 'circle', radius: 1}))
```

## 索引类型

```js
interface P {
    name: string;
    age: number;
}
const people: P = {
    age: 20,
    name: 'wang',
};
const fn: <P, T extends keyof P>(p: P, t: T[]) => P[T][] = (p, t) => {
    return t.map(item => p[item])
};

console.log(fn(people,['age']))
```
