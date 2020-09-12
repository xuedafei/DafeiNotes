# typescript学习笔记

## 强类型与弱类型

> 在强类型语言中，当一个对象从调用函数传递到被调用函数时，其类型必须与被调用函数中生命的类型兼容 --Liskov, Zilles 1974

强类型语言： 不允许改变变量的数据类型，除非进行强制转换

弱类型语言： 在弱类型语言中变量可以被赋予不同的数据类型

## 动态类型语言与静态类型语言

静态类型语言： 在编译阶段确定所有变量类型
动态类型语言： 在执行阶段确定所有变量类型

## ts基本类型

ES6的数据类型： Boolean, Number, String, Array, Object, Function, Symbol, Null, undefined
TypeScript的数据类型：除了包含ES6的类型外, 还有void, any, never, 元组, 枚举, 高级类型

```typescript
// 原始类型
let bool: boolean = true;
let num: number = 123;
let str: string = 'abc';

// 数组
let arr1: number[] = [1, 2, 3];
let arr2: Array<number> = [1, 2];
//联合类型数组
let arr3: Array<number | string> = [1, '1']; 

// 元组
let tuple: [number, string] = [1, '1'];
// 元组越界问题，给元组push一个该元组没定义的类型 tuple.push(2), 可以添加，但是无法访问，开发过程中强烈不建议这样操作


// 函数
let add = (a: number, b: number) => a + b;
let compute: (a: number, b: number) => number;
compute = (a, b) => a + b;

// 对象
let obj: object = {x: 1};
let obj2: { a: number, b: string} = {a:1, b: '2'};

// symbol
let s1: symbol = Symbol();
let s2 = Symbol();

// undefined null 是任意类型的子类型，所有类型都可以赋值undefined null，前提是tsconfig中的strictNullChecks设置为false
let un: undefined = undefined;
let nu: null = null;

// void
let noReturn = () => {};

// any 不指定类型 默认为any 和js中没有区别
let x;
x = 1;
x = '1';

// never 表示 永远不会有返回值的类型 1.抛出异常的函数 2. 死循环函数永远不会返回
let error = () => {
  throw new Error('error')
}

let endless = () => {
  while(true) {}
}

interface Lib {
  (): void; // 表示 Lib 是个函数
  version: string; // 属性
  doSomething(): void; // 方法
}

const myLib: Lib = () => {}
myLib.version = '1.0';
myLib.doSomething = () => {};
```

## 类型注解

作用：相当于强类型语言中的类型声明
语法：(变量/函数): type

```typescript
const abc: string = 'abc';
```

## 枚举类型

枚举就是一组有名字的常量集合，分为数字枚举、字符串枚举、异构枚举、常量枚举
枚举成员的值是只读类型不能修改
数字枚举的实现原理是反向映射

枚举成员分类

- const member 编译阶段就确定了值
  - 无初始值
  - 常量表达式
  - 对已有枚举成员的引用
- computed member 需要被计算的枚举成员，不会在编译阶段被计算，会保留到执行阶段计算。

枚举类型：某些情况下，枚举和枚举成员可以作为单独的类型存在

```typescript
//数字枚举, 数字和值都可以取到值，原理反响映射
enum Role {
  Reporter,
  Developer,
  Owner,
}
// 字符串枚举，字符串枚举没有反响映射，只有成员的名称被作为了key
enum Message {
  Success = '成功',
  Fail = '失败',
}
// 异构枚举，数字枚举和字符串枚举的混用，不建议使用
enum Answer {
  N,
  Y = 'yes',
}

// 反响映射
var a = {};
a[a.b = 1] = 'b';

// 枚举成员分类
enmu Char {
  // const member
  a,
  b = Char.a,
  c = 1 + 2,
  // computed member
  d = Math.random(),
  e = '123'.length,
}

// 常量枚举 会在编译后被移除，作用：不需要对象，而需要他的值的时候就可以使用常量枚举，可以减少编译后的代码
const enum Month {
  Jan,
  Feb,
  Mar
}

```

## 接口

接口可以用来约束对象,函数以及类的结构和类型

```typescript

//对象类型的接口
interface List {
  readonly id: number; // 只读
  name: string;
  sex?: string; // 可选
  [key: string]:  any; // 字符串索引签名
  [index: number]: string; // 数字索引签名
}

// 接口定义函数
interface Add {
  (x: number, y: number): number
}

const add: Add = (a, b) => a + b;

// 混合类型接口, 既可以定义函数 又可以有对象成员
interface Lib {
  (): void; // 表示 Lib 是个函数
  version: string; // 属性
  doSomething(): void; // 方法
}

const myLib: Lib = () => {}
myLib.version = '1.0';
myLib.doSomething = () => {};

```

## 函数

在 js 中对函数的参数个数是没有限制的,在 ts 中形参和实参要一一对应

```typescript
// 可选参数
function add (x: number, y: number, z?:number) {
  if (z) {
    return x + y + z;
  }
  return x + y;
}

// 默认值
function add2(x=1, y: number) {
  return x + y;
}

// 剩余参数
function add3(x: number, ...rest: number[]) {
  return x + rest.reduce((pre + cur) => pre + cur)
}
```

### 函数重载

一般概念：如果函数名称相同但是参数个数或者类型不同,那么就实现了函数重载. 函数重载的好处是需要给功能相似的函数起不同的名称,增强函数可读性。

ts 中的函数重载要求我们先定义一系列名称相同的函数声明, 然后在一个类型最宽泛的版本中实现重载

```typescript
// 函数声明
function add1(...rest: number[]): number;
function add1(...rest: stringp[]): string;
// 函数实现 类型宽泛
function add1(...rest: any[]): any {
  let first = rest[0];
  if(typeof first === 'string') {
    return rest.join('');
  }
  if(typeof first === 'number') {
    return rest.reduce((pre + cur) => pre + cur);
  }
}

```

## 类

ts 的类覆盖了 ES6 的类，同时也引入了其他特性如成员修饰符
类的所有属性默认是 public

```typescript
class Dog {
  constructor(name: string) {
    this.name = name;
  }
  // 属性是实例的属性不是原型的属性，必须有初始值或者在构造函数中初始化
  name: string
  run() {}
}

// 类的继承
class wangcai extends Dog {
  constructor(name: string, color: string) {
    super(name);
    this.color = color;
  }
  color: string;
}

// 成员修饰符
class nianq {
  // 给构造函数加 private 表明这个类既不能被实力化也不能被继承
  // 给构造函数加 protected 表明这个类只能被继承 不能被实例化，基类
  // 构造函数加修饰符，相当于形参变为类属性，省去在类中显示声明
  private constructor(name: string, public sex: string) {
    this.name = name;
    this.sex = sex;
  }
  name: string;
  // 私有成员 只能被类本身调用，不能被实力或者子类调用 
  private color = 'red';
  // 受保护成员，只能在类或者子类中访问，不能在实例中访问
  protected run() {}
  // 只读属性, 不能修改，一定要初始化
  readonly legs = 4;
  // 静态成员， 只能通过类名来调用，不能通过在子类或者实例调用
  static food: string = 'bones';
}

// 抽象类与多态
// 所谓抽象类就是只能被继承 不能被实例化的类
// 所谓多态就是在抽象类中定义一个抽象方法，在多个子类中有不同的实现
abstract class Animal {
  eat() { console.log('eat') }
  // 抽象方法，由子类实现
  abstract sleep(): void;
}

class Cat extends Animal {
  constructor() {
    super();
  }
  sleep() {
    console.log('cat sleet');
  }
}

class Pig extends Animal {
  constructor() {
    super();
  }
  sleep() {
    console.log('Pig sleep');
  }
}
```

## 类与接口的关系

```typescript
// 类类型接口: 一个接口可以约束类成员有哪些属性及方法
// 1. 接口只能约束类的公有成员
// 2. 接口不能约束类的构造函数
// 3. 接口可以继承，一个接口可以继承多个接口
// 4. 接口继承类，接口除了可以继承接口还可以继承类，相当于把类成员都抽象出来（包括公共成员，私有成员，受保护成员），及只有成员接口，没有具体实现
interface Human {
  name: string;
  eat(): void;
}

// 实现 Human 接口 用 implements 关键字
// 类实现接口的时候必须实现接口中定义的所有属性
class Asian implements Human {
  constructor(name: string) {
    this.name = name;
  }
  name: string
  eat() {
    console.log('asian eat')
  }
}

// 接口的继承
interface Man extends Human {
  run(): void;
}

interface Child {
  cry(): void;
}

interface Boy extends Man, Child {}

const boy = {
  name: 'ac',
  eat() {},
  run() {},
  cry() {},
}

// 接口继承类
class Auto {
  state = 1
  private name = 'abc'
  protected alias = 'aaa'
}
// AutoInterface继承了 Auto 这个接口中隐含了 state 属性, 类型是 number
interface AutoInterface extends Auto {}

```

## 泛型

很多时候希望一个函数或者一个类可以支持多种数据类型，有很大的灵活性，这时候就要用到泛型。
泛型：不预先确定的数据类型，具体的类型在使用的时候才确定。
泛型的好处：

1. 函数和类可以轻松的支持多种类型，增强程序的扩展性
2. 不必写很多条函数重载，冗长的联合类型声明，增强代码的可读性
3. 灵活控制类型之间的约束

```typescript
// 一个打印函数, 比较局限
function log(value: string): string {
  console.log(value);
  return value;
}

// 定义泛型函数
function log2<T>(value: T): T {
  return value;
}

// 调用 log2, 显示定义类型
log2<string[]>(['1', '2'])
// 省略类型，利用 ts 自动类型推断
log2(123)

// 定义一个泛型函数类型
type Log = <T>(value: T): T;
const myLog: Log = log2;

// 泛型接口
interface Test {
  <T>(value: T) => T;
}

// 泛型对整个接口其作用, 可以给泛型默认值
interface Test2<T = string> {
  name: T;
}
const b: Test2<number> = {
  name: 1
}

// 泛型类，泛型约束类的成员
// 1. 泛型不能约束类的静态成员
// 2. 泛型约束，泛型继承某种接口，及被约束，下例子中的参数被约束为有 length 属性的类型

interface Length {
  length: number;
}

class Log<T extends Length> {
  log(value: T) {
    console.log(value, value.length)
    return value;
  }
}

let log1 = new Log<number>();
```

## ts 的类型检查机制

Typescript 编译器在做类型检查时，所秉承的一些原则，以及表现的一些行为。
作用：辅助开发， 提高开发效率

- 类型推断
- 类型兼容性
- 类型保护

### 类型推断

不需要指定变量的类型（函数返回值的类型），Typescript 可以根据某些规则自动的推断出一个类型

- 基础类型推断
- 最佳通用类型推断
- 上下文类型推断

```typescript
// 从右向左的推断 基础类型推断 为 number
let a = 1;
let fun = (a = 1) { return a + 1 }
// 从右向左的推断  最佳通用类型推断
let c = [1, null, '2']

// 从左向右的推断 上下文类型推断, 通常发生在事件处理中
window.onkeydown = (event) => {
  // ts 根据事件绑定，推断对应的 event 的类型，即为上下文类型推断
}
```

### 类型的兼容性

当一个类型 x 可以被赋值给类型y 时，y 就兼容 x
枚举和 number 可以相互兼容，枚举之间完全不兼容
两个类兼容，比较结构，静态成员和构造函数不参与比较

- 结构之间比较，成员少的兼容成员多的
- 函数之间比较，参数多的兼容参数少的

```typescript
// 接口兼容性 X 兼容 Y
interface X {
  a: any
}

interface Y {
  a: any;
  y: any
}
```

### 类型保护机制

TypeScript 能够在特定的区块中保证变量数据某种确定的类型。可以在此区块中放心的引用类型的属性，或者调用此类型的方法。

```typescript
// 创建这种特殊区块的方法
// 1. 使用 instanceof，判断是否属于某个类的实例

// 2. 使用 in 关键字，判断某个属性是不是属于某个对象

// 3. typeof，判断基本类型

// 4. 自定义函数判断
```

## 高级类型

- 交叉类型，  用 & 连接
  - 交叉类型是将所有类型合并成一个类型，新的类型具有所有类型的特性
- 联合类型， 用 | 连接
  - 表示声明的类型并不确定，可以是多个类型中的一个
