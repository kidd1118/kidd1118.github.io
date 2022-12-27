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