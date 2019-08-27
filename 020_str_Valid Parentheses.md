#### 020__字串題型__easy
# Valid Parentheses 合法的括號

### Description

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid. Note that an empty string is also considered valid.

An input string is valid if:
1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.

<br/>
有一個字串，裡面有許多單字 '(', ')', '{', '}', '[' and ']'。
<br/>
寫一個function，判斷這段字串是合法的，回傳布林。
<br/>
什麼是合法的? 
1. 必須是成對的括號
2. 必須是放在對的地方
<br/>注意就算是空字串也一樣合法。
<br/>
<br/>

##### Example 1:
```js
    Input: "()"
    Output: true
```
##### Example 2:
```js
    Input: "()[]{}"
    Output: true
```
##### Example 3:
```js
    Input: "(]"
    Output: false
```
##### Example 4:
```js
    Input: "([)]"
    Output: false
```
##### Example 5:
```js
    Input: "{[]}"
    Output: true
```
##### start:
```js
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function(s) {
    ...
};
```
* * *
### Thinking
1. 去除掉所有文字、空白，和單引號，只留下括號
2. 如果是奇數回傳false，如果是空字串回傳true
3. 利用正規表達式跟回圈，重複把已經成對的刪除，最後都被刪掉就是true了
* * *
### Submissions
```js
var isValid = function(s) {
    const reg = /([A-Z])\w+/gi;
    const reg1 = /(\'+)/gi;
    let newStr = s.replace(reg,'').replace(reg1,'').replace(' ','');
    let reg2 = /(\(\))|(\[\])|(\{\})/gi;
  	
    if(newStr.length===0)return true;
    if(newStr.length%2!==0)return false;
	const mult = newStr.length/2;//最多會做幾次
	for(let i=0; i<mult; i++){
      if(reg2.test(newStr)){
     		newStr = newStr.replace(reg2,'');
 		}
      return newStr==="";
	}
	return false;
};
```
###### Runtime: 68 ms
* * *
### Solution
###### 別人怎麼寫的? Runtime: 44 ms 的人的寫法
>Thinking:
    <br/><br/>
    1. <br/>
    當括號包裹括號，會先一直碰到左括號，直到遇到右括號為止。
    而這個右括號又必須與上個左括號是成對的。所以直接判斷就可以知道是否正確。
    <br/><br/>
    2. <br/>
    『利用stack堆疊的概念實作! stack是先進後出。』
    先使用物件建立左右括號成對的部分。
    如果有符合左邊括號的話，就push左邊括號進去陣列中，遇到右括號時比對物件中左括號key的value。
    <br/>使用pop拔起來比較，原陣列也會刪除。
    <br/><br/>
    3. <br/>
    當最後比較完，變成空陣列，就等於是true。
    <br/><br/>
```js
var isValid = function(s) {
  let stack = [];
  let pairsHashMap = {
    '{': '}',
    '(': ')',
    '[': ']'
  };
        
  for (let i = 0; i < s.length; i++) {
    let char = s[i]; //字串中的每個字
    
    if (pairsHashMap[char]) {
        // if current character is left
        //如果這個字有符合我們要找的左邊括號的話，就push左邊括號進去陣列
      stack.push(char);
    } else if (pairsHashMap[stack.pop()] !== char) {
        // 如果遇到右邊括號的話，從陣列的前面拔出來元素，並且跟字串中的字比對，如果不符合就回傳false
      return false;
    }
  }
  
  return stack.length === 0;
};
```
* * *
### Note
用 JavaScript 學習資料結構和演算法：堆疊（Stack）篇: https://blog.techbridge.cc/2016/06/24/javascript-data-structure-algorithm-stack/


