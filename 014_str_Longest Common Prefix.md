#### 014__字串題型__easy
# Longest Common Prefix <br/>尋找字串的共同前綴

### Description
Write a function to find the longest common prefix string amongst an array of strings. 
(All given inputs are in lowercase letters a-z.)
<br/>
If there is no common prefix, return an empty string "".

有一個陣列，裡面有許多單字 (都是小寫字母)。
<br/>
寫一個function，尋找他們的共同前綴字。如果沒有共同的部分，就回傳空字串。
<br/>

##### Example 1:
```js
    Input: ["flower","flow","flight"] 
    Output: "fl"
```
##### Example 2:
```js
    Input: ["dog","racecar","car"]
    Output: ""
```
##### start:
```js
/**
 * @param {string[]} strs
 * @return {string}
 */
var longestCommonPrefix = function(strs) {
    ...
}
```
* * *
### Thinking
1. 拿陣列的第一個字，暫時當作共同的前綴字 (字首)，跟陣列之後的每個字比較 (forLoop)。
2. 字首的每個字母進行做比對作業 (forLoop + charAt)，沒有的要去除(slice)。
* * *
### Submissions
```js
var longestCommonPrefix = function(strs) {
    let same = '';
    if(strs.length===0)return same;
    same = strs[0];
    for(let j=0; j<same.length; j++){ 
        for(let i=1; i<strs.length; i++){
            if(same.charAt(j)!==strs[i].charAt(j)){
                same = same.slice(0,j);
            }
        }
    }
    return same
};
```
###### Runtime: 60 ms
* * *
### Solution
###### 別人怎麼寫的? Runtime: 44 ms 的人的寫法
>Thinking:
    <br/><br/>
    一樣字母與文字做比對，『但只要判斷不同，就直接回傳，不繼續切字串，遇到相同的才需要後續處理，存起來』。
    <br/><br/>
```js
var longestCommonPrefix = function(strs) {
    let prefix = '';
    if (strs.length === 0) return prefix;
    for (let i = 0; i < strs[0].length; i++) {
        const character = strs[0][i];//每個字母
        for (let j = 0; j < strs.length; j++) {
            //陣列的每個字的字母跟第一個字的字母比對
            //如果不同就不繼續，直接回傳空值
            if (strs[j][i] !== character) return prefix;
        } 
        // 如果相同再做後續處理!
        prefix = prefix + character;
    }
    return prefix;
};
```
* * *
### Note
 存取字元有兩個方法，第一個是用 charAt，另一個(在ECMAScript 5中被提到)是將字串當作一個類似陣列的物件，直接存取字串中對應的數值索引。
```js
return 'cat'[1]; // 回傳 "a"
```