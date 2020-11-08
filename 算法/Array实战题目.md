## Array 实战题目

- https://leetcode-cn.com/problems/container-with-most-water/
- https://leetcode-cn.com/problems/move-zeroes/
- https://leetcode.com/problems/climbing-stairs/
- [https://leetcode-cn.com/problems/3sum/ ](https://leetcode-cn.com/problems/3sum/)(高频老题）

----

![image-20201030214139339](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//image-20201030214139339.png)

---

#### [283. 移动零](https://leetcode-cn.com/problems/move-zeroes/)

> 给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。
>
> 示例:
>
> 输入: [0,1,0,3,12]
> 输出: [1,3,12,0,0]
> 说明:
>
> 必须在原数组上操作，不能拷贝额外的数组。
> 尽量减少操作次数。

1.loop ，count  zeros

2.开新数组 0放后面，非0放前面，可能需要多的空间，不符合要求

3.直接在数组操作 index

```java
//双指针
class Solution {
    public void moveZeroes(int[] nums) {
      
        //index在原数组上进行操作
        int j = 0;   
        for(int i=0;i<nums.length;i++) {
            if(nums[i] != 0) {
                nums[j] = nums[i];   
                if(i != j) {
                    nums[i] = 0;
                }
                j++;
            }
        }

    }
}

//2.先把不为0的放到前面，最后补齐0
class Solution {
    public void moveZeroes(int[] nums) {
        if(nums.length < 1 || nums == null) {
            return;
        }

        int j = 0;
        for(int i=0;i<nums.length;i++) {
            if(nums[i] != 0) {
                nums[j] = nums[i];
                j++;
            }
        }

        for(;j<nums.length;j++) {
            nums[j] = 0;
        }
    }
}

//form discuss
// 双指针 遍历数组，不等于0时，交换num[j] num[i]
//最后 0都被移到最后
class Solution {
    public void moveZeroes(int[] nums) {
    int j = 0;
    for(int i = 0; i < nums.length; i++) {
        if(nums[i] != 0) {
            int temp = nums[j];
            nums[j] = nums[i];
            nums[i] = temp;
            j++;
        }
    }
    }
}
```

---

#### [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

> 假设你正在爬楼梯。需要 n 阶你才能到达楼顶。
>
> 每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
>
> 注意：给定 n 是一个正整数。
>
> 示例 1：
>
> 输入： 2
> 输出： 2
> 解释： 有两种方法可以爬到楼顶。
> 1.  1 阶 + 1 阶
> 2.  2 阶
> 示例 2：
>
> 输入： 3
> 输出： 3
> 解释： 有三种方法可以爬到楼顶。
> 1.  1 阶 + 1 阶 + 1 阶
> 2.  1 阶 + 2 阶
> 3.  2 阶 + 1 阶
>

```java
//懵逼的时候：暴力？ or 基本情况是什么？
// 找 最近 重复子问题
//计算机无非就是 if else for while  recursion

// 1:1
// 2:2
// 3:f(1) + f(2)
// 4:f(2)+f(3)
// ...
// f(n) = f(n-1)+f(n-2)  :Fibonacci

class Solution {
    public int climbStairs(int n) {
        int f1=1,f2=2,f3=3;
        if(n<=2) {
            return n;
        }
        for(int i=3;i<=n;i++) {
            f3 = f1+f2;
            f1 = f2;           //先更新f1
            f2 = f3;
        }
        return f3;
    }
}

//2.递归   超出时间限制

// class Solution {
//     public int climbStairs(int n) {
//         if(n == 1) return 1;
//         if(n == 2) return 2;
//         return climbStairs(n-1) + climbStairs(n-2);
//     }
// }
```

---

#### [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

> 给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。
>
> 说明：你不能倾斜容器，且 n 的值至少为 2。
>
>  ![img](https://gitee.com/sunnyzq/my-image-hosting-service/raw/master/img//question_11.jpg)
>
> 
>
> 图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
>
> 示例：
>
> 输入：[1,8,6,2,5,4,8,3,7]
> 输出：49

```java
//1.枚举，left bar x;right bar y;  (y-x)*height_diff
//O(n^2)

// class Solution {
//     public int maxArea(int[] height) {
//         int max = 0;
//         //用i,j遍历数组,且ij下表不会重复的写法！！
//         for(int i = 0;i < height.length - 1;++i) {
//             for(int j = i + 1; j < height.length;++j) {
//                 int area = _getArea(i,j,height);
//                 max = Math.max(max,area);
//             }
//         }
//         return max;
//     }
//         //
//     public int _getArea(int x,int y,int[] height) {
//         return (y-x) * Math.min(height[x],height[y]);
//     }
// }

//2.双指针法：左右边界i，j，向中间收敛：左右夹逼   左右两个柱子谁低点，谁往中间挪动，记录area，并与最大值比较更新否
//O(n)
class Solution {
    public int maxArea(int[] height) {
        int max = 0;
        for(int i=0,j=height.length-1;i<j; ) {
            int minHeight = height[i] < height[j] ? height[i++] : height[j--];
            int area = (j-i+1) * minHeight;
            max = Math.max(area,max);
        }
        return max;
    }
}

```

---

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

```java
//1.双指针
//1.判断数组长度，如果数组为空或者长度小于3，返回空数组
//2.排序数组
// 3.对排序后的数组进行遍历
// 3.1若nums[i] >0,因为数组已经有序，所以后面所有的元素都大于0，不会有三个数相加等于0，返回
// 3.2若当前元素和上一个元素相同，continue，避免出现重复解
// 3.3令左指针L=i+1,右指针R=n-1,L<R时，循环下面操作：
//     3.3.1若nums[i]+nums[L]+nums[R]==0,执行循环，判断左边界和右边界是否和下一个元素相同，去除重复解，同时L和R指向下一个元素，寻找新的解
//     3.3.2若三者之和大于0，说明右边界的值过大，R左移
//     3.3.3若三者之和小于0，说明左边界的值太小，L右移
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        if(nums.length < 3) return ans;
        Arrays.sort(nums);
        for(int i=0;i<nums.length;i++) {
            int L = i+1;
            int R = nums.length-1;
            if(nums[i]>0) break;
            if(i>0 && nums[i] == nums[i-1]) continue;
            while(L<R) {
                int sum = nums[i]+nums[L]+nums[R];
                if(sum == 0) {
                    ans.add(Arrays.asList(nums[i],nums[L],nums[R]));
                    while(L<R && nums[L]==nums[L+1]) L++;
                    while(L<R && nums[R]==nums[R-1]) R--;
                    L++;
                    R--;
                }
                else if(sum<0) L++;
                else R--;
            }
        }
        return ans;
    }
}
```



两数之和











---





feedback

五遍



Python中交换x  y 不需要 z辅助

x , y = y ,x 即可