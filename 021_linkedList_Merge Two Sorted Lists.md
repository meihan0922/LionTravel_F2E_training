#### 021__連結串列題型__easy
# Merge Two Sorted Lists <br/>連結兩個排序過的連結串列

### Description

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

<br/>
有兩個排序過的連結串列，將它們合併、回傳新的連結串列。新表單要通過拼接兩個連結串列的節點來完成。

<br/>
<br/>

##### Example:
```js
    Input: 1->2->4, 1->3->4
    Output: 1->1->2->3->4->4
```

##### start:
```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var mergeTwoLists = function(l1, l2) {
    ...
};
```
* * *
### Thinking
以第一個連結串列(l1)為基準，一個值與另一連結串列(l2)值比較。
<br/>如果 l1值 <小於 l2值，就在新的連結串列增加l1的節點。
<br/>其他狀況就新增l2節點、l1節點，最後回傳新的連結串列。

* * *
### Submissions
```js
var mergeTwoLists = function(l1, l2){
  var newLink = new ListNode(0);
  var currentNode = newLink; //目前節點的位置
  //當節點鏈接到null表示是串列的末端了
  while(l1 && l2){
    if(l1.val > l2.val){
      currentNode.next = l2; //新增l2目前的值到newLink中
      l2 = l2.next;  //節點往後推移
    }else{
      currentNode.next = l1;//新增l1目前的值到newLink中
      l1 = l1.next;//節點往後推移
    }
    currentNode = currentNode.next;//節點往後推移
  }
  //若較短的連結串列都已經放入，長的連結串列還有剩值，要將剩下的值都放進去
  if(l1)currentNode.next = l1;
  if(l2)currentNode.next = l2;

  return newLink.next
}
```
###### Runtime: 80 ms
* * *
### Solution
###### 別人怎麼寫的? Runtime: 48 ms 的人的寫法
```js
var mergeTwoLists = function(l1, l2) {
  let dummy = new ListNode();
  let dummyHead = dummy;

  while (l1 && l2) {
    if (l1.val < l2.val) {
      const temp = new ListNode(l1.val);
      dummy = dummy.next = temp;
      l1 = l1.next;
    } else {
      const temp = new ListNode(l2.val);
      dummy = dummy.next = temp;
      l2 = l2.next;
    }
  }

  if (l1) dummy.next = l1;
  if (l2) dummy.next = l2;

  return dummyHead.next;
};
```
* * *
### Note
連結串列，不是陣列，
<br/>
參考: https://blog.kdchang.cc/2016/09/21/javascript-data-structure-algorithm-linked-list/





