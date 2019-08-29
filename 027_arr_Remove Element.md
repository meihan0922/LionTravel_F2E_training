#### 027__陣列題型__easy
# Remove Element<br/>移除元素

### Description
Given an array nums and a value val, remove all instances of that value in-place and return the new length.
<br/>
Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.
<br/>
The order of elements can be changed. It doesn't matter what you leave beyond the new length.
<br/><br/>
給一個陣列nums和一個值val，如果陣列內元素等於值就刪除，最後返回新長度。
<br/>
不能再另外建立陣列，必須要使用內存來修改項目。
<br/>
最後生成的陣列元素順序可以改變。

##### Example 1:
```js
    Given nums = [3,2,2,3], val = 3,

    Your function should return length = 2, with the first two elements of nums being 2.

    It doesn't matter what you leave beyond the returned length.
```
##### Example 2:
```js
    Given nums = [0,1,2,2,3,0,4,2], val = 2,

    Your function should return length = 5, with the first five elements of nums containing 0, 1, 3, 0, and 4.

    Note that the order of those five elements can be arbitrary.

    It doesn't matter what values are set beyond the returned length.
```

##### start:
```js
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function(nums, val) {
    ...
}
```
* * *
### Clarification:
Confused why the returned value is an integer but your answer is an array?
<br/>
Note that the input array is passed in by reference, which means modification to the input array will be known to the caller as well.

是不是感覺很困惑，為甚麼return是一個整數，但答案是一個數組?
<br/>
因為，輸入的陣列是通過引用參數傳入，這表示調用的人也可以知道這個函式對數組進行了甚麼修改。

```js
// nums is passed in by reference. (i.e., without making a copy)
int len = removeDuplicates(nums);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

* * *
### Thinking
1. 回圈和val值進行比對，如果不要就切掉(splice，對原陣列操作)
2. 如果陣列長度是零，或是nums根本搜尋不到val就直接返回陣列長度
* * *
### Submissions
```js
var removeElement = function(nums, val) {
  if(nums.length == 0) return nums.length;
  if(nums.indexOf(val) < 0) return nums.length;
  for(let j=0; j<nums.length; j++){
    if(nums[j]===val){
      nums.splice(j,1);
      j--;
    }
  }
  return nums.length;
};
```
###### Runtime: 56 ms
* * *
### Solution
###### 別人怎麼寫的? Runtime: 40 ms 的人的寫法
>Thinking:
    <br/><br/>
    1.<br/>
    使用while回圈。<br/>
    while 迴圈會在條件符合的時候，執行一段程式碼，直到條件不符合為止，常常用在顯示搜尋結果或是目錄清單等地方。
    <br/><br/>
    2.<br/>
    使用indexOf的第二個參數!<br/>
    第二個參數表示要從哪裡開始搜尋，這個索引值大於或是等於陣列長度十會直接回傳-1，就不進行搜尋。
    <br/>就可以少寫兩行判斷式! 不用浪費效能!
    <br/><br/>
```js
var removeElement = function(nums, val) {
    while(nums.indexOf(val,0) >= 0) {
        //同時處理陣列長度不足與陣列根本沒有val的問題
        nums.splice(nums.indexOf(val,0), 1);
    }
    return nums.length;
};
```