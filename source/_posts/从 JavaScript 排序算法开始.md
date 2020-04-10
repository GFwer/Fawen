---
title: 从 JavaScript 排序算法开始
tags: [Javascript,Algorithm]
category: Algorithm
date: 2020-3-20 23:08
---
# 从 JavaScript 排序算法开始

## 前言
算法一直都是我不太愿意面对的内容，不过最近还是希望了解基本的排序算法和动态规划、洗牌算法等，对实际生产相比也有一定帮助，那么事不宜迟，马上开始！
## 交换
考虑到可能有比较多的交换操作，所以先把交换函数进行封装：
```javascript
function swap(array, index1, index2) {
  [array[index1], array[index2]] = [array[index2], array[index1]];
}
```

## 冒泡排序
冒泡排序是一种简单的排序算法，通过不断比较相邻元素，将大的数不断往后交换，直到完成排序。
具体步骤如下：
1. 比较第一个和第二个元素，如果第一个较大，则交换两个元素
2. 比较第二个和第三个元素，如果第二个较大，则交换两个元素
3. .....
4. 比较第 n 个和第 n + 1 个元素，如果第 n 个较大，则交换两个元素
5. 针对所有元素重复以上步骤，直到完成排序

根据这个描述可以写出如下代码：
```javascript
function bubbleSort(arr) {
  const size = arr.length;
  // 第一次循环全部，第二次由于最后一个数已经不需要比较了只需要循环 size - 1 次
  for (let i = 0; i < size - 1; i++) {
    for (let j = 0; j < size - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) swap(arr, j, j + 1);
    }
  }
  return arr
}
console.log(bubbleSort([5,4,25,1,3])); // [1, 3, 4, 5, 25]
```

### 优化方案一
设置一个标志变量`position`，它代表上一次冒泡排序中最后一次进行交换的位置，如果后面都不需要进行交换，证明`position`之后无需比较，所以扫描只需要到`position`位置即可，如果`position == 0`，那么证明排序已经完成。
代码如下：
```javascript
function bubbleSort2(arr) {
  let i = arr.length - 1;
  while (i > 0) {
    let position = 0;
    for (var j = 0; j < i; j++) {
      if (arr[j] > arr[j + 1]) {
        swap(arr, j, j + 1);
        // 每次都记录交换位置
        position = j;
      }
    }
    // 如果 position == 0 则退出循环，不等于零则遍历至 position 位置即可
    i = position;
  }
  return arr
}
console.log(bubbleSort2([5,4,25,1,3])); // [1, 3, 4, 5, 25]
``` 

### 优化方案二
冒泡排序每一次循环至找出一个最大值，那么如果每次排序的时候分别进行一次正向排序和一次反向排序可以得到一个最大值和最小值，从而使次数差不多减少了一半；
代码实现如下：
```javascript
function bubbleSort3(arr) {
  let low = 0,
    high = arr.length - 1;
  while (low < high) {
    // 从最低开始遍历，找出最大值
    for (let i = low; i < high; i++) {
      if (arr[i] > arr[i + 1]) swap(arr, i, i + 1);
    }
    high--; // 最大值找出之后，遍历范围 -1
    // 从最高开始遍历，找出最小值
    for (let i = high; i > low; i--) {
      if (arr[i] < arr[i - 1]) swap(arr, i, i - 1);
    }
    low++; // 最小值找出之后，遍历范围 -1
  }
  return arr;
}
console.log(bubbleSort3([5,4,25,1,3])); // [1, 3, 4, 5, 25]
```

### 时间复杂度
冒泡排序逻辑比较简单，时间复杂度如下：
- 最佳情况，输入是正序的数组或即将完成排序的数组，时间复杂度为<code>O(n)</code>
- 最差情况，输入是反序的数组，时间复杂度为<code>O(n^2)</code>

## 选择排序
选择排序，顾名思义，就是不断选择未排序数中的小数，然后和未排序数中第一个进行交换。具体步骤如下：
- 找到数组中最小的数，和第一个元素交换；
- 在剩下的数中找出最小的数，和第二个元素交换；
- ......
- 直到最后一个数为止，排序完成。

根据这个我们可以写出代码：
```javascript
function selectionSort(arr) {
  let minIndex,
    size = arr.length;
  // 这里只需要循环 size - 1 次，最后一次只有一个数不用循环
  for (let i = 0; i < size - 1; i++) {
    minIndex = arr[i]; // 第 i + 1 次循环，从 i 开始
    // 找出剩余数组中最小数的索引
    for (let j = i + 1; j < size; j++) {
      if (arr[j] < arr[minIndex]) minIndex = j;
    }
    if (i !== minIndex) swap(arr, i, minIndex);
  }
  return arr;
}
console.log(selectionSort([5, 4, 25, 1, 3])); // [1, 3, 4, 5, 25]
```

### 时间复杂度
时间复杂度非常稳定，就是<code>O(n^2)</code>。

## 插入排序
插入排序是的原理是构建有序数列，假定最开始的元素是有序数列，把有序数列从后往前对比之后每一个数，将数插入至有序数列中，具体步骤如下：
1. 认为第一个元素已经排好序（有序数列）；
2. 取出第二个元素，对比第一个元素，若小于第一个元素，则将第二个元素移至第一个元素前；
3. 取出第三个元素，分别从后往前对比第二个与第一个元素，将其插入其中合适的位置；
4. 取出第 n 个元素，对比长度为 n - 1 的有序数组，将其插入到合适的位置；
5. 重复直到排序完成。

给出代码实现：
```javascript
function insertionSort(arr) {
  // 认为第一个元素是有序的，从第二个元素开始循环
  for (let i = 1; i < arr.length; i++) {
    const current = arr[i];
    let preIndex = i - 1; // 有序数组从后往前对比
    // 如果之前的元素比当前元素大，那么就把之前的元素向后移，直到之前元素比当前元素小
    while (arr[preIndex] > current) {
      arr[preIndex + 1] = arr[preIndex];
      preIndex--;
    }
    // 由于这里拿到的 preIndex 是减 1 的，所以 current 赋给索引为 preIndex + 1 的元素
    arr[preIndex + 1] = current; 
  }
  return arr;
}
console.log(insertionSort([5, 4, 25, 1, 3])); // [1, 3, 4, 5, 25]
```

### 优化插入排序
上面的步骤对于有序数列还是从后往前一个个对比，那么可以再这个查找过程使用二分法并根据此优化插入排序：
```javascript
// 查找 value 所应该对应的索引
function binarySearchIndex(arr, currentPreIndex, value) {
  let min = 0,
    max = currentPreIndex;
  while (min <= max) {
    const mid = ~~((min + max) / 2);
    if (arr[mid] < value) min = mid + 1;
    else max = mid - 1;
  }
  return min;
}

function insertionSort2(arr) {
  for (let i = 1; i < arr.length; i++) {
    const current = arr[i];
    // 找到当前值所需要插入的索引
    const insertIndex = binarySearchIndex(arr, i - 1, current);
    // 至那个索引开始，直到当前索引之前一个元素，每个元素向后移一位
    for (let j = i - 1; j >= insertIndex; j--) {
      arr[j + 1] = arr[j];
    }
    arr[insertIndex] = current;
  }
  return arr;
}
console.log(insertionSort2([5, 4, 25, 1, 3])); // [1, 3, 4, 5, 25]
```

### 时间复杂度
插入排序的时间复杂度如下：
- 最好的情况，数组按升序排列，时间复杂度为<code>O(n)</code>
- 最坏的情况，数组按降序排列，时间复杂度为<code>O(n^2)</code>

## 希尔排序
希尔排序是改进版的插入排序，根据需要设定好一个间隔`gap`，利用插入排序在几乎已经排好序的数据操作时比较快的特点，它的基本思想是先将整个需要排序的数据分成若干段，先对每一段进行插入排序，再对全体进行插入排序。基本步骤如下：
1. 将数组分成`gap`组（建议`gap = size >> 1`，即`Math.floor(size / 2)`），以长度为 10 的数组为例；
2. `gap = 10 >> 1 = 5`，数据分成 5 组，第一个元素和第六个元素比较，第二和第七个元素进行比较....第五和第十个元素进行比较，小的就交换数据，完成第一次插入排序；
3. `gap = 5 >> 1 = 2`，数组分成 2 组，下标分别为`[0,2,4,6,8]`和`[1,3,5,7,9]`，再进行插入排序；
4. `gap = 2 >> 1 = 1`，数组分成 1 组，统一进行一次插入排序。

代码如下所示：
```javascript
function shellSort(arr) {
  const size = arr.length;
  let gap = size >> 1;
  while (gap > 0) {
    for (let i = gap; i < size; i++) {
      const current = arr[i];
      let preIndex = i - gap;
      // 与插入排序类似，不过这里不是以 1 为单位，而是以 gap 为单位
      while (arr[preIndex] > current) {
        arr[preIndex + gap] = arr[preIndex];
        preIndex -= gap;
      }
      arr[preIndex + gap] = current;
    }
    gap = gap >> 1;
  }
  return arr
}
console.log(shellSort([5, 4, 25, 1, 3])); // [1, 3, 4, 5, 25]
```

## 归并排序
归并排序采用了分治思想，将数据分成若干份，先使子序列有序，再合并得到完整的有序数据。其基本步骤如下：
1. 把长度为 n 的数组分成两个长度为 n/2 的子数组；
2. 把两个子数组进行归并排序（递归）；
3. 申请一个新数组；
4. 从头开始比较两个数组第一个元素，将较小的元素推出并进入新数组；
5. 重复步骤 4，直到两个数组都为空。

代码描述如下：
```javascript
function mergeSort(arr) {
  const size = arr.length;
  if (size < 2) return arr;
  const mid = size >> 1,
    left = arr.slice(0, mid),
    right = arr.slice(mid);
  // 递归调用
  return merge(mergeSort(left), mergeSort(right));
}
function merge(left, right) {
  const result = [];
  while (left.length && right.length) {
    // 小者入数组
    result.push(left[0] <= right[0] ? left.shift() : right.shift());
  }
  // 若两个数组长度不等，则一个数组还有一项
  return result.concat(left, right);
}
console.log(mergeSort([5, 4, 25, 1, 3])); // [1, 3, 4, 5, 25]
```

## 快速排序
快速排序这个名字听起来就简单暴力，它同样使用分治思想，通过设置基准值来将数组中的值做对比，从而把数组置成有序状态。具体步骤如下：
1. 从数组挑选一个元素，称为基准；
2. 重新排列数组，把数组中比基准小的都放在左边，比基准大的放在右边（相同的两边都行），得到了两个有序数列
3. 递归地对子数组使用快速排序；

代码实现如下：
```javascript
function quickSort(arr) {
  const pivot = arr[0],
    left = [],
    right = [],
    size = arr.length;
  if (size < 2) return arr;
  for (let i = 1; i < size; i++) {
    if (arr[i] < pivot) left.push(arr[i]);
    else right.push(arr[i]);
  }
  return quickSort(left).concat(pivot, quickSort(right));
}
console.log(quickSort([5, 4, 25, 1, 3])); // [1, 3, 4, 5, 25]
```

## 堆排序
堆排序是利用堆这种数据结构对选择排序的改进。
> 堆积是一个近似完全二叉树（除了最后一层其它层都达到最大节点数，且最后一层节点都靠左排列）的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。
> 一个堆是可以用从左到右，从上到下的子节点组成一个数组。
> 对于任意一个索引为 i 的节点，它的父节点索引为`(i - 1)/2`，左子节点为`2i + 1`，右子节点为`2i + 2`
![最大堆和最小堆](https://static.gongfangwen.com/2020-03-20-15847129448199.jpg)

其具体步骤如下：
1. 将输入数组创建一个堆；
2. 调整堆，使其成为一个最大堆；
3. 堆排序，将最大值交换至有序区，重新调整堆，使其成为一个最大堆
4. 重复 3。


![堆排序示例](https://static.gongfangwen.com/2020-03-20-20190108165241107.gif)

代码实现如下：
```javascript
function heapSort(arr) {
  let size = arr.length;
  // 从最后一个节点的 parent 节点开始做 heapify 创建最大堆，创建完之后堆顶层的元素是最大值
  for (let i = (size >> 1) - 1; i >= 0; i--) {
    heapify(arr, i, size);
  }
  // 堆排序，顶层的数和最后的数做交换，同时 size -- 排除顶层数据，交换完成之后用新的数据重复流程
  for (let i = size - 1; i > 0; i--) {
    swap(arr, 0, i);
    size--;
    heapify(arr, 0, size);
  }
  return arr;
}

// 调整堆，使其达到最大堆
function heapify(arr, index, size) {
  // 父节点的索引值
  let largest = index;
  // 父节点 i 的左子节点在位置 2i + 1
  let left = 2 * index + 1;
  // 父节点 i 的右子节点在位置 2i + 2
  let right = 2 * index + 2;
  // 在保证有节点的情况下，求出三个节点中的最大值所在的索引
  if (left < size && arr[left] > arr[largest]) {
    largest = left;
  }
  if (right < size && arr[right] > arr[largest]) {
    largest = right;
  }
  // 如果最大值所在的索引不等于当前父节点的索引，就需要交换父节点和子节点数据，对其子节点继续做 heapify
  if (largest !== index) {
    swap(arr, index, largest);
    heapify(arr, largest, size);
  }
}
console.log(heapSort([5, 4, 25, 1, 3])); // [1, 3, 4, 5, 25]
```


## 参考
本文大量抄袭了以下文章/视频：
- [十大经典排序算法总结（JavaScript描述）](https://juejin.im/post/57dcd394a22b9d00610c5ec8#heading-11)
- [优雅的 JavaScript 排序算法（ES6）](https://www.rayjune.me/2018/03/22/elegant-javascript-sorting-algorithm-es6/)
- [堆排序(Heapsort)](https://www.youtube.com/watch?v=j-DqQcNPGbE)
- [数据结构示例——堆排序过程](https://blog.csdn.net/sxhelijian/article/details/50295637)
- [十大排序算法(一)-堆排序](https://narcissuscyn.github.io/2019/01/23/%E5%A0%86%E6%8E%92%E5%BA%8F%E7%9A%84%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3/)
- [归并排序就这么简单](https://juejin.im/post/5ab4c7566fb9a028cb2d9126)
- [shellSort](https://en.wikipedia.org/wiki/Shellsort)