### JS数组常考算法详解



####数组方法概述

* concat 返回新数组，两边的原始数组都不会变化
* slice 返回新数组，从开始到结束（不包括结束）选择的数组的一部分浅拷贝到一个新数组。且原始数组不会被修改。



#### 两个升序数组合并成一个升序数组

// 复杂度 O(M+N) 时间 O(M+N) 空间

```Js
function merge(left, right){
    Let result  = [],
        il      = 0,
        ir      = 0;

    while (il < left.length && ir < right.length) {
        result.push(left[il] < right[ir] ? left[il++] : right[ir++]);
    }

    return result.concat(left.slice(il)).concat(right.slice(ir));
}
```



// 复杂度 O(M+N) 时间 O(1) 空间

```Js
public class Solution {
    // m, n 是数组长度
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1, j = n - 1, writeIdx = m + n - 1;
        while (i >= 0 && j >= 0)
            nums1[writeIdx--] = nums1[i] > nums2[j]? nums1[i--] : nums2[j--];
        while (j >= 0)
            nums1[writeIdx--] = nums2[j--];
    }
}
```



#### 数组去重

```Js
let arr = [1,2,1,2,3,5,4,5,3,4,4,4,4];

let result = arr.sort().reduce((init, current) => {

    if(init.length===0 || init[init.length-1]!==current){
        init.push(current);
    }

    return init;

}, []);

console.log(result); //[1,2,3,4,5]
```



#### 两个数组的交集 II

给定两个数组，写一个方法来计算它们的交集。

例如:

给定 nums1 = [1, 2, 2, 1], nums2 = [2, 2], 返回 [2, 2].

注意：

-    输出结果中每个元素出现的次数，应与元素在两个数组中出现的次数一致。
-    我们可以不考虑输出结果的顺序。

跟进:

- 如果给定的数组已经排好序呢？你将如何优化你的算法？
- 如果 nums1 的大小比 nums2 小很多，哪种方法更优？
- 如果nums2的元素存储在磁盘上，内存是有限的，你不能一次加载所有的元素到内存中，你该怎么办？

解法：

```
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[]}
 */
var intersect = function(nums1, nums2) {
    
    var map1 = new Map();
    var number = [];
    
    for(var i = 0; i < nums1.length; i++) {
        var map1Value = map1.get(nums1[i]);
        map1.set( nums1[i], ( map1Value ? map1Value : 0 ) + 1 );
    }
    
    for(var i = 0; i < nums2.length; i++) {
        if( map1.has(nums2[i]) && map1.get(nums2[i]) != 0 ) {
            number.push(nums2[i]);
            map1.set( nums2[i], map1.get(nums2[i]) - 1 );
        }
    }
    
    return number;
};
```



#### 从排序数组中删除重复项

```Js
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function(nums) {
    
    if(!nums || nums.length == 0) return 0;
    
    let len = 0;
    for(let i = 1; i < nums.length; i++) {
        if (nums[len] != nums[i]) {
            nums[++ len] = nums[i];
        }
    }
    return len + 1;
};
```



#### 只出现一次的数字

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素

```
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
    
    let number = 0;
    for(let i = 0; i < nums.length; i++) {
        number ^= nums[i];
    }
    return number;
};
```

参考资料：<https://blog.csdn.net/qq_35546040/article/details/80284079> 



#### 两数之和

给定一个整数数组和一个目标值，找出数组中和为目标值的两个数。

你可以假设每个输入只对应一种答案，且同样的元素不能被重复利用。

示例：

```Js
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```



解法：

```Js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {

    var map = new Map();
    var number = [];
    
    for(var i = 0; i < nums.length; i ++) {
        
        if(map.has(target - nums[i])) {
           number.push(i, map.get(target - nums[i]));
            break;
        }
        map.set(nums[i], i);
    }
    return number;
};
```

> 参考资料：<http://www.cnblogs.com/grandyang/p/4130379.html>



#### 旋转数组

给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

示例 1:

```Js
输入: [1,2,3,4,5,6,7] 和 k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右旋转 1 步: [7,1,2,3,4,5,6]
向右旋转 2 步: [6,7,1,2,3,4,5]
向右旋转 3 步: [5,6,7,1,2,3,4]
```



示例 2:

```Js
输入: [-1,-100,3,99] 和 k = 2
输出: [3,99,-1,-100]
解释: 
向右旋转 1 步: [99,-1,-100,3]
向右旋转 2 步: [3,99,-1,-100]
```



说明:

- 尽可能想出更多的解决方案，至少有三种不同的方法可以解决这个问题。
- 要求使用空间复杂度为 O(1) 的原地算法。

解法：

```Js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function(nums, k) {
    
    var k = k % nums.length;
    reverse(nums, 0, nums.length - 1);
    reverse(nums, k, nums.length - 1);
    reverse(nums, 0, k - 1);
};

var reverse = function(nums, i, j) {
    
    while(i < j) {
       swap(nums, i++, j--);
    }
}

var swap = function(nums, i, j) {
    var temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

> 参考资料：<https://segmentfault.com/a/1190000003705446>



#### 加一

给定一个非负整数组成的非空数组，在该数的基础上加一，返回一个新的数组。

最高位数字存放在数组的首位， 数组中每个元素只存储一个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

示例 1:

```Js
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。
```



示例 2:

```Js
输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。
```



解法： 

```Js
/**
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function(digits) {
    
    if(digits.length == 0) return digits;
    var carry = 1;
    var res = [1];
    
    for(var i = digits.length - 1; i >= 0; i--) {
        
        if(carry == 0) return digits;
        var sum = carry + digits[i];
        digits[i] = sum % 10;
        carry = parseInt(sum / 10);
    }
    
    for(let i = digits.length - 1; i >= 0; i--) {
        res.push(0);
    }
    
    return carry == 0 ? digits : res;
    
};
```

> 参考资料：<http://www.cnblogs.com/grandyang/p/4079357.html> 



#### 存在重复

给定一个整数数组，判断是否存在重复元素。

如果任何值在数组中出现至少两次，函数返回 true。如果数组中每个元素都不相同，则返回 false。

示例 1:

```Js
输入: [1,2,3,1]
输出: true
```



示例 2:

```Js
输入: [1,2,3,4]
输出: false
```



示例 3:

```Js
输入: [1,1,1,3,3,4,3,2,4,2]
输出: true
```



解法 :

```Js
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var containsDuplicate = function(nums) {
    
    let hashMap = new Map();
    for(let i = 0; i < nums.length; i++) {
        
        if( hashMap.has(nums[i]) ) {
           return true;
        }
        
        hashMap.set(nums[i], 1);
    }
    
    return false;
};
```

