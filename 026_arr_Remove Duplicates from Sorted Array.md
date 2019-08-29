#### 026__陣列題型__easy
# Remove Duplicates from Sorted Array<br/>從排序過的陣列中，刪除重複的數字

### Description
Given a sorted array nums, remove the duplicates in-place such that each element appear only once and return the new length.
<br/>
Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.
<br/><br/>
給一個排序的陣列: nums，就地刪除重複的項，使每個元素只出現一次，並返回陣列的長度。
<br/>
不能再另外建立陣列，必須要使用內存來修改項目。
<br/>

##### Example 1:
```js
    Given nums = [1,1,2],

    Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.

    It doesn't matter what you leave beyond the returned length.
```
##### Example 2:
```js
    Given nums = [0,0,1,1,1,2,2,3,3,4],

    Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively.

    It doesn't matter what values are set beyond the returned length.
```

##### start:
```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function(nums) {
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
我想分別實作 :
* 回傳刪除重複項後的陣列
* 回傳刪除重複項後的陣列長度
<br/><br/>
#### > 回傳刪除重複項後的陣列 :
條件是: 不能建立一個新陣列，只能操作原陣列
1. 如果陣列長度小於等於1，就返回原陣列
2. 回圈從1開始，使用i變數來紀錄前一筆索引值

```js
var removeDuplicates = function(nums) {
    if(nums.length<=1)return nums;
    let i=0;
    for(let j=1; j<nums.length; j++){
        if(nums[i]===nums[j]){
            nums.splice(i,1);
            j--;
        }else{
            i++;
        }
    }
    return nums;

```
###### Runtime: 88 ms

#### > 回傳刪除重複項後的陣列長度 :
條件是: 不能建立一個新陣列，只能操作原陣列
1. 如果陣列長度等於0，就返回0
2. 回圈從1開始，使用i變數來紀錄不一樣的次數，不一樣的次數再加1就是陣列的長度了

```js
var removeDuplicates = function(nums) {
    if(nums == null || nums.length == 0) return 0;
    let i = 0;
    for(let j=0; j<nums.length; j++){
        if(nums[i]!==nums[j]){
            i++;
            nums[i]=nums[j];
        }
    }
    return ++i;
}
```
###### Runtime: 56 ms
* * *
### Solution
###### 別人怎麼寫的? Runtime: 44 ms 的人的寫法
>Thinking:
    <br/><br/>
    1.<br/>
    如果陣列為空，返回。
    <br/><br/>
    2.<br/>
    把第一個元素複製出來後，放進陣列最後。
    <br/><br/>
    3.<br/>
    回圈，如果元素大於前一個元素，就把他放進陣列的後方。不然的話，就往後比對下兩筆。
    <br/><br/>
    4.<br/>
    最後會複製出沒有重複的陣列在原陣列的後方，只要再切除本來的陣列即可! 
    <br/>
    (只切除一次而已!提升效能)
    <br/><br/>
```js
var removeDuplicates = function(nums) {
    if (!nums.length)return;
    const length = nums.length;
    let previousNumber = nums[0];
    nums.push(nums[0]);
    for (let index = 0; index < length; index++) {
        if (nums[index] > previousNumber) {
            nums.push(nums[index])
        }
        previousNumber = nums[index]
    }
    nums.splice(0, length)
};
```
