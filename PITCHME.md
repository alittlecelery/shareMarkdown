
### 介绍
TypeScript是由微软开发，2012年10月份发布第一个版本，代码开源，托管在[GitHub](https://github.com/Microsoft/TypeScript)

---
    
### 特点
1. **es6支持**：TypeScript是JavaScript的扩展，它沿用了es6的一些新特性。
2. **类型检查**：TypeScript提供了静态语言强类型支持，增加了静态编译时的类型检查（也可以忽略），兼容动态语言弱类型的语法，因此TypeScript和JavaScript可以共存，最终被编译成纯粹的javascript。另外有了类型定义后，配合支持TypeScript的编辑器，写代码是一种很好的体验，函数的定义会自动提示出来，错误也会自动提示。

---
#### 我将从类型系统出发，介绍TypeScript的类型定义系统,下面开始学习TypeScript
---

### 第一部分：类型系统
---
 #### 原始类型

   `number，string， boolean，null，undefined，symbol(ES6)`
  
   - ES6支持：字符串类型支持ES6的模板字符串
   - 特殊类型：undefined 和 null 是所有类型的子类型
---
 #### any类型
   - any类型：如果变量声明但未赋值时未指定类型，默认为any类型，变量被定义成any类型后，可以访问任意属性和方法，不会被类型检查
   - 延伸：
     - 类型推断：如果变量声明的时候有赋值，则会根据值的类型推断出一个具体的类型
     - 联合类型：多种类型之间可以组合  
     
---

```
let num: number = 8; //指出明确类型 

let anything: any = 8; //等价于 let anything
    anything = 'I want to be string'; //ok
    anything.getName(); //编译ok

let something = 'you define me'; //等价于 something: string
    something = 8; //类型推断为string,赋值number时，编译报错

let numOrStr: number | string; //联合类型
    numOrStr = 7; 
    numOrStr = 'I can be string too';        
```  

---
#### 对象的类型
<p>TypeScript中类型检查关注值的形状，**接口（interface）**作为一种规范，可用来定义形状和约束，描述对象的类型</p>
  - 普通属性：属性描述支持可选属性（？）和只读属性（readonly）
  - 字符串索引属性：当为接口添加任意属性时，此时接口的确定属性和可选属性的类型必须是任意属性类型的子属性
  - 数字索引属性：可以描述一个数组对象，具体见 *4.数组的类型*
  - 函数类型属性：除了描述普通的属性，还可以描述函数类型,具体见 *5.函数的类型*     
      
 ---
  - 延伸：接口的其他特性
      - 泛型接口：为接口传入一个或多个类型变量来提高接口的通用性，这样的接口称为**泛型接口**,使用的时候需要定义泛型的类型
      - 接口继承接口：接口可以继承接口，相互扩展
      - 接口继承类：不同于Java等语言中接口的作用，TypeScript里接口可以继承类，当接口继承一个类类型时，它会继承类的所有成员但不包括其实现，当接口继承了一个具有private或protected的类时，这个接口就只能被这个类或它的子类去实现，原理见 *7.类类型*          

---  

    ```
    interface Person {  //首字母大写
        name: string;
        age？: number; 
        [propNamr: string]: string
    }
    
    let individual: Person = {
        name: 'Alice',
        age: 20,  
        gender:'female'
    }     //age的类型number不是string的子类，编译无法通过
    
    //接口约束数组
    interface stringArray {
      readonly [index: number]: string;
    }
   
    ```
---

   ```
    // 泛型接口
    interface Person<T> {
        [propName: string]: T
    }
    let individual: Person<string> = {
        name:'Tom'
    }
    let individual2: Person<number> = {
        age:20
    } 
  ```
---

#### 数组的类型
 合并相同类型的对象叫数组，合并不同类型的对象叫元祖(Tuple),数组用`类型[]`或者 `Array<类型>`定义，**接口**也可以用来定义数组或元祖
 
---  

    ```
    let array1: number[] = [1,2,3];
    let array2: Array<number> = [1,2,3];
    let tuple = [string,number]
    
    interface NumberArray {
      [index: number]: number;
    }
    
    interface NumberArray {
      0: number;
      1: string;
    }
    ``` 
--- 

#### 函数的类型
对函数的输入和输出定义类型
 - 如果函数没有输出时，使用关键字void
 - 如果永远没有返回，使用关键字never，如`function error(message:String): never { throw new Error(message) }`
 - 支持定义**泛型函数**    
---    

一等公民函数：当一个变量为函数类型，用（输入）=> (输出)表示函数类型定义，可以在**接口**中定义函数类型，描述函数的形状

---
函数参数
 - 可选参数：当定义参数和传入参数不一致时，需明确指出可选参数，并且可选参数必须放在必须参数的后面
 - 默认参数：可以为参数指定初始值（ES6）
 - 剩余参数：同ES6，用...rest获取剩余参数
     
---
函数重载
  - 函数名相同，参数个数或类型不一样，和返回值类型无关。
  - 实现同一个功能的函数可以取相同的名字，还可以清晰的告诉调用者传入什么参数得到什么结果
---    

    ```
    function add(x:number,y:number): number{
       return x + y 
    }
    //将函数赋值给一个变量，此时为变量定义函数类型
    let myAdd: (x:number,y:number) => number = add
    //用接口定义函数的类型
    interface AddFunc {
       (x:number,y:number): number 
    }
    let myAdd: AddFunc = add;
    ```
---    

#### 枚举类型（Enum）
<p>枚举类型是一种名称数值集，默认情况下，枚举成员索引从0开始递增，它也支持手动赋值，枚举索引和枚举值具有双向映射关系</p>
   
--- 
    ```
    enum Directions {Up,Down,Left,Right};
    let d: Directions = Directions.Up;
    console.log(Directions.Up); //0
    console.log(Directions[0]) //Up
    ```
---    

#### 类类型
 <p>TypeScript除了实现ES6中类的所有功能之外，还添加了新用法,给类加上类类型类似于接口</p>
    - 增加了public,private,protected，readonly关键字修饰的访问权限，成员都默认为 public，当成员被标记成private时，它就不能在声明它的类的外部访问，protected修饰符与private修饰符的行为很相似，但有一点不同，protected成员在派生类中仍然可以访问。readonly关键字将属性设置为只读的，只读属性必须在声明时或构造函数里被初始化。
    - 提供了抽象类（abstract）
    - 类可以实现接口（implements），使一个类去符合某种契约，接口用来描述类的公共部分，
    - 类似泛型接口，也可以定义泛型类  
    
---   

#### type创建别名
 <p>type可以为上述类型创建别名，为对象创建别名的作用类似接口,但不具备接口可以继承和实现的特点，所以不要用type代替接口</p>
    - 原始类型 `type a = string`
    - 函数类型 `type b = () => string`
    - 联合类型 `type Alias = a | b;`
    - 字符字面量类型 `type Easing = "ease-in" | "ease-out" | "ease-in-out"; `
    - 泛型 `type Container<T> = { value: T };`
    
--- 

#### 类型断言 & 类型约束
  <p>上述是TypeScript的类型定义，对于any，联合类型，或泛型这些不确切的类型，我们不可以安全的操作它的属性或方法，为此TypeScript提供了**类型断言**和**类型约束**规范，对定义了any类型或联合类型的变量进行类型断言，告诉编译器确切的类型，然后才能判断某个属性是否存在，例如，想要获取length属性，需要断言为string类型，其语法为`<类型>值`或者`值 as 类型`，在JSX中使用as语法，对于泛型，我们可以对其进行泛型约束，通过继承的方式，泛型继承接口使之符合接口的形状，多个类型参数之间也可以实现继承，来实现参数之间的约束</p>
    
---

```
//类型断言
function getLength(something: String | number): number {
    if((something as string).length) {
        return (something as string).length
    } else {
    return something.toString().length
    }
}
//类型约束
interface Length {
    length: number
}      
//约束泛型T必须包含length属性
function getLength<T extends Length>(something:T): number {
  return something.length
}
getLength(123);  //123没有length属性，编译报错   
```
---
#### 声明文件
   TypeScript的关键之一是type，我们定义类型，当我们要使用第三方库时，就需要使用它的声明文件，声明文件以`.d.ts`为后缀，用`declare`关键字定义，为了便于管理，所有的类型被发布到npm @types scope下，使用npm安装对应的声明模块即可，例如`npm install @types/react`

---
#### 内置对象
   内置对象如`Error`,`Date`,`Document`,`NodeList`等可以直接作为TypeScript的类型定义使用，它们在[TypeScript核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib)中定义好了

---
### 第二部分：在react项目中使用TypeScript
---

1. npm全局安装typescript包，并在工程根目录运行tsc --init，自动产生tsconfig.json文件。初始化得到的tsconfig.json，增加"allowJs": true选项。
```
npm install -g typescript
tsc --init
```
2. 增加react声明文件
```
npm install --save-dev @types/react @types/react-dom
```
3. 配置webpack，增加ts的loader，如awesome-typescript-loader。
```
npm install --save-dev awesome-typescript-loader
```
4. 新项目可以参考TypeScript-React-Starter
---
### THANK YOU



