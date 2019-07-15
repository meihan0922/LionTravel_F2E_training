> 你所不知道的JS讀書會，作者meihan，簡報檔
https://docs.google.com/presentation/d/1mKMEWeJwXr5SliG8E4AY5nLaSBRuH8Qj9H9yM-NdD0M/edit?usp=sharing

>感謝桑莫夏天的學習筆記
https://cythilya.github.io/tags/%E4%BD%A0%E6%89%80%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84JS/


# Natives 原生功能
  1. 什麼是Natives? 最常使用的原生功能有哪些?
  2. 為什麼String、Number、Boolean是基本型別，卻會有"屬性"、"方法"可以呼叫?
  3. 如果把Natives用建構式（constructor）來建立會發生什麼事??
  4. 什麼是物件包裹器? 包裹器有什麼特性?
  5. 物件包裹器的陷阱（Object Wrapper Gotchas）與 解封裝（Unboxing）
  6. Internal [[Class]]
  7. 建構子的原生功能
  8. 原生的原型（Native Prototype）
  9. 總結

* * *
### 1.什麼是原生功能?
  就是「內建功能」（built-ins）

  最常使用的原生功能有哪些?
    * String()
    * Number()
    * Boolean()
    * Array()
    * Object()
    * Function()
    * RegExp()
    * Date()
    * Error()
    * Symbol()

    其中null和undefined沒有內建函式
  參考:https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects

* * *
### 2.為什麼String、Number、Boolean是基本型別，卻會有"屬性"、"方法"可以呼叫?
  因為"封裝(boxed)"，會讓它"自動轉型"!!!，當你去存取 String、Number 與 Boolean 這三種基本型別的屬性時，它就只會在「那一刻」被轉型為該類別的「物件」

    ps.toString()和[[Class]]，ES5、ES6有些不同
    pps.如果你忘記基本型別是什麼的話...
    JS分成七個內建型別，null、undefined、boolean、number、string、object、symbol，除了object都是基本型別

    ```js
    var str = 'Hello';
    console.log( str.length );
    ```

    其實在背後是這樣運作的

    ```js
    var str = new String("Hello");
    str.length;

    str = null;
    str = 'Hello';
    ```
### 2.那既然是這樣，我們可以直接用new來宣告基本型別，就不用經過背後運作，比較省效能吧?
  基本型別的處理與運算效率>物件型別，除了瀏覽器已經針對基本型別進行最佳化了，
  使用基本型別，讓JS預先編譯、自動封裝、快取起來。如果使用物件型別"預先最佳化"，反而會執行得更慢


* * *
### 3.如果把原生功能用建構式（constructor）來建立??
  就會產生包了基本型別的物件包裹器(object wrapper 或是 Primitive Wrapper Objects)

* * *
### 4.那什麼是物件包裹器?
  內建的型別主要可以分成基本型別 (Primitives) 與物件型別 (Object) 兩大類。
  從物件型別當中又可以細分出幾種「建構器」(Constructor)，可以使用new來產生對應的物件，
  這些「建構器」(Constructor)也就是原生功能、內建函式，注意他不是class類別
### 4.包裹器的特性:
  1. 透過 instanceof 來確認它的基本型別是什麼
  2. 他們都是物件
  3. 透過.valueOf()來取得原始值
  4. 他們都是透過"原型"的繼承來取得方法的

  ```js
  var str = new String('Hello World!');
  typeof str; //Object
  str instanceof String; // true
  str.toString() // "Hello World!"
  str.valueOf(); // "Hello World!"
  ```

* * *
### 5.物件包裹器的陷阱（Object Wrapper Gotchas）
  直接使用物件形式的物件包裹器來宣告變數會造成一些誤用，因此非常不建議這麼做
  ```js
  const isValid = new Boolean(false);

  if (isValid) {
    console.log('true...');
  } else {
    console.log('false...'); // true....
  }
  ```
  因為false被物件包裹住，物件本身一直是true的存在，所以印不出裡面的false!!

* * *
### 5.解封裝（Unboxing）
  只好使用 valueOf 來抽出底層的基型值，或是讓它發生強制轉型
  ```js
  var a = new String("abc");
  var b = a + "";
  typeof a; //"object"
  typeof b; //"string"
  ```

* * *
### 6.Internal [[Class]]
  typeOf()為"object"，會額外標示一個內部的特性[[Class]]，可以想成它是一個內部的分類，
  但這個特性無法直接的取用，預設方法Object.prototype.toString(...)，就可以看到:
  ```js
  Object.prototype.toString.call(123456789); // "[object Number]"
  Object.prototype.toString.call('Hello World'); // "[object String]"
  Object.prototype.toString.call(true); // "[object Boolean]"
  Object.prototype.toString.call([1, 2, 3]); // "[object Array]"
  Object.prototype.toString.call({ name: 'Jack' }); // "[object Object]"
  Object.prototype.toString.call(function sayHi() {}); // "[object Function]"
  Object.prototype.toString.call(/helloworld/i); // "[object RegExp]"
  Object.prototype.toString.call(new Date()); // "[object Date]"
  Object.prototype.toString.call(Symbol('foo')); // "[object Symbol]"
  ```
  但是!它不一定會對應到原生函式
  ```js
  Object.prototype.toString.call(null); // "[object Null]"
  Object.prototype.toString.call(undefined); // "[object Undefined]"
  ```
  ps.上次有討論到call()，他是JS預設的方法，可以用在物件身上，調用它作為參數使用
  可以看W3:https://www.w3schools.com/js/js_function_call.asp
  ```js
  var person = {
    fullName: function() {
      return this.firstName + " " + this.lastName;
    }
  }
  var person1 = {
    firstName:"John",
    lastName: "Doe"
  }
  person.fullName.call(person1); //John Doe
  ```
  它也可以擁有參數

  ```js
  var person = {
    fullName: function(city, country) {
      return this.firstName + " " + this.lastName + "," + city + "," + country;
    }
  }
  var person1 = {
    firstName:"John",
    lastName: "Doe"
  }
  person.fullName.call(person1, "Oslo", "Norway"); //John Doe,Oslo,Norway
  ```

* * *
### 7.建構子的原生功能 -- Array()、Object()、Function()、RegExp()
  最好還是不要用，很容易產生錯誤跟陷阱

  * Array(..)
    1. new Array(...) 產生結果同 Array(...)
    2. 只傳入一個數字，會被當成是陣列長度來預先設定陣列的大小(上次說的稀疏陣列)
    ```js
    const a = Array(10);
    a // (10) [empty × 10]
    a.length // 10

    const b = [undefined, undefined, undefined];
    delete b[1] // true，成功刪除一個元素？
    b // [undefined, empty, undefined]，這裡產生一個空插槽！
    ```
    其他各別瀏覽器對於空插槽的處理，請看書裡的補充

  * RegExp(..)
    1. 一般來說直接建立就好，正規表達式字面值在 script 載入時會被編譯，當正規表達式為定值時，使用此方法可獲得較佳效能
    ```js
    var re = /ab+c/;
    ```
    2. 「動態地」為正規表達式建立範式（pattern），就可以使用new RegExp('pattern', 'flags')
    ```js
    const name = 'Apple';
    const pattern = new RegExp("\\b(?:" + name + ")+\\b", "ig");
    const matches = 'Hi, Apple'.match(pattern);

    matches // ["Apple"]
    ```
    其他太冷門，請看書裡的範例

* * *
### 7.建構子的原生功能 -- Date()、Error()、Symbol()
  Date 與 Error 沒有字面值格式，只能用物件包裹器作為建構子的方式建立物件
  * Date()的功能就自己上MDN看了，在此把握時間不說明，https://developer.mozilla.org/zh-TW/docs/Web/  JavaScript/Reference/Global_Objects/Date

  * Error()
    1. new Error(...) 產生結果同 Error(...)
    2. 通常會藉由throw來使用:
    ```js
    function foo(x){
        if(!x){
            throw new Error("x wasn't provided");
        }
        //...
    }
    ```
    3. Error()的實體(instances)一般至少會有一個message特性，有時會有其他特性(視為唯讀)。最好是在Error()上呼叫toString()，
    以取得經過格式化方便閱讀的錯誤訊息
    除了Error()之外，還有其他關於錯誤的原生功能，像是ReferenceError(..)，會自動地被使用，很少會手動操作
    https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Error

    * Symbol()
      Symbol 同樣沒有字面值格式，若要自定義的 Symbol，就要使用建構子 Symbol(..) 且不可在前面加上 new，否則會報錯。其他我們等ES6的章節再來講
### 8.原生的原型（Native Prototype）
  * 每個建構子都有自己的.prototype物件，例如：Array.prototype、String.prototype 等，
    而這些 .prototype 物件擁有各自子物件的專屬行為。
    建構子建立的物件 與 經由 JavaScript 引擎封裝的字面值，由於原型委派（prototype delegation）的緣故，都能使用定義於 .prototype 的屬性和方法。

      ps. String.prototype.XXX 可以簡寫成 String#XXX

    像是new String("hello")跟宣告的"hello"都可以使用string預設的方法String#indexOf、String#chartAt、String#substring.....。

  * 但有些原生原型，並不只是普通的物件，可以修改原生的原型，最好不要這樣
    ```js
    typeof Function.prototype; // "function"
    Funciton.prototype(); // empty function

    RegExp.prototype.toString(); // "/(?:)/ 空的正規表達式"
    "abc".match(RegExp.prototype); // [""]

    Array.isArray(Array.prototype); //true
    Array.prototype.push(1,2,3);
    Array.prototype; // [1,2,3]
    ```
  * 可以看到Array.prototype 是空陣列，Function.prototype 是空的函式，RegExp.prototype 是空的正規表達式，   因此有人會拿來做為變數的初始值，(.prototype內建的，也只會建立一次，和直接創建[]、{}、function(){}不同)  ，雖然可能節省了重新創建新值和垃圾回收的工作而讓效能變好，但這可能會在無意間修改了這些預設的原生的原型

* * *
### 9.總結
  * Natives（原生功能）即是「內建函式」，像是 String(..)、Number(..) 等，除了使用字面值，也可用 Natives     當成建構子來建立值
  * 不建議使用 Natives 當成建構子來建立值！
  * 不要任意修改這些預設的原生的原型，甚至不要無條件地擴充原生的原型，若要擴充也應撰寫符合規格的測試程式。
    不要使用原生的原型當成變數的初始值，以避免無意間的修改