---
title: es6 全面教程
tags:
  - es6
categories: 前端
abbrlink: 8307
date: 2019-04-28 14:01:54
---
### 1. 三大基本特征
>封装, 继承, 多态
### ES5中对象的创建
1. 字面量
```js
var Student = {
  name: 'zhudk',
  age: 22,
  subject: '前端开发'
};
```
2. 构造函数
``` js
function Student (name, age, subject) {
  this.name = name;
  this.age = age; 
  this.subject = subject;
}
Student.prototype.study = function() {
  console.log('我在学习' + this.subject);
}
let student1 = new Student('阿坤', 22, '前端开发');
let student2 = new Student('阿傻', 22, '大数据开发');

student1.study(); //我在学习前端开发
student2.study(); //我在学习大数据开发

console.log(student1.study === student2.study); //true
```
<!-- more -->

### ES5中对象的继承
1. prototype的原型继承
```js
function Pupil(school) {
  this.school = school;
}
Pupil.prototype = new Student('zhudk', 8, '小学义务教育课程');
Pupil.prototype.constructor = Pupil;

let pupil1 = new Pupil('北大附小');
Pupil.prototype = new Student('zhudk', 8, '小学义务教育课程');
```
2. 使用call和apply方法实现继承
##### 使用call实现继承
```js
//父类构造函数
function Student (name, age, subject) {
  this.name = name;
  this.age = age; 
  this.subject = subject;
}

//子类构造函数
//父类构造函数
function Student (name, age, subject) {
  this.name = name;
  this.age = age; 
  this.subject = subject;
}
//原型上挂载study方法
Student.prototype.study = function() {
  console.log('我在学习' + this.subject);
}

//子类构造函数
function Pupil(name, age, subject, school) {
  //使用call实现继承
  Student.call(this, name, age, subject);
  this.school = school;
}

let pupil2 = new Pupil('zhudk', 8, '小学义务教育课程', '北大附小');

//报错
pupil2.study(); //Uncaught TypeError: pupil2.study is not a function
```
##### 使用apply实现继承
```js
//父类构造函数
function Student (name, age, subject) {
  this.name = name;
  this.age = age; 
  this.subject = subject;
}

//子类构造函数
function Pupil(name, age, subject, school) {
  //使用applay实现继承
  Student.apply(this, [name, age, subject]);
  this.school = school;
}

//实例化Pupil
let pupil2 = new Pupil('zhudk', 8, '小学义务教育课程', '北大附小');
```
### ES6中对象的创建
```js
//定义类
class Student {
  //构造方法
  constructor(name, age, subject) {
    this.name = name;
    this.age = age;
    this.subject = subject;
  }

  //类中的方法
  study(){
    console.log('我在学习' + this.subject);
  }
}

//实例化类
let student3 = new Student('zhudk', 24, '前端开发');
student3.study(); //我在学习前端开发
```
### ES6中对象的继承
```js
//子类
class Pupil extends Student{
  constructor(name, age, subject, school) {
    //调用父类的constructor
    super(name, age, subject); 
    this.school = school;
  }
}

let pupil = new Pupil('zhudk', 8, '小学义务教育课程', '北大附小');
pupil.study(); //我在学习小学义务教育课程
```

