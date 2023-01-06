---
title: 面試經驗
date: 2022-12-5 14:44:26
categories:
tags: Careers, Interview
---

# 前端面試考題

1. 列舉 web storge 與特性，適合的使用時機

   - cache
   - local storge
   - session storge

2. JS event quene, microtask and macrotask

   - micro : promise, async
   - macro : web api, settimeout, setinterval

3. CROS and prevent

   - Set Http header
   - JSONP
   - Proxy
   - Post Message (iframe)

4. Browser render pipeline

   - Parse HTML HTML document parsed and the DOM tree as we know it has been built.
   - Recalculate Styles All kind of stylesheets are parsed. Render tree is built. Render tree contains DOM tree nodes that are going to be displayed. Thus, head tag is excluded as well as style and script. Elements with CSS display property set to none are also ignored. On the other hand, pseudo elements, such as :after and :before, are included. Moreover, every text line becomes a single block.
   - Layout (also called reflow) A collection of blocks generated from the render tree. All the block dimensions, that are dependent on each other, are calculated.
   - Paint Rasterization of the blocks and texts. Images decoding and resize if necessary also happen here.
   - Layer Composition Composition of the visual layers that can be processed and painted independently.

5. Hoasting
6. Scope for this
7. Filter duplicate value in array

   - Using Array Filter
   - Using Set and Destructuring
   - Using Object to store

8. Destructuring
9. Spread/Rest Operator
10. vue-router 的 hash 和 history mode

    - hash 模式下的 URL 會帶有 #，跳轉時不會發出 HTTP 請求。限制是不利 SEO，以及會與錨點功能有衝突等。
    - history 模式不會有 #，跳轉時會發出 HTTP 請求。限制是瀏覽器兼容性較差，以及後端需要自行設定，避免 404 問題。

11. vue-router 的 navigation guards 與其 hook

    - 全域
      - beforeEach
      - beforeResolve
      - afterEach
    - 路由
      - beforeEnter
    - 元件
      - beforeRouteEnter
      - beforeRouteUpdate
      - beforeRouteLeave

12. Vue Lift Cycle

    - beforeCreate
    - created
    - beforeMount
    - mounted
    - beforeUpdate
    - updated
    - beforeUnmount (3.x)
    - unmounted (3.x)

13. Vuex ?

14. Vue two-way binding

# Algorithms

## LeetCode - 136. Single Number

- my answer

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function (nums) {
  return nums.filter((item, index, arr) => {
    return arr.indexOf(item) == index && arr.lastIndexOf(item) == index;
  });
};
```

Time complexity: O(n), Space complexity: O(0)

- perfact answer (使用互斥 ^)

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function (nums) {
  var result = nums[0];
  for (var i = 1; i < nums.length; i++) {
    result ^= nums[i];
  }
  return result;
};
```

Time complexity: O(n-1), Space complexity: O(0)

## string match

- my answer : 利用兩個指標從前後同時尋找配對字串

```javascript
var a = "RTGDAFT",
  b = "GDA";
var start_a = 0,
  end_a = a.length - 1;
var start_b = 0,
  end_b = b.length - 1;
var match = false;
while (start_a < end_a) {
  if (a[start_a] == b[start_b]) {
    // match = a[start_a];
    match = true;
    while (start_b < end_b) {
      start_a++;
      start_b++;
      if (a[start_a] != b[start_b]) {
        match = false;
        break;
      }
      // match = match + a[start_a];
    }
    if (match) break;
  }
  if (a[end_a] == b[end_b]) {
    // match = a[end_a];
    match = true;
    while (start_b < end_b) {
      end_a--;
      end_b--;
      if (a[end_a] != b[end_b]) {
        match = false;
        break;
      }
      // match = a[end_a] + match;
    }
    if (match) break;
  }
  start_a++;
  end_a--;
  start_b = 0;
  end_b = b.length - 1;
}
```

Time complexity: O(n / 2 \* m), Space complexity: O(0)

## LeetCode - 763. Partition Labels

- my answer : 儲存字元的起始與結束點，看各字元是否有接續

```javascript
/**
 * @param {string} s
 * @return {number[]}
 */
var partitionLabels = function (s) {
  let result = [];
  let temp = {};
  for (let i = 0; i < s.length; i++) {
    const char = s[i];
    if (!temp[char]) {
      temp[char] = { start: i, end: i };
    }
    if (i > temp[char].end) {
      temp[char].end = i;
    }
  }
  let max = -1;
  let start = 0;
  for (let i in temp) {
    if (temp[i].start > max && max !== -1) {
      result.push(max - start + 1);
      start = temp[i].start;
    }
    max = Math.max(max, temp[i].end);
  }
  result.push(max - start + 1);
  return result;
};
```

Time complexity: O(n + n), Space complexity: O(n)

## LeetCode - 22. Generate Parentheses

- my answer : 使用遞迴去組成

```javascript
/**
 * @param {number} n
 * @return {string[]}
 */
var generateParenthesis = function (n) {
  let result = [];
  const dp = (str, openNum, closeNum) => {
    if (openNum < closeNum) return;
    if (openNum == n) {
      for (let i = str.length; i < n * 2; i++) str += ")";
      result.push(str);
      return str;
    }
    dp(str + "(", openNum + 1, closeNum);
    dp(str + ")", openNum, closeNum + 1);
  };
  dp("(", 1, 0);
  return result;
};
```

Time complexity: O(2^n), Space complexity: O(n)

## counting character

- my answer : XXXX

```javascript
/*
"H2O" => {
  "H": 2,
  "O": 1
}

"C2H5OH" => {
  "C": 2,
  "H": 6,
  "O": 1
}

"COOH" => {"C": 1, "O": 2, "H": 1}

// “Mg2C2H” => {“Mg”: 2, “C”: 2, “H”: 1}
*/

// Iterate through each element till end of string
// 1. find char (isNaN) to store to parameter
// 2. couter 1 to this char
// 3. if the next char is number, store to prevValue and combine to string;
// 4. if loop to next char, store previous value to previous element
// 5. loop to end
function stringToObject(input) {
  let temp = {};
  let prev = "";
  let val = "";
  for (let i = 0; i < input.length; i++) {
    const curr = input[i];
    if (isNaN(curr)) {
      //is char
      temp[curr] = temp[curr] ? Number(temp[curr]) + 1 : 1;
      prev = curr;
      val = "";
    } else {
      //is number
      if (isNaN(val)) {
        val = curr;
      } else {
        val += curr;
      }
      temp[prev] = Number(val);
    }
  }
  return temp;
}

console.log(stringToObject("H2O"));
console.log(stringToObject("C2H5OH"));
console.log(stringToObject("COOH"));
console.log(stringToObject("C11O10OH"));
// console.log(stringToObject("Mg2C2H"));
```
