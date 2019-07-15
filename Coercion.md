> 你所不知道的JS讀書會，作者meihan，簡報檔
https://docs.google.com/presentation/d/1fX09LiQqZ2e1ldwjIA40Lc6U4dx708o596bnavGXbtw/edit?usp=sharing

>感謝桑莫夏天的學習筆記
https://cythilya.github.io/tags/%E4%BD%A0%E6%89%80%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84JS/

# Coercion 強制轉型
  1. 什麼是強制轉型? 隱含的? 明確的?
  2. 抽象的值運算 (值轉換規則)
  3. 補充 JSON.stringify
  4. 明確的強制轉型（Explicit Coercion）
  5. 隱含的強制轉型（Implicit Coercion）
  6. 寬鬆相等（Loose Equals） vs 嚴格相等（Strict Equals）
  7. 邊緣情況
  8. 安全的使用隱含的強制轉型
  9. 抽象關係式比較
* * *
### 1. 什麼是強制轉型? 隱含的? 明確的?
  JS是動態型別，再執行的時候才會進行轉換，而轉換的動作就叫做強制轉型
  而在JS當中，強制轉型的結果，一定是純量的"基本型別"!
  作者將強制轉型分為兩種，只要是程式碼中刻意寫出的型別轉換的動作，就是明確的
  沒有明確指定要轉型卻轉換的，就叫做隱含的
  ```js
  var a = 42;
  var b = a + ''; //隱含的
  var c = String(a); //明確的
  ```

* * *
### 2. 抽象的值運算(值轉換規則)
  指的是內部限定的運算，根據ES5當中，定義的值轉換規則，也就是JS背後偷偷做的工作
  主要是這四個抽象的值運算:
  * ToString
  * ToNumber
  * ToBoolean
  * ToPrimitives

#1. ToSrting
任何非字串的值被強制轉換成字串，會遵循ES5規格中的 ToString 來運作

* 非物件 => 
  undefined : 'undefined'
  null : 'null'
  boolean : true - 'true' / false - 'false'
  number : 直接轉換成字串，其中數字非常大或非常小都會改以指數呈現(1.07*10的21次方 - '1.07e21') 

* 物件 =>
  若有定義其 toString 方法，則會以它自己的 toString 方法所產生的結果為優先
  ```js
  [1,2,3].toString(); // 1,2,3
  ```
  沒有的話，會呼叫預設方法Object.prototype.toString()，會回傳物件的子分類，[[Class]]
      
    參考:https://es5.github.io/#x9.8

#2. ToNumber

* 非物件 =>
undefined : NaN
null : +0
boolean : true - 1 / false - 0
string : 數字或是NaN

* 物件 =>
目標是轉換成基本型別，有共同的方法valueOf()(找出基本型別值)(沒有此方法就略過)，
再遵照ToString規則再使用ToNumber規則
但是!以 Object.create(null) 建立的 null 沒有 valueOf 或 toString 方法，因此在試圖轉為基本型別值的時候會出錯，丟出 TypeError

> 注意：我們會在本章稍後講解如何強制轉換至 number，但對於下面的代碼段，想像 Number(..) 函數就是 ToString再 ToNumber。

```js
var a = {
  valueOf: function(){
    return "42";
  }
};

var b = {
  toString: function(){
    return "42";
  }
};

var c = [4,2];
c.toString = function(){
  return this.join( "" );	// "42"
};

Number( a );			// 42
Number( b );			// 42
Number( c );			// 42
Number( "" );			// 0
Number( [] );			// 0
Number( [ "abc" ] );	// NaN
Number( {'M': 123} );   // NaN
```

#3. ToBoolean
要知道在JS當中，1 !== true、0 !== false，我們在寫code的時候，(==)可以轉換是因為背後有強制轉型的發生。

> falsy值列表:
  * undefined
  * null
  * false
  * +0,  -0, and NaN
  * ""

> falsy物件
  當使用包裹器物件來建立字串、數字或布林值時，由於包了一層物件，因此就算其底層的基型值是會被轉為 false 的值，它根本上都還是個物件，而只要是物件（即使是空物件），就會被轉為 true。

```js
const a = new String('');
const b = new Number(0);
const c = new Boolean(false);

!!a // true
!!b // true
!!c // true
const d = Boolean(a && b && c)
const e = a && b && c;
```

#4. ToPrimitive
* undefined -> undefined（基本型別值，不轉換）。
* null -> null（基本型別值，不轉換）。
* boolean -> boolean（基本型別值，不轉換）。
* number -> number（基本型別值，不轉換）。
* string -> string（基本型別值，不轉換）。
* object -> 使用 [[DefaultValue]] 內部方法，依照傳入的參數來決定要使用 toString 或 
    valueOf 取得基本型別值(可以valueOf就用，結果不是基本型再toString)


* * *
下面這段程式碼，就是遵照不同的規則在轉型:

```js
const a = {
 toString: function() { return 54321 },
};
const b = {
 valueOf: function() { return 12345 },
};
String(a) // "54321"，遵照toString規則
b + '' // "12345"，遵照toPrimitive規則
```

* * *
### 3. 補充 JSON.stringify
JSON.stringify(..) 將一個值序列化為一個JSON兼容的string值，那它算是強制轉型嗎?
NO! 但這個轉為JSON字串的過程與 ToString 規則有關，所以在此補充說明，

> 任何"JSON安全值"都可以被 JSON.stringify(..) 字符串化。但是什麼是"JSON安全值"?
就是可以安全轉換合法表示的值，像是基本型別的值，都會 ToString()，其他像是：undefined、function、（ES6+）symbol、和帶有循環引用的 object（一個對象結構中的屬性互相引用而造成了一個永不終結的循環）都是不安全的。

> 為甚麼他們都無法合法的轉換? 
因為! 它們不能移植到其他語言中使用。

> 那不安全的值會怎麼處理?
如果在 array 中遇到，會被換成 null，如果在 object 中遇到，會被剃除。

  ```js
  JSON.stringify(undefined) // undefined，忽略非法值
  JSON.stringify(function() {}) // undefined，忽略非法值
  JSON.stringify(Symbol()) // undefined，忽略非法值
  JSON.stringify([1, 2, 3, undefined]) // "[1,2,3,null]"，非法值以 null 取代
  JSON.stringify({ a: 2, b: function() {}}) // "{"a":2}"，忽略非法屬性

  const a = { someProperty: 'Jack' };
  const b = { anotherProperty: a };
  a.b = b;
  JSON.stringify(a) // Uncaught TypeError: Converting circular structure to JSON
  JSON.stringify(b) // Uncaught TypeError: Converting circular structure to JSON
  ```

> 那我不小心寫非法的物件，想要不報錯誤、順利運行，怎麼辦?
如果是物件且有定義 toJSON 方法則會優先呼叫這個方法，並依照這個方法，回傳安全值。
  ```JS
  const someObj = {
    a: 2,
    b: function() {}, // 非法!
    toJSON: function() {
      return {
        a: 2, // 序列化過程只包含 a 屬性
      }
    },
  }
  JSON.stringify(someObj); // "{"a":2}"
  ```
  ```js
  const a = {
    someProperty: 'Jack',
    toJSON: function() {
      return {
        prompt: 'Hello World'
      }
    },
  };

  const b = {
    anotherProperty: a ,
    toJSON: function() {
      return {
        prompt: 'Hello World'
      }
    },
  };

  a.b = b;

  // 序列化成功！不會被報錯了！
  JSON.stringify(a) // "{"prompt":"Hello World"}"
  JSON.stringify(b) // "{"prompt":"Hello World"}"
  ```

* * *
### 4. 明確的強制轉型（Explicit Coercion）
  指程式碼中刻意寫出來的明顯的型別轉換的動作。

  * Numbers -> Strings
    1. 使用內建函式 String(..)
      原生建構器，但沒有new，所以不會產生物件包裹器。
      https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/String

      ```js
      String(123) // "123"
      ```

    2. 使用 toString(..)
      基本型別中，沒有toString()，JS會進行封裝，產生物件包裹器，作者稱這個轉換叫作"明確的隱含"。
      ```js
      var a = 42;
      var b = a.toString();
      (123).toString();
      ```


  * Strings -> Numbers
    1. 使用內建函式 Number(..)
        原生建構器，但沒有new，所以不會產生物件包裹器。
        https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Number

        ```js
        Number('123') // 123
        ```

    2. 使用位元否定運算子 ~ 轉數字

        只有32位元內的數字可以用。
        可以先參考http://140.129.118.16/~richwang/99-2-Courses/About_C_BitOperation.pdf
        位元否定運算子的功能是進行二進位的補數（公式為 ~x 得到 -(x+1)，例如：~42 得到 -43），
        很類似 "!" 強制將值轉為布林並反轉其真偽的運作方式。

        先跳脫思考，我們要怎麼將-(x+1)變成 0，x必須要等於-1才做得到!其他值都不會，轉成布林時，其他值都會是truthy的數字。-1是很特別的。在C語言中很多的函式都設計成"成功"傳回">=0"的值，失敗傳回-1。
        indexOf就是遵照這樣設計的，在有找到值時會回傳>=0的值

        因此很多人會這樣寫判斷式，有四種寫法:
          ```js
          if(a.indexOf("123") >= 0){}
          if(a.indexOf("123") != -1){}
          if(a.indexOf("123") < 0){}
          if(a.indexOf("123") ===-1 ){}
          ```
        作者不喜歡這樣寫!如果直接改寫，會比較簡潔，更是強制轉型的機制
        ```js
        if(~a.indexOf("123")) //會是0或者是以外的正整數，那就可以使用布林判斷了
        ```

        補充: ~~又是啥小?
        ~~就是反轉再反轉，得到截斷小數的結果，類似!!的雙重否定。在正整數中就像是 Math.floor() 得到無條件捨去的最大整數!
        但但但!!與 Math.floor() 在負數的結果不同。如下，Math.floor(-29.8) 得到 -30，而 ~~-29.8 得到 –29。
        另外，使用 x | 0 也可以得到同樣的效果，差別只在於 ~~ 運算子優先權較高，遇到四則運算時不用包小括號。
        ```js
        Math.floor(-29.8) // -30
        ~~-29.8 // -29
        -29.8 | 0 // -29
        ```

    3. 使用單元運算子(一元正/負運算子) +、- 轉數字
        只有一個運算符號就會發生強制轉型!
        ```js
        var c = '3.14';
        var d = +c;
        +('123');
        -('-123');
        ```
        但很容易跟遞增運算子++、遞減運算子--、或是二元運算子的+、-運算混淆。

        補充: 單元運算子很常搭配日期轉數字使用!
        當new Date()要轉成數字:
          1. 強制轉型 +
          ```js
          var timeStamp = +new Date();
          var timeStamp1 = +new Date; // new可以省略()
          ```
          2. 內建方法 getTime()
          ```js
          var timeStamp = new Date().getTime();
          // var timeStamp = new Date.getTime();
          ```
          3. 不使用new，使用ES5新增的 Date.now();
          ```js
          var timeStamp = Date.now();
          ```
          https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Date/now

        > 結論
        現在時間用Date.now();
        非現在時間用new Date().getTime();


    4. 使用 parseInt(..) ~ 轉數字
        除了Number(..)，parseInt(..) 函式能將輸入的字串轉成整數，若輸入非字串的值則會使用 ToString 強制轉為字串再轉成數字。

        與 Number(..) 的不同?

        * 在由左至右掃描值的過程中，遇到非數值字元就停下來（忽略後續部份），只轉換到停下來之前所得到的數值。除非整個字串都是非數值，否則不會得到 NaN。而 Numer(..) 則是只要傳入的字串不是可轉成數值的，就會得到 NaN。

        * parseInt(..)要指定進位系統的數字。一定要定義這個參數以避免他人的困惑、也好預估函式的行為。
        若沒有輸入第二個參數來指定基數，就會以第一個參數的頭幾個字元決定基數為何，例如：開頭若為 0X 就會轉為十六進位的數字。通常預設值是 10 進位。
          ```js
          var a = '12';
          var b = '12px';

          Number(a); // 12
          parseInt(a); // 12

          Number(b); // NaN
          parseInt(b); // 12
          parseInt('HelloWorld'); // NaN
          ```

  * -> Boolean
    1. 使用內建函式 Boolean(..)
      https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Boolean
    2. 否定運算子 !
      !強制轉型布林，但會反轉。使用!!就可以了
### 5. 隱含的強制轉型（Implicit Coercion）
  指在程式碼中沒有明確指出要轉換型別但卻轉型的動作。
  * Numbers -> Strings
    使用加號串聯
    參考: http://eddychang.me/js-object-plus-object/
    看完後就可以來做練習
    ```js
    const a = '1';
    const b = 1;
    const c = [1, 2];
    const d = [3, 4];

    a + 1 // "11"
    b + 1 // 2
    b + '' // "1"
    c + d // "1,23,4"

    [] + {} // "[object Object]"
    {} + [] // 0
    ```
  * Strings -> Numbers
    使用運算符號
    ```js
    const a = '1';
    a + 1 // "11"
    a - 0 // 1
    a * 1 // 1
    a / 1 // 1
    [9] - [7] // 2
    ```
  * -> Boolean
    在什麼狀況下會隱含地將值強制轉為布林呢？
      1. if 述句中的條件判斷
      2. for 述句中的條件判斷
      3. while 與 do…while
      4. 三元運算式 條件 ? 值1 : 值2
      5. 邏輯運算子的 ||（or） 和 &&（and)

> 運算子 || 與 &&
  ||（or）來說，若結果為 true，則取第一個運算元為結果；若結果為 false，則取第二個運算元為結果。
  &&（and）來說，若結果為 true，則取第二個運算元為結果；若結果為 false，則取第一個運算元為結果。
  所以 
  ||（or） 可用來設定變數的初始值。
  &&（and）可用來執行「若特定條件成立，才做某件事情」，功能近似 if 述句。
```js
const a = 'Hello'
const b = 777;
const c = null;
a && c;
a && b;
a || b;
c || 'foo';

const flag = true;

function foo() {
  console.log('try me');
}

flag && foo();
```
注意: &&或是||產生的可能不是布林。
下面的判斷中是因為if強制轉型為布林，不然其實是'foo'
```js
var a = 42;
var b = null;
var c = 'foo';
if(a && (b || c)){
  console.log('yep');
}
```
### 6. 寬鬆相等（Loose Equals） vs 嚴格相等（Strict Equals）
  * 寬鬆相等「==」、「!==」，嚴格相等「!==」、「===」。
    他們都會做型別的檢查，只是當面對型別不同時的反應是不一樣的而已。效能尚無明顯差異，但建議用嚴格相等避免錯誤發生!
    == 會做強制轉型，而 === 不會! 
    ```js
    const a = '100';
    const b = 100;

    a == b // true，強制轉型，將字串 '100' 轉為數字 100
    a === b // false
    ```
    同類別可以直接比較，但要注意!
    * NaN（NaN 不大於、不小於也不等於任何數字，也不等於它自己）
    * +0、-0 彼此相等。
    * 物件（含 function 和 array）的相等是比較參考（reference），若參考相等才是相等。==和===的行為是相同的。
    
    不同類別，要依照規則:
    * string、boolean優先轉數字
    * 若是物件，使用ToPrimitive規則，valueOf()（優先）或 toString() 將物件取得基本型別的值，再做比較。
    * null 與 undefined 在寬鬆相等下會強制轉型為彼此，也相等的自己，但不等於其他值
        ```js
        var a = doSomething();
        if (a == null) {};
        // a == null檢查僅在doSomething()返回null或者undefined時才會通過，而在任何其他值的情況下將會失敗，即便是0，false，和""這樣的falsy值。
        ```

練習~
1. 
```js
var a = "42";

if (a == true) {} 

if (a === true) {}

if (a) {}

if (!!a) {}

if (Boolean( a )) {}
```

2. 
```js
var a = null;
var b;

a == b;
a == null;
b == null;

a == false;
b == false;
a == "";
b == "";
a == 0;
b == 0;
```

3. 
```js
var a = 42;
var b = [ 42 ];

a == b;
```

4. 
```js
var a = "abc";
var b = Object( a );

a === b;
a == b;
```

5. 
```js
var a = null;
var b = Object( a );
a == b;

var c = undefined;
var d = Object( c );
c == d;

var e = NaN;
var f = Object( e );
e == f;
```

6. 
```js
const a = '123';
const b = 123;

a === b;
a == b;
```

7. 
```js
const a = true;
const b = 123;

a === b;
a == b;
```

8. 
```js
const a = null;
const b = 123;

a === b;
a == b;
```

9. 
```js
const a = null;
const b = 0;

a === b;
a == b;
```

10. 
```js
const a = '1,2,3';
const b = [1,2,3];

a === b;
a == b;
```

11. 
```js
"0" == null;
"0" == undefined;
"0" == false;
"0" == NaN;
"0" == 0;
"0" == "";

false == null;
false == undefined;
false == NaN;
false == 0;
false == "";
false == [];
false == {};

"" == null;
"" == undefined;
"" == NaN;
"" == 0;
"" == [];
"" == {};

0 == null;
0 == undefined;
0 == NaN;
0 == [];
0 == {};

[] == ![];//[]==false

2 == [2];
"foo" == ["foo"];
"" == [null];
0 == '\n';
'true' == true;
```
### 7. 邊緣情況
  一些少見的狀況
  * 避免修改原型 valueOf(..)
    經由原生的內建函式所建立的值，由於是物件型態，在強制轉型時會經過 ToPrimitive 的過程，也就是使用 valueOf(..)（優先）或 toString(..) 將物件取得基本型別的值，才會做後續比較。因此，若修改了原型中的 toValue(..) 方法，則可能會導致比較時出現「不可思議」的結果。
    ```js
    Number.prototype.valueOf = function() {
      return 3;
    };

    new Number(2) == 3; // true
    ```
### 8. 安全的使用隱含的強制轉型
* 如果任一邊的比較有可能出現true或是false就不要使用==
* 如果任一邊的比較有可能出現[]、""、0，慎重考慮不要使用==
  盡量使用===，除了
  1. 比較 null 與 undefined 的強制轉型是安全的，因為它們互轉為彼此，一定相等
  2. 做 typeof x == '指定值' 一定是安全的
### 9. 抽象關係式比較
<（小於）、 >（大於）、<=（小於等於）、>=（大於等於）
規則:
* 若兩個運算元皆為字串時，就直接依照字典字母順序做比較。
* 其他，先使用 ToPrimitive 做強制轉型-先使用 valueOf 取得基型值，然後再用 toString 方法轉為字串。
* 若有任一值轉型後的結果不是字串，就使用 ToNumber 的規則轉為數字，來做數字上的比較。
```js
var a = [ 42 ];
var b = [ "43" ];

a < b;	// true
b < a;	// false
```
```js
var a = [ "42" ];
var b = [ "043" ];

a < b;	// false
```
```js
var a = [ 4, 2 ];
var b = [ 0, 4, 3 ];

a < b;	// false
```
```js
var a = { b: 42 };
var b = { b: 43 };

a < b;	// false
a == b;	// false
a > b;	// false

a <= b;	// true
a >= b;	// true
```
沒有辦法防止a < b這樣的關係型比較發生隱含的強制轉換，除非在進行比較之前就明確地確保a和b是同種類型。
如果你需要在關係型比較上獲得安全性，那麼在使用<（或>）之前，就首先明確地強制轉換這些值。