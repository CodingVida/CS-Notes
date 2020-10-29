### 解题可选思路

* 暴力
* 双指针
* 快慢指针
* 递归
* DFS/BFS
* 动态规划：自下而上、自上而下



### 位运算

* 左移 >>
* 右移 <<
* 与 & 
* 或 | 
* 异或 ^，不同取1（相当于不进位加法）

> 使用位运算进行加法操作：
>
> ```javascript
> function sum (a, b) {
>     if (a === 0) return b;
>     if (b === 0) return a;
>     
>     const newA = a ^ b;
>     const newB = (a & b) << 1;
>     return sum(newA, newB);
> }
> ```
>
> 

### 排序

> 排序中使用的方法定义如下：
>
> ```JavaScript
> function swap (arr, i, j) {
>     const temp = arr[i];
>     arr[i] = arr[j];
>     arr[j] = temp;
> }
> ```



#### 冒泡排序

不断比较相邻元素，将大的元素移动到右边，从而达到升序排序的目的。

```JavaScript
function bubbleSort (arr) {
    if (!arr) {
        return;
    }
    for (let i = 0; i < arr.length - 1; i++) {
        for (let j = 0; j < arr.length - i; j++) {
            if (arr[j] < arr[j - 1]) {
                swap(arr, j, j - 1);
            }
        }
    }
    return arr;
}

```

**复杂度**：n -1 + n -2 + ... + 1  => O(n * n)

#### 选择排序

首先，找到数组中最小的元素，其次与数组的第一个元素交换（可能会同自己交换）；然后在剩下的元素中找到最小的元素，与第二个元素交换；如此反复直到将数组排序完成。

````javascript
function selectionSort (arr) {
    if (!arr) {
        return arr;
    }

    for (let i = 0; i < arr.length - 1; i++) {
        let min = i;
        for (let j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[min]) {
                min = j;
            }
        }
        swap(arr, i, min);
    }
    
    return arr;
}
````

**复杂度**：n -1 + n -2 + ... + 1  => O(n * n)



#### 插入排序

插入排序模拟了桥牌整理的过程，它将每一个新来的数插入到已经有序的数组中的适当位置。算法的起点是认为第一个数是有序的。

```JavaScript
function insertionSort (arr) {
    if (!arr) {
        return;
    }
    for (let i = 1; i < arr.length; i++) {
        for (let j = i; j > 0 && arr[j] < arr[j - 1]; j--) {
            swap(arr, j, j - 1);
        }
    }
    return arr;
}
```

**复杂度**：1 + 2 + .... + n -1  => O(n * n)



#### 归并排序

将两个有序的数组合并为一个更大的有序的数组。

```JavaScript
function merge(arr, left, mid, right) {
    var temp = [];
    for (let k = left; k <= right; k++) {
        temp[k] = arr[k];
    }

    let i = left;
    let j = mid + 1;
    for (let k = left; k <= right; k++) {
        if (i > mid) {
            arr[k] = temp[j++];
        } else if (j > right) {
            arr[k] = temp[i++]; 
        } else if (arr[i] < arr[j]) {
            arr[k] = temp[i++];
        } else {
            arr[k] = temp[j++];
        }
    }
}

function mergeSort (arr, left, right) {
    if (left >= right) return;

    const mid = (left + right) >> 1;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
    return arr;
}
```

**复杂度**：O(n * lgn)。

明显的缺点是需要一个辅助空间，O(n)。



#### 快速排序

快排需要先选取一个基准值，将比它小的放在基准值左边，大的放右边；再以基准值为界，拆分左右两个部分，重复以上操作。

```JavaScript
function partition (arr, left, right) {
    let i = left;
    let j = right + 1;
    let pivot = arr[i];

    while (true) {
        while (arr[++i] < pivot) {
            if (i === right) break;
        }
        while (arr[--j] > pivot) {
            if (j === left) break;
        }
        if (i >= j) break;
        swap(arr, i, j);
    }
    swap(arr, left, j);
    return j;
}

function quickSort (arr, left, right) {
    if (left >= right) {
        return;
    }

    let pivotIndex = partition(arr, left, right);
    quickSort(arr, left, pivotIndex);
    quickSort(arr, pivotIndex + 1, right);
}
```

**复杂度**：O(n * lgn)。



#### 堆排序







