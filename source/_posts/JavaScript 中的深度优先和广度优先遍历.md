---
title: JavaScript 中的深度优先和广度优先遍历
tags: [Javascript,Algorithm]
category: Algorithm
date: 2020-3-24 20:09
---
# JavaScript 中的深度优先和广度优先遍历
## 前言
深度优先（Deepth First）和广度优先(Breadth First)都是一种用于遍历或者搜索树/图的常用算法，今天做题的时候用到了，打算好好地总结一下。


下面通过两种方法来遍历如下的 DOM 结构（不要在意标签名）。
![DOM](https://static.gongfangwen.com/2020-03-24-DOM.png)


## 深度优先
深度优先，顾名思义，一条路走到底，如果走到头，便回溯到上一步的状态，继续一条路走到底，直到全部走完。
对于上面例子来说，深度优先的走法如下：
![深度优先](https://static.gongfangwen.com/2020-03-24-%E6%B7%B1%E5%BA%A6%E4%BC%98%E5%85%88.png)

代码实现如下：
```javascript
function deepFirstSearch(node, list = []) {
  list.push(node);
  const { children } = node;
  for (let i = 0; i < children.length; i++) {
    deepFirstSearch(children[i], list);
  }
  return list;
}

console.log(deepFirstSearch(document.querySelector("ul"))); // [ul, li, i, a, span, li, b, li]
```

这种方法是递归实现的，我们还可以使用非递归：
```javascript
function deepFirstSearch(node) {
  const list = [],
    stack = [];
  if (node) {
    stack.push(node);
    while (stack.length) {
      const item = stack.pop();
      list.push(item);
      for (let i = item.children.length - 1; i >= 0; i--) {
        stack.push(item.children[i]);
      }
    }
  }
  return list;
}
console.log(deepFirstSearch(document.querySelector("ul"))); // [ul, li, i, a, span, li, b, li]
```

## 广度优先
广度优先是按一层层来遍历的，每一层搜索完成之后才会到下一层搜索。
对于之前的例子，广度优先走法如下：
![广度优先](https://static.gongfangwen.com/2020-03-24-%E5%B9%BF%E5%BA%A6%E4%BC%98%E5%85%88.png)

主要利用了队列先进先出的思想，遇到有子节点的就将子节点列表`push`进数组，同时使用`shift`来拿数组中的元素，主要代码实现如下：
```javascript
function breadthFirstSearch(node) {
  const list = [];
  if (node) {
    const queue = [];
    queue.unshift(node);
    while (queue.length) {
      const item = queue.shift();
      list.push(item);
      for (let i = 0; i < item.children.length; i++) {
        queue.push(item.children[i]);
      }
    }
  }
  return list;
}
console.log(breadthFirstSearch(document.querySelector("ul"))); //  [ul, li, li, li, i, a, b, span]
```

## 例题
### Q 1
> 给出一棵二叉树，其上每个结点的值都是 0 或 1 。每一条从根到叶的路径都代表一个从最高有效 位开始的二进制数。例如，如果路径为 0 -> 1 -> 1 -> 0 -> 1，那么它表示二进制数 01101，也就是 13 。
> 
> 对树上的每一片叶子，我们都要找出从根到该叶子的路径所表示的数字。
> 以 10^9 + 7 为模，返回这些数字之和。
> 示例：
> ![sum-of-root-to-leaf-binary-numbers](https://static.gongfangwen.com/2020-03-24-sum-of-root-to-leaf-binary-numbers.png)
> ```
> 输入：[1,0,1,0,1,0,1]
输出：22
解释：(100) + (101) + (110) + (111) = 4 + 5 + 6 + 7 = 22
> ```


这道题和上面的类似，只不过变成了加法运算，那就可以把`DFS`上的`list`改成`sum`即可，同时高位加上地位可以是用位运算。
代码如下：
```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
var sumRootToLeaf = function (root) {
  let sum = 0;
  function dfs(tree, value = 0) {
    if (!tree) return;
    value = (value << 1) | tree.val;
    if (!tree.left && !tree.right) sum += value;
    dfs(tree.left, value);
    dfs(tree.right, value);
  }
  dfs(root);

  return sum;
};
```

### Q 2
> 给定一个二叉树，检查它是否是镜像对称的。

> 例如，二叉树 [1,2,2,3,4,4,3] 是对称的。
> ```
>     1
>    / \
>   2   2
>  / \ / \
> 3  4 4  3
> ```
> 但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:
> ```
>     1
>    / \
>   2   2
>    \   \
>    3    3
> ```

如果一个二叉树是镜像对称，那么：
- 可能他的父节点为 null
- 如果父节点不为空，那么递归判断父节点的左子树和右子数

可以给出如下代码：
```javascript
var isSymmetric = function (root) {
  function isEqual(left, right) {
    if (left === null && right === null) return true;
    if (left === null || right === null) return false;
    return (
      left.val === right.val &&
      isEqual(left.left, right.right) &&
      isEqual(left.right, right.left)
    );
  }
  if (root === null) return true;
  return isEqual(root.left, root.right);
};
```

此题还可以使用 BFS ，构建一个队列，每次推出最前的两个，通过这两个的对比来确定是否镜像对称：
```javascript
var isSymmetric = function (root) {
  const queue = [root, root];
  while (queue.length) {
    const left = queue.shift();
    const right = queue.shift();
    // 先推出，后判断
    if (left === null && right === null) continue;
    if (left === null || right === null) return false;
    if (left.val !== right.val) return false;
    queue.push(left.left);
    queue.push(right.right);
    queue.push(left.right);
    queue.push(right.left);
  }
  return true;
};
```

## 后记
算法这个东西，融会贯通确实很难，也许今天会了这题，没有领会到其精华思想，过两天可能连做过的题都忘记了，得加把劲了。

## 参考
- [深度优先搜索](https://zh.wikipedia.org/zh-hans/%E6%B7%B1%E5%BA%A6%E4%BC%98%E5%85%88%E6%90%9C%E7%B4%A2)
- [广度优先搜索](https://zh.wikipedia.org/wiki/%E5%B9%BF%E5%BA%A6%E4%BC%98%E5%85%88%E6%90%9C%E7%B4%A2)
- [1022. 从根到叶的二进制数之和](https://leetcode-cn.com/problems/sum-of-root-to-leaf-binary-numbers/)
- [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)