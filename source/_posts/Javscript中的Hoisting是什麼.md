---
title: Javscript中的Hoisting是什麼?
date: 2024-06-30 20:16:21
tags:
---
## 前言

今天的主題是Hoisting，內容為參考各路高手前輩的文章而來，因此只能算是整理而非原創。

來源會在文章最下方，如有錯誤請不吝來信指教，感謝。

## Hoisting是什麼？

hoisting是指Javascript在實際執行code之前，先將變數跟函數的宣告分配到記憶體中的現象，看起來「像是」宣告的部分被移動到了最上方，但實際上沒有任何程式碼被移動。以下來看例子：

如果今天我們想對一個尚未被宣告過的變數取值，會像以下這樣：

```jsx
 console.log(x);
 // ReferenceError: x is not defined
```

如果我們改成在宣告前取值：

```jsx
console.log(x);
var x = 5;
// undefined
```

這時候卻會變成undefined而不是 x is not defined，這種現象就被稱為Hoisting，變數的宣告部分被提升到了最上面，賦值的部分卻沒有，因此導致了結果為undefined。我們可以將上面的程式碼想像成以下形式：

```jsx
var x; // 宣告的部分被提升到最上方
console.log(x); // 取值的時間點中，x已經被宣告，但還未被賦值為5，因此為undefined
x = 5;
```

### 如果是Function的情況呢？

```jsx
const testFunc = (y) => {
	console.log(y);
	var y = 3;
}
testFunc(5);
// 5
```

為什麼以上結果會是5而不是undefined?，我們可以將以上的程式碼看成這樣：

```jsx
const testFunc = (y) => {
	var y = 5; // 傳進5
	console.log(y);
	var y = 3;
}
testFunc(5);
```

```jsx
const testFunc = (y) => {
	var y ; // 宣告的部分被提升
	var y ; // 宣告的部分被提升
	y = 5; 
	console.log(y); // 此時y為5，因此結果為5
	var y = 3;
}
testFunc(5);
```

### function的宣告也會被提升

```jsx
console.log(z);
var z;
function z(){};
// [Function: z]
```

function的宣告同樣也會發生hoisting，並且優先順序比變數的還要更高。

## let, const也有hoisting嗎？

現在多半比較少在使用var來宣告變數，而是使用let或const，那麼這兩個也會發生hoisting嗎？

let跟const的行為類似，因此只用let舉例。

```jsx
console.log(x);
let x = 5;

// ReferenceError: Cannot access 'x' before initialization
```

以上的例子來看let跟const似乎沒有hoisting，但真的是這樣嗎？

```jsx
var a = 10
function test(){
  console.log(a);
  let a;
}
test()

// ReferenceError: Cannot access 'a' before initialization
```

在以上的例子中，如果let沒有發生hoisting，照理來說應該會輸出10。也就是說let跟const其實也有hoisting，只是跟var在行為上不太一樣。以下節錄自w3school：

> Variables defined with `let` and `const` are hoisted to the top of the block, but not *initialized*.
> 
> 
> Meaning: The block of code is aware of the variable, but it cannot be used until it has been declared.
> 
> Using a `let` variable before it is declared will result in a `ReferenceError`.
> 
> The variable is in a "temporal dead zone" from the start of the block until it is declared:
> 

使用let跟const定義的變數會被提升到區塊的最上方，但不會被「初始化」。

意思是：目前的區塊已經知道有這個變數，但其在被宣告之前是不能使用的。

在被宣告之前使用let的變數會造成ReferenceError，這個變數從區塊開始到被宣告之前會暫時處於一個「Temporal dead zone」，簡稱TMZ。

### let跟const的差別

let跟const有一個小小的差異，就是在TMZ期間試圖賦值會造成不一樣的結果：

使用let

```jsx
console.log('Start');
driverName = "Norris";
let driverName;

// Start
// Uncaught ReferenceError: Cannot access 'driverName' before initialization
```

以上的例子中，在第二行出現了ReferenceError，但程式碼依然會執行直到被拋出錯誤的那一行。

使用const

```jsx
console.log('Start');
driverName = "Alonso";
const driverName;

// SyntaxError: Missing initializer in const declaration
```

使用const的情況則不會印出Start。

## 為什麼會有hoisting?

大多數人初學程式語言時，大概都會聽說過“程式碼是逐行執行”這個說法，那麼為什麼會有hoisting這樣的特性存在呢？原因大概有以下幾點：

### 讓function的互相呼叫可以成立

```jsx
function funcA(n){
  if (n>1) {
    funcB(--n)
  }
}
  
function funcB(n) {
  console.log(n, n % 2 ? 'Odd' : 'Even')
  funcA(n)
}
  
funcA(10)
```

如果沒有hoisting，以上程式碼就不可能運作，因為我們無法同時達成funcA在funcB上面，而funcB又在funcA上面。

### 不需先宣告function才能使用

這個特性讓我們可以不需要在大量定義function的程式碼下面嘗試呼叫function，而是在一開始的地方就先呼叫，因此可以讓程式碼整體的可讀性變得更好。

## hoisting的運作方式

在理解hoisting的運作方式之前，我們需要先認識Execution Context(以下稱EC)跟Variable Object(以下稱VO)。

Execution Context 是 JavaScript 引擎在執行程式碼時所建立的一個環境。當 JavaScript 引擎執行一段程式碼時,它會先創建一個全域的 Execution Context,稱為 Global Execution Context。之後每當遇到一個function呼叫,引擎就會為該function創建一個新的 Execution Context。Variable Object 包含了該 Execution Context 中所有的變數和函式聲明。在創建階段,Variable Object 會先被初始化,之後在執行階段會被賦值。因此function所需的大部分東西都會存在Variable Object裡面，創建階段（還沒有開始跑程式碼的時候），會依序做以下三件事：

1. 將參數放到VO裡面並且按照傳進來的值設定數值，若無則設成undefined。
2. 將function宣告放進VO，如有同名則覆蓋。
3. 將variable宣告放進VO，若有同名則忽略。

---

### 創建階段

```jsx
var foo = "bar"
var a = 1
function bar() {
    foo = "inside bar"
    var a = 2
    c = 3
    console.log(c)
    console.log(d)
}
bar()
```

```
Line 1：global scope，宣告一個variable: foo
Line 2：global scope，宣告一個variable: a
Line 3：global scope，宣告一個function: bar
Line 4：沒有任何variable宣告，不做事
Line 5：bar scope，宣告一個variable: a
Line 6：沒有任何variable宣告，不做事
Line 7：沒有任何variable宣告，不做事
Line 8：沒有任何variable宣告，不做事
```

此時的VO:

```
globalScope: {
  foo: undefined,
  a: undefined,
  bar: function
}
  
barScope: {
  a: undefined
}
```

### 執行階段

在進入執行階段前，還有一個概念需要先理解，先看一下以下範例：

```jsx
var a = 10;
console.log(a)
```

以上兩行程式碼的差異在於：第一行中我們只在意a的記憶體位置，而不太在意值是多少。我們稱呼其為 LHS（Left hand side）引用。

第二行則是相反：我們只在意值是多少，稱呼其為 RHS（Right hand side）引用。

LHS：請幫我去查這個變數的位置在哪裡，因為我要對它賦值。

RHS：請幫我查詢這個變數的值是什麼，因為我要用這個值。

有了這個概念以後，再看一次上面的範例程式碼，就可以一步一步來解釋：

```jsx
var foo = "bar";
var a = 1;
function bar() {
    foo = "inside bar";
    var a = 2;
    c = 3;
    console.log(c);
    console.log(d);
}
bar();
```

### Line 1：var foo = "bar"

JS 引擎：global scope，我這裡有個對 foo 的 LHS 引用，你有看過它嗎？

執行結果：scope 說有，所以成功找到 foo 並且賦值

這時候的 global scope：

```
{
  foo: "bar",
  a: undefined,
  bar: function
}
```

### Line 2：var a = 1

JS 引擎：global scope，我這裡有個對 a 的 LHS 引用，你有看過它嗎？

執行結果：scope 說有，所以成功找到 a 並且賦值

這時候的 global scope：

```
{
  foo: "bar",
  a: 1,
  bar: function
}
```

### Line 10：bar()

JS 引擎：global scope，我這裡有個對 bar 的 RHS 引用，你有看過它嗎？

執行結果：scope 說有，所以成功返回 bar 的值並且呼叫 function

### Line 4：foo = "inside bar"

JS 引擎：bar scope，我這裡有個對 foo 的 LHS 引用，你有看過它嗎？

執行結果：bar scope 說沒有，所以去問上一層的 global scope

JS 引擎：global scope，我這裡有個對 foo 的 LHS 引用，你有看過它嗎？

執行結果：有，所以成功找到 foo 並且賦值

這時候的 global scope：

```
{
  foo: "inside bar",
  a: 1,
  bar: function
}
```

### Line 5：var a = 2

JS 引擎：bar scope，我這裡有個對 a 的 LHS 引用，你有看過它嗎？

執行結果：bar scope 說有，所以成功找到 a 並且賦值

此時的 bar scope：

```
{
  a: 2
}
```

### Line 6：c = 3

JS 引擎：bar scope，我這裡有個對 c 的 LHS 引用，你有看過它嗎？

執行結果：bar scope 說沒有，所以去問上一層的 global scope

JS 引擎：global scope，我這裡有個對 c 的 LHS 引用，你有看過它嗎？

執行結果：沒有。

這時候有幾種結果，如果你是處在嚴格模式底下（use strict），會返回 `ReferenceError: c is not defined` 錯誤，如果你不是在嚴格模式，那 global scope 就會把 c 加上去並且設定成 3，這邊先假設我們不是在嚴格模式。

此時的 global scope：

```
{
  foo: "inside bar",
  a: 1,
  bar: function,
  c: 3
}
```

### Line 7：console.log(c)

JS 引擎：bar scope，我這裡有個對 c 的 RHS 引用，你有看過它嗎？

執行結果：bar scope 說沒有，所以去問上一層的 global scope

JS 引擎：global scope，我這裡有個對 c 的 RHS 引用，你有看過它嗎？

執行結果：global scope 說有，所以成功返回 c 的值並且呼叫 console.log

### Line 8：console.log(d)

JS 引擎：bar scope，我這裡有個對 d 的 RHS 引用，你有看過它嗎？

執行結果：bar scope 說沒有，所以去問上一層的 global scope

JS 引擎：global scope，我這裡有個對 d 的 RHS 引用，你有看過它嗎？

執行結果：global scope 說沒有，所以返回錯誤 `ReferenceError: d is not defined`

參考資料

[我知道你懂 hoisting，可是你了解到多深？](https://github.com/aszx87410/blog/issues/34)

[MDN Hoisting](https://developer.mozilla.org/zh-TW/docs/Glossary/Hoisting)

延伸閱讀

[W3School Hoisting](https://www.w3schools.com/js/js_hoisting.asp)