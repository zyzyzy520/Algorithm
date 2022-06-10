# Nsum问题

## 1. 2 sum[两数之和](https://leetcode-cn.com/problems/two-sum/)

- 声明一个hash表。然后遍历数组，针对每个数nums[i]
- 如果target - nums[i]`不在`hash表中，将`{target-nums[i] : i}`存于hash表中，方便后面的数查询
- 如果target - nums[i]`在`hash表中，读出hash[target-nums[i]]，返回找到的索引[hash[target - nums[i]], i]

``` javascript
var twoSum = function(nums, target) {
    let hash = new Map();  //存储出现过的字符和位置
    for(let i = 0; i < nums.length; i++){
        let cur = nums[i];
        if(hash[target - cur] != undefined) return [hash[target - cur], i];
        else hash[cur] = i;
    }
};
```

## [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

- `复用求两数之和的代码`，将求两数之和的代码封装成函数
- 首先对数组进行排序，用一个数组ans存储满足要求的三个数

- 遍历数组
  - 如果`该数与前面的数相等就跳过`，因为结果已经在前面那个数就计算过了
  - `调用求两数之和的函数(nums, i + 1, target - nums[i])`
  - `两数之和的目标是target-nums[i]。且是从i后面开始找两个数`
- 求两数之和的函数(target, start, nums)
  - left = start, right = nums.length - 1;
  - whil(left < right)
    - leftVal = nums[left], rightVal = nums[right]
    - 如果leftVal + rightVal = target，将[nums[start], leftVal, rightVal]压入ans数组中。然后移动左右指针，注意要`跳过重复元素`
    - 如果leftVal + rightVal > target，移动右指针，注意要`跳过重复元素`
    - 如果leftVal + rightVal < target，移动左指针，注意要`跳过重复元素`
- 在遍历数组时就做了防重复工作，同时在求两数之和里也做了防重复工作，所以没有重复元素
- 时间复杂度：在for循环里调用求twosum的函数O(n*n)  = O(n^2)
- 重点：`getTwoSum`里`left = start`

``` javascript
var threeSum = function(nums) {
    nums = nums.sort(function(a,b){
        if(a - b > 0) return 1;
        else if(a - b < 0) return -1;
        else return 0;
    }
    let ans = [];
    // [-4, -1, -1, 0, 1, 2, 2]
    function getTwoSum(nums, index, target){
            let left = index, right = nums.length - 1;
    //小于等于会压入同样的元素。
    while(left < right){
        let leftVal = nums[left], rightVal = nums[right];
        if(leftVal + rightVal == target){
            ans.push([0 - target, leftVal, rightVal]);
            // 可能存在多种满足条件的情况，所以不能直接跳出循环，例如[-1,2],[0,1]
            while(nums[left] == leftVal)left++;
            while(nums[right] == rightVal) right--;
        }
        else if(leftVal+ rightVal > target){
            //跳过重复元素
            while(nums[right] == rightVal) right--;
        }
        else{
            while(nums[left] == leftVal)left++; 
        }
    }

    }
    for(let i = 0; i < nums.length; i++){
        // 因为nums数组排序后，后面的数肯nums[i]大，如果nums[i]都大于0，肯定找不到三个数等于0。同时因为i之后的数也是大于0的，所以直接跳过
        // 在[-2,-1,0,2]中，对于2来说也许存在-2，0能够使得三数之和等于0，但这一种情况在-2时就已经得到了，2只能向后看，避免重复
        if(nums[i] > 0) break;
        // 例如[-1,-1,0,1]，如果不跳过就会从出现重复[-1,0,1]。无需担心漏掉，因为在前一个相同数已经全部考虑到了
        if(i - 1 >= 0 && nums[i] == nums[i - 1]) continue;
        let cur = nums[i];
        //找到后面两个和为b + c  = -a
        getTwoSum(nums, i + 1, 0 -cur);
    }
    return (ans);
};
```

## [18. 四数之和](https://leetcode-cn.com/problems/4sum/)

- 总体思路：穷举第一个数字，对于剩下的三个数字，调用threeSum函数
- 首先对数组进行排序，用一个数组ans存储满足要求的四个数
- 遍历数组
  - 如果`该数与前面的数相等就跳过`，因为结果已经在前面那个数就计算过了
  - `调用求三数之和的函数(target - nums[i], i + 1, nums[i])`
  - `两数之和的目标是target-nums[i]。且是从i后面开始找两个数。将nums[i]传递下去方便压入ans中`
- 求三数之和的函数(target, start, firstNum)
  - 从`start开始遍历数组`，nums[i]
  - 如果`该数与前面的数相等就跳过`，因为前面那个相同的数已经作为第二个满足条件的数就计算过了（从start+1开始）
  - `调用求两数之和的函数(target - nums[i], i + 1, firstNum, nums[i])`
  - `两数之和的目标是target-nums[i]。且是从i后面开始找两个数。将nums[i]和firstNum传递下去方便压入ans中`
- 求两数之和的函数(target, start, firstNum, secondNum)
  - left = start, right = nums.length - 1;
  - whil(left < right)
    - leftVal = nums[left], rightVal = nums[right]
    - 如果leftVal + rightVal = target，将[firstNum, secondNum, leftVal, rightVal]压入ans数组中。然后移动左右指针，注意要`跳过重复元素`
    - 如果leftVal + rightVal > target，移动右指针，注意要`跳过重复元素`
    - 如果leftVal + rightVal < target，移动左指针，注意要`跳过重复元素`
  - 时间复杂度，三重for循环O(n^3)
  - 重点：`getThreeSum`里`遍历从start开始`，并`从start+1开始判重`，因为start是第一个可能性

``` javascript
var fourSum = function(nums, target) {
    nums = nums.sort(function(a ,b){
        if(a - b > 0) return 1;
        else if(a - b < 0) return -1;
        else return 0;
    })
    let ans = [];
    for(let i = 0; i < nums.length; i++){
        //跳过重复的数，在第一个数字时就已经得到答案了
        if(i - 1 >= 0 && nums[i] == nums[i - 1] ) continue;
        //调用求三数之和的函数，目标值是target-nums[i]
        getThreeSum(nums, i + 1, target - nums[i], nums[i]);
    }
    return (ans);
    function getThreeSum(nums, start, target, firstNum){
        for(let i = start; i < nums.length; i++){
            // 跳过重复的数，在该数第一次出现时，就讨论过了.出现重复数字，跳过。一定要是start之后的，否则对于[2,2,2,2]。会跳过
            if(i - 1 >= start && nums[i] == nums[i - 1]) continue;
            getTwoSum(nums, i + 1, target - nums[i], firstNum, nums[i]);
        }
    }
    function getTwoSum(nums, start, target, firstNum, secondNum){
        // console.log(targetBefore, target);
        let left = start, right = nums.length -1;
        while(left < right){
            let leftVal = nums[left], rightVal = nums[right];
            if(leftVal + rightVal == target){
                ans.push([firstNum, secondNum, leftVal, rightVal]);
                while(nums[right] == rightVal) right--;
                while(nums[left] == leftVal) left++;
            } else if(leftVal + rightVal > target){
                // 移动右指针
                while(rightVal == nums[right]) right--;
            } else{
                // 移动左指针
                while(leftVal == nums[left]) left++;
            }
        }
    }
};
```

