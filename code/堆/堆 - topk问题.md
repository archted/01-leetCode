### topk问题 快速选择算法

## 题目描述：在未排序的数组中找到第 **k** 个最大的元素。

解法一、二叉堆$O(n \log n)$

解法二、快速选择算法$O(n)$。下面讲解快速选择算法如何解决topK系列问题。

#### [母题]topk切分

**题目描述：**寻找下标k，满足左边k个元素<nums[k],右边(包含下标k)`nums.length-k`个元素≥nums[k]。我将它命名为`topk_split(nums,k)`，找到这个位置后停止迭代，完成了一次划分。

快速选择算法和快速排序算法是亲兄弟，也需要时用到`patiton()`函数。当调用`patiton() `函数时 ，`int[] points =  partition(int[] arr, int l, int r), index=points[0]`,因为我们要寻找下标k，在它左边有index个比nums[index]小的数，即左边界的定义，和二分法中左边界的定义一致。下面所有题型中，**一般使用`points[0]`作为判断条件。**

>  接下来就依赖`topk_split(nums,k)`解决所有的topk问题,平均时间复杂度$O(n)$

#### 获得前k小的数

#### 获取第k小的数

#### 获得前k大的数

#### 获得第k大的数

#### 只排序前k个小的数

#### 只排序后k个大的数

代码为：

```java
class Solution {

    /* 寻找下标k，满足左边k个元素<nums[k],右边(包含下标k)`nums.length-k`个元素≥nums[k]。 $O(n)$ */
    public void topk_split(int[] nums, int k) {
        int left = 0;
        int right = nums.length - 1;
        while (left <= right) { // [left,right]
            int[] points = partition(nums, left, right);
            // index含义：左边有index个比nums[index]小的数,即左边界的定义
            int index = points[0];
            if (index < k) {
                left = index + 1;
            } else if (index > k) {
                right = index - 1;
            } else {
                return;
            }
        }
    }

    /* 获得前k小的数 */
    public int[] topk_smalls(int[] nums, int k) {
        topk_split(nums, k);
        int[] arr = new int[k];
        for (int i = 0; i < k; i++) {
            arr[i] = nums[i];
        }
        return arr;
    }

    /* 获取第k小的数 */
    public int topk_small(int[] nums, int k) {
        topk_split(nums, k);
        return nums[k - 1];// 第1小的数, ..., 第k小的数
    }

    /* 获得前k大的数,思路：partion是按从小到大划分的，如果让index左边为前n-k个小的数，则index右边为前k个大的数(包括index) */
    public int[] topk_larges(int[] nums, int k) {
        int target = nums.length - k;
        topk_split(nums, target);
        int[] arr = new int[k];
        for (int i = 0; i < k; i++) {
            arr[i] = nums[target + i];
        }
        return arr;
    }

    /* 获得第k大的数,思路：parttion是按从小到大划分的，如果让index左边为前n-k个小的数，则index右边为前k个大的数(包括index) */
    public int topk_large(int[] nums, int k) {
        int target = nums.length - k;
        topk_split(nums, target);
        return nums[target];
    }

    /* 只排序前k个小的数,思路：分割数组为[前k小]和[后n-k大]两部分，对前半部分数组进行快排O(klogk) */
    public void topk_sort_left(int[] nums, int k) {
        topk_split(nums, k);
        quickSort(nums, 0, k - 1);
    }

    /* 只排序后k个大的数,思路：分割数组为[前n-k小]和[后k大]两部分，对后半部分数组进行快排O(klogk) */
    public void topk_sort_right(int[] nums, int k) {
        int target = nums.length - k;
        topk_split(nums, target);
        quickSort(nums, target, nums.length - 1);
    }

    /* 快速排序 */
    public void quickSort(int[] arr, int l, int r) {
        if (l >= r) {
            return;
        }
        int[] p = partition(arr, l, r);
        quickSort(arr, l, p[0] - 1);// 左边小于区
        quickSort(arr, p[1] + 1, r);
    }

    // partion 把选定的pivot挪到正确位置上
    public int[] partition(int[] arr, int l, int r) {
        // 如果是对近乎有序的数组进行快速排序，每次 partition 分区后子数组大小极不平衡，容易退化成O(n^2)的时间复杂度算法。
        // Math.random()= 0 ~ 0.99
        swap(arr, l, l + (int) (Math.random() * (r - l + 1)));
        int less = l - 1;// 小于区域的边界
        int more = r + 1;// 大于区域的边界
        int cur = l;
        int pivot = arr[l]; // 选择区间起点为pivot
        while (cur < more) {
            if (arr[cur] < pivot) {
                swap(arr, ++less, cur++);
            } else if (arr[cur] > pivot) {
                swap(arr, --more, cur);
            } else {
                cur++;
            }
        }
        return new int[] { less + 1, more - 1 };
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }

    public static void main(String[] args) {
        int[] arr1 = { 1, 3, 2, 34, 23, 42, 34, 24, 2, 4, 2, 4, 2, 34, 42, 3, 2, 4, 322, 43, 0, -19 };
        Solution solution = new Solution();
        // int[] topk_smalls = new Solution().topk_smalls(arr1,2);
        // System.out.println(Arrays.toString(topk_smalls));
        // int topk_small = new Solution().topk_small(arr1, 2);
        // System.out.println(topk_small);
        // int[] topk_larges = new Solution().topk_larges(arr1, 5);
        // System.out.println(Arrays.toString(topk_larges));
        // System.out.println(Arrays.toString(arr1));
        // System.out.println("topk_large");
        // int topk_large = solution.topk_large(arr1, 3);
        // System.out.println(topk_large);
        solution.topk_sort_right(arr1, 10);
    }
}
```

时间复杂度分析：

1）快速选择算法每次选定一个pivot，使用`partiton()`将数组分成左右两部分，最好情况下，每次 `p` 都恰好是正中间 `(lo + hi) / 2`，如果一直到最后子数组长度为1才找到第k大/小的值，那就一共要进行⌊logn⌋+1次partition。

2）第一次partition要遍历的长度是n，第二次要遍历的长度是n/2，第三次要遍历的长度是n/4……倒数第二次遍历的长度是2，最后一次遍历的长度是1。这是一个首项为1，公比为2的等比数列，记ax=2^(x-1)^，其中x为项数。这个等比数列的前x项和为2^x-1^，（等比数列的求和公式为Sx=(a1-ax*q)/(1-q)）。因为一共要求⌊logn⌋+1次partition，而$S(⌊logn⌋+1）=2^{(⌊logn⌋+1)}-1≈2n-1$，所以时间复杂度是O(2n-1)=O(n)。



但我们其实不能保证每次 `p` 都是正中间的索引的，最坏情况下 `p` 一直都是 `lo + 1` 或者一直都是 `hi - 1`，遍历的元素总数就是：

N + (N - 1) + (N - 2) + ... + 1

这就是个等差数列求和（Sn=n(a1+an)/2），时间复杂度会退化到 `O(N^2)`，**为了尽可能防止极端情况发生，我们需要在算法开始的时候对** **`nums`** **数组来一次随机打乱**。

#### 题目

获得第k大的数 对应[215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

[215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

![image-20210721161248605](https://i0.wp.com/tvax1.sinaimg.cn/large/00496yplgy1gvnwoee5a1j60fj0dr76u02.jpg)

解法二、快速选择算法，即partition+二分查找`O(N)`

**快速选择算法比较巧妙，相当于对数组部分排序而不需要完全排序，将平均时间复杂度降到 `O(N)`**。

我们回到一开始的问题，寻找第 `k` 大的元素，和快速排序有什么关系？

我们在学习「快速排序」的时候，会学到 `partition`（切分），`int p = partition(nums, lo, hi);`由于从左到右比较大小，因此上面求得的p就是左边界。`partition` 函数会将 `nums[p]` 排到正确的位置，使得 `nums[lo..p-1] < nums[p] <= nums[p+1..hi]`。

题目要求的是「第 `k` 个最大元素」，这个元素其实就是 `nums` 升序排序后「索引」为 

`target = len(nums) - k` 的这个元素。

**那么我们可以把 p 和 target 进行比较，如果 p < target 说明第 target 大的元素在 nums[p+1..hi] 中，如果 p > target 说明第 target 大的元素在 nums[lo..p-1] 中。**

这样就可以写出解法代码：

```c
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return topk_large(nums,k);
    }
    
    /* 获得第k大的数,思路：parttion是按从小到大划分的，如果让index左边为前n-k个小的数，则index右边为前k个大的数(包括index) */
    public int topk_large(int[] nums, int k) {
        int target = nums.length - k;
        topk_split(nums, target);
        return nums[target];
    }
    
    /* 寻找下标k，满足左边k个元素<nums[k],右边(包含下标k)`nums.length-k`个元素≥nums[k]。 */
    public void topk_split(int[] nums, int k) {
        int left = 0;
        int right = nums.length - 1;
        while (left <= right) { // [left,right]
            int[] points = partition(nums, left, right);
            // index含义：左边有index个比nums[index]小的数,即左边界的定义
            int index = points[0];
            if (index < k) {
                left = index + 1;
            } else if (index > k) {
                right = index - 1;
            } else {
                return;
            }
        }
    }
    // partion 把选定的pivot挪到正确位置上
    public int[] partition(int[] arr, int l, int r) {
        // 如果是对近乎有序的数组进行快速排序，每次 partition 分区后子数组大小极不平衡，容易退化成O(n^2)的时间复杂度算法。Math.random()= 0 ~ 0.99
        swap(arr, l + (int) (Math.random() * (r - l + 1)), l);
        int less = l - 1;// 小于区域的边界
        int more = r + 1;// 大于区域的边界
        int cur = l;
        int pivot = arr[l]; // 选择区间起点为pivot
        while (cur < more) {
            if (arr[cur] < pivot) {
                swap(arr, ++less, cur++);
            } else if (arr[cur] > pivot) {
                swap(arr, --more, cur);
            } else {
                cur++;
            }
        }
        return {less + 1,right-1};
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }

    public static void main(String[] args) {
        int[] nums = { 3, 2, 1, 5, 6, 4 };
        int findKthLargest = new Solution().findKthLargest(nums, 2);
        System.out.println(findKthLargest);
    }
}
```





解法一、二叉堆$O(n \log n)$

1)使用PriorityQueue

```c
class Solution{
    public int findKthLargest(int[] nums, int k) {
        // 小顶堆，堆顶是最小元素
        PriorityQueue<Integer> 
            pq = new PriorityQueue<>();
        for (int e : nums) {
            // 每个元素都要过一遍二叉堆
            pq.offer(e);
            // 堆中元素多于 k 个时，删除堆顶元素
            if (pq.size() > k) {
                pq.poll();
            }
        }
        // pq 中剩下的是 nums 中 k 个最大元素，
        // 堆顶是最小的那个，即第 k 个最大元素
        return pq.peek();
    }
}
```

堆的大小不会超过 `k`，所以插入和删除元素的复杂度是 `O(logK)`，再套一层 for 循环，总的时间复杂度就是 `O(NlogK)`。

### 两个数组第小k问题

#### 两个有序数组第k小的数

假设我们要找第 k 小数，我们可以每次循环排除掉 k/2 个数。看下边一个例子。

假设我们要找第 7 小的数字。

![image.png](https://pic.leetcode-cn.com/735ea8129ab5b56b7058c6286217fa4bb5f8a198e4c8b2172fe0f75b29a966cd-image.png)

我们比较两个数组的第 `k/2` 个数字，如果 `k` 是奇数，向下取整。也就是比较第 3 个数字，上边数组中的 4 和下边数组中的 3，_如果哪个小，就表明该数组的前_ `k/2个数字都不是第 `k`小数字，所以可以排除。

也就是 1，2，3 这三个数字不可能是第 7 小的数字，我们可以把它排除掉。将 1349 和 45678910 两个数组作为新的数组进行比较。

更一般的情况 `A[1]` ，`A[2]` ，`A[3]`，`A[k/2]` ... ，`B[1]`，`B[2]`，`B[3]`，`B[k/2]` ... ，如果 `A[k/2]`<`B[k/2]` ，那么`A[1]`，`A[2]`，`A[3]`，`A[k/2]`都不可能是第 `k` 小的数字。

橙色的部分表示已经去掉的数字。

![image.png](https://pic.leetcode-cn.com/09b8649cd2b8bbea74f7f632b098fed5f8404530ff44b5a0b54a360b3cf7dd8f-image.png)

由于我们已经排除掉了 3 个数字，就是这 3 个数字一定在最前边，所以在两个新数组中，我们只需要找第 7 - 3 = 4 小的数字就可以了，也就是 k = 4。此时两个数组，比较第 2 个数字，3 < 5，所以我们可以把小的那个数组中的 1 ，3 排除掉了。

![image.png](https://pic.leetcode-cn.com/f2d72fd3dff109ad810895b9a0c8d8782f47df6b2f24f9de72704961bc547fcb-image.png)

我们又排除掉 2 个数字，所以现在找第 4 - 2 = 2 小的数字就可以了。此时比较两个数组中的第 k / 2 = 1 个数，4 == 4，怎么办呢？由于两个数相等，所以我们无论去掉哪个数组中的都行，因为去掉 1 个总会保留 1 个的，所以没有影响。为了统一，我们就假设 4 > 4 吧，所以此时将下边的 4 去掉。

![image.png](https://pic.leetcode-cn.com/3c89a8ea29f2e19057b57242c8bc37c5f09b6796b96c30f3d42caea21c12f294-image.png)

由于又去掉 1 个数字，此时我们要找第 1 小的数字，所以只需判断两个数组中第一个数字哪个小就可以了，也就是 4。

所以第 7 小的数字是 4。



* 边界条件

1.我们每次都是取 `k/2` 的数进行比较，有时候可能会遇到数组长度小于 `k/2`的时候。我们此时将箭头指向它的末尾就可以了。

2.一个数组变为空，直接返回另一个数组的第k小元素即可。

* 采用递归思想

从上边可以看到，_无论是找第奇数个还是第偶数个数字，对我们的算法并没有影响_

，而且在算法进行中，k 的值_都有可能从奇数变为偶数_，_最终都会变为 1 或者由于一个数组空了，直接返回结果。_

所以我们采用递归的思路，为了防止数组长度小于 `k/2`，所以每次比较 `min(k/2`，`len`(数组) 对应的数字，把小的那个对应的数组的数字排除，将两个新数组进入递归，并且 `k` 要减去排除的数字的个数。递归出口就是当 `k=1` 或者其中一个数字长度是 `0` 了。



```java
    public int getKth(int[] nums1, int start1, int end1, int[] nums2, int start2, int end2, int k) {
        int len1 = end1 - start1 + 1;
        int len2 = end2 - start2 + 1;
        //让 len1 的长度小于 len2，这样就能保证如果有数组空了，一定是 len1 
        if (len1 > len2) return getKth(nums2, start2, end2, nums1, start1, end1, k);
        if (len1 == 0) return nums2[start2 + k - 1];

        if (k == 1) return Math.min(nums1[start1], nums2[start2]);

        int i = start1 + Math.min(len1, k / 2) - 1;
        int j = start2 + Math.min(len2, k / 2) - 1;

        if (nums1[i] > nums2[j]) {
            return getKth(nums1, start1, end1, nums2, j + 1, end2, k - (j - start2 + 1));
        }
        else {
            return getKth(nums1, i + 1, end1, nums2, start2, end2, k - (i - start1 + 1));
        }
    }
```

时间复杂度：每进行一次循环，我们就减少 k/2 个元素，所以时间复杂度是 O(log(k)。

空间复杂度：虽然我们用到了递归，但是可以看到这个递归属于尾递归，所以编译器不需要不停地堆栈，所以空间复杂度为 O(1)。

#### 两个有序数组中位数

[4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

![image-20210809114600856](https://i0.wp.com/tva1.sinaimg.cn/large/00496yplgy1gvnwonica7j60jx0j578802.jpg)

![image-20210812145445209](https://i0.wp.com/tva1.sinaimg.cn/large/00496yplgy1gvnwolhe2lj60km061mxs02.jpg)

前置知识

- 中位数 + topk问题 扩展

题意分析：

最直接的方式是模拟归并排序，用两个指针分别指向两个数组，比较指针下的元素大小，一共移动次数为 (m+n + 1)/2，便是中位数。时间复杂度$O(m+n)$, 不符合题中给出`O(log(m+n))`时间复杂度的要求。

解法一、题目是求中位数，其实就是求第 k 小数的一种特殊情况。

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int n = nums1.length;
    int m = nums2.length;
    int left = (n + m + 1) / 2;
    int right = (n + m + 2) / 2;
    //将偶数和奇数的情况合并，如果是奇数，会求两次同样的 k 。
    return (getKth(nums1, 0, n - 1, nums2, 0, m - 1, left) + getKth(nums1, 0, n - 1, nums2, 0, m - 1, right)) * 0.5;  
}
    
```

两个有序数组第k小的数`getKth()`函数时间复杂度为O(log k),而 k=(m+n)/2，所以最终的复杂度也就是 O(log(m+n)).

解法二、分割数组

一个长度为 m 的数组，有 0 到 m 总共 m + 1 个位置可以切。

![image.png](https://pic.leetcode-cn.com/d78f16160c2c546cb5447bad95ad4b82008b46966c21eaa1ba605fcd3dae4997-image.png)

我们把数组 A 和数组 B 分别在 i 和 j 进行切割。

![image.png](https://pic.leetcode-cn.com/b9d90d65438709de1d537b8b340fb15104a10da3a2b121727e6edfc8484b6b80-image.png)

将 i 的左边和 j 的左边组合成「左半部分」，将 i 的右边和 j 的右边组合成「右半部分」。因为A/B为有序数组，因此，左右两半部分的关系：`max(A[i-1],B[i-1])<=min(A[i],B[i])`.

* 当 A 数组和 B 数组的总长度是偶数时：

左半部分的长度等于右半部分，即`i + j = m - i  + n - j  , 也就是 j = ( m + n ) / 2 - i`.那么，中位数为(左半部分最大值+右半部分最小值)/2，即`(max(A[i-1],B[j-1])+min(A[i],B[j]))/2`

* 当 A 数组和 B 数组的总长度是奇数时：

保证左半部分的长度比右半部分大 1，即` i + j = m - i  + n - j  + 1也就是 j = ( m + n + 1) / 2 - i`。那么，中位数就是左半部分最大值，也就是左半部比右半部分多出的那一个数,即`max(A[i-1],B[j-1])`。

上边的第一个条件可以用一个统一的式子向上取整:`j=(m+n+1)/2−i`。

而且需要始终保证nums1为较短的数组，nums1过长可能导致j为负数而越界。

而对于第二个条件`max(A[i-1],B[i-1])<=min(A[i],B[i])`，奇数和偶数的情况是一样的，我们进一步分析。因为 A 数组和 B 数组是有序的，所以我们只需要保证 `B [ j - 1 ] < = A [ i ] && A [ i - 1 ] <= B [ j ]` 。

在搜索的过程中，我们会比较分割线左边和右边的数，即 nums[i]、 nums[i - 1]、 nums[j]、 nums[j - 1]，因此 这几个数的下标不能越界。

我们把关心的「边界线」两旁的 4 个数的极端情况都考虑一下：

![image-20191216171333129](https://pic.leetcode-cn.com/1605312125-RgKpAI-file_1605312122379)

当 i = 0 的时候，此时数组 nums1 在红线左边为空，可以设置 nums1_left_max = 负无穷。同理，当 j = 0 的时候，此时数组 nums2 在红线左边为空，可以设置 nums2_left_max = 负无穷。

当 i = m 的时候，此时数组 nums1 在红线右边为空，可以设置 nums1_right_min = 正无穷。同理，当 j = n 的时候，此时数组 nums2 在红线右边为空，可以设置 nums2_right_min = 正无穷。

所有的思路都理清了，最后一个问题，增加 i 的方式。当然用二分了。

```java
class Solution {
  public static double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // 始终保证nums1为较短的数组，nums1过长可能导致j为负数而越界
     if (nums1.length > nums2.length) {
        return findMedianSortedArrays(nums2, nums1);
      }
      int m = nums1.length;
      int n = nums2.length;
      int lo = 0;
      int hi = m;
      while (lo <= hi) {
        // partition A position i
        int i = lo + (hi - lo) / 2;
        // partition B position j
        int j = (m + n + 1) / 2 - i;

        int maxLeftA = i == 0 ? Integer.MIN_VALUE : nums1[i - 1];
        int minRightA = i == m ? Integer.MAX_VALUE : nums1[i];

        int maxLeftB = j == 0 ? Integer.MIN_VALUE : nums2[j - 1];
        int minRightB = j == n ? Integer.MAX_VALUE : nums2[j];

        if (maxLeftA <= minRightB && maxLeftB <= minRightA) {
          // total length is even
          if ((m + n) % 2 == 0) {
            return (double) (Math.max(maxLeftA, maxLeftB) + Math.min(minRightA, minRightB)) / 2;
          } else {
            // total length is odd
            return (double) Math.max(maxLeftA, maxLeftB);
          }
        } else if (maxLeftA > minRightB) {
          // binary search left half
          hi = i - 1;
        } else {
          // binary search right half
          lo = i + 1;
        }
      }
      return -1;//程序不会运行到这里
    }
}
```

时间复杂度：我们对较短的数组进行了二分查找，所以时间复杂度是 O(log（min（m，n））。

方法三、不好手撕代码

1. 只有一个有序数组的时候中位数的性质

如果数组的元素个数是偶数，此时我们可以想象有一条分界线，把数组分成两个部分，中位数就是介于这个分界线两边的两个元素的平均值；
如果数组的元素个数是奇数，此时我们也可以想象有一条分界线，把数组分成两个部分，此时我们**让分割线左边多一个元素**，此时分割线的左边的那个元素就是这个有序数组的中位数。

**在偶数/奇数个数的数组中，分割线的下标为4.**

![image-20191216154514844](https://pic.leetcode-cn.com/1605312125-SmaXRP-file_1605312122365)

2. 两个有序数组的时候中位数的性质

接下来看两个有序数组的时候，依然可以用这种画分界线的方式来找中位数。

当数组的总长度为偶数的时候，中位数就是分割线左边的最大值与分割线右边的最小值的平均数；
当数组的总长度为奇数的时候，中位数就是分割线左边的最大值**。因此，在数组长度是奇数的时候，中位数就是分割线左边的所有数的最大值。**这就是我们让分割线左边在整个数组长度是奇数的时候，多 1 个数的原因。

因为两个数组本别是有序数组，因此，我们只需要判定交叉的关系中，是否满足左边依然小于等于右边即可，即

第 1 个数组分割线左边的第 1 个数小于等于第 2 个数组分割线右边的第 1 的数；
第 2 个数组分割线左边的第 1 个数小于等于第 1 个数组分割线右边的第 1 的数。

3. 通过不断缩减搜索区间确定分割线的位置

接下来，我们就来看一下分割线怎么划分。

m+n 为偶数，分割线要求左侧有 $\cfrac{len(num1) + len(nums2)}{2} $个元素；
m+n 为奇数，分割线要求左侧有 $\cfrac{len(num1) + len(nums2)}{2} + 1 $ 个元素；

可以用一个统一的式子向上取整：$\cfrac{len(num1) + len(nums2) + 1}{2} $ 

4. 再次提炼问题、细化算法流程

**现在问题转化为在其中一个数组找到 i 个元素，则另一个数组的元素个数就一定是 $\cfrac{len(num1) + len(nums2) + 1}{2} - i $,使得两个数组分割线左右元素满足交叉判断条件。**找 i 个元素，我们通常的做法是找索引为 i的元素，因为下标是从 0 开始编号，因此编号为 i 的元素，就刚刚好前面有 i 个元素。因此，i 就是第 1 个数组分割线的右边的第 1 个元素。**即分割线的下标为索引i。**



下面我们来看怎么找 i。

分割线左侧元素小于等于分割线右侧元素。由于两个数组均为正序数组，则只需要要求：`nums1[i-1] <= nums2[j] && nums2[j-1] <= nums1[i]`；由于该条件等价于在[0, m]中找到最大的i使得nums1[i-1] <= nums2[j]，因此**使用二分法寻找右侧边界来确定i的值。**

分割线找到后，若m+n为奇数，分割线左侧的最大值即为中位数；若为偶数，分割线左侧的最大值与分割线右侧的最小值的平均数即为中位数。

5. 考虑极端情况

1.人为规定为nums1为较短的数组，并且使用`if(nums1[i - 1] > nums2[j])...`作为判断条件：会防止j取到nums2.length导致越界。

![image-20210820153611223](https://i0.wp.com/tvax1.sinaimg.cn/large/00496yplgy1gvnwoib44tj60rl08a40502.jpg)

![image-20210820155318254](https://i0.wp.com/tva4.sinaimg.cn/large/00496yplgy1gvnwoi0y0cj60ts0940v602.jpg)

对i-1的取值范围是`[0,m-1]`的解释：假如我们使用`if(nums1[i - 1] > nums2[j]) nums1分割线左移`，因为 i 的定义是nums1分割线的右边，而它的左边一定有值。由i的定义可知`int i = left + (right - left + 1) / 2;`,i最小取到1，会保证在条件判断`if(nums1[i - 1] > nums2[j])`时，`i-1`最小取到0不会越界。

**综上，求两数组合并后数组的中位数，要在nums1和nums2中各取一些数，较短数组的分割线会取到边界，较长数组的分割线只会在数组中间位置附近取分割线。人为定义通过在短数组上取分割线（i = left + (right - left + 1) / 2），可以使得分割线即使取到较短数据边界也不会越界（nums[i-1]）。**

在搜索的过程中，我们会比较分割线左边和右边的数，即 nums[i]、 nums[i - 1]、 nums[j]、 nums[j - 1]，因此 这几个数的下标不能越界。

我们把关心的「边界线」两旁的 4 个数的极端情况都考虑一下：

![image-20191216171333129](https://pic.leetcode-cn.com/1605312125-RgKpAI-file_1605312122379)

当 i = 0 的时候，此时数组 nums1 在红线左边为空，可以设置 nums1_left_max = 负无穷。同理，当 j = 0 的时候，此时数组 nums2 在红线左边为空，可以设置 nums2_left_max = 负无穷。

当 i = m 的时候，此时数组 nums1 在红线右边为空，可以设置 nums1_right_min = 正无穷。同理，当 j = n 的时候，此时数组 nums2 在红线右边为空，可以设置 nums2_right_min = 正无穷。

> 既然上面说j取不到边界，那么这里为什么还要设置nums2_left_max 和nums2_right_min 呢？
>
> 因为当测试用例为
>
> `[]
> [1]`时，程序都不会进入while()循环体，会直接运行这里的代码。

```java
public class Solution {

    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // 始终保证nums1为较短的数组，nums1过长可能导致j为负数而越界
        if (nums1.length > nums2.length) {
            int[] temp = nums1;
            nums1 = nums2;
            nums2 = temp;
        }
        
        int m = nums1.length;
        int n = nums2.length;

        // 分割线左边的所有元素需要满足的个数 m + (n - m + 1) / 2;
        int totalLeft = (m + n + 1) / 2;

        // 在 nums1 的区间 [0, m] 里查找恰当的分割线，
        // 使得 nums1[i - 1] <= nums2[j] && nums2[j - 1] <= nums1[i]
        int left = 0;
        int right = m;

        while (left < right) {//中位线一定存在，因此，left==right的时候就可以退出循环了
            int i = left + (right - left + 1) / 2;//在nums1中的分割线
            int j = totalLeft - i;//在nums2中的分割线
            //要找最大i，使得nums1[i-1] <= nums2[j]
            if (nums1[i - 1] > nums2[j]) {
                // 下一轮搜索的区间 [left, i - 1]
                right = i - 1;
            } else {
                // 下一轮搜索的区间 [i, right]
                left = i;
            }
        }

        int i = right;
        int j = totalLeft - i;

        int nums1LeftMax = i == 0 ? Integer.MIN_VALUE : nums1[i - 1];
        int nums1RightMin = i == m ? Integer.MAX_VALUE : nums1[i];
        int nums2LeftMax = j == 0 ? Integer.MIN_VALUE : nums2[j - 1];
        int nums2RightMin = j == n ? Integer.MAX_VALUE : nums2[j];       
        
        if (((m + n) % 2) == 1) {
            return Math.max(nums1LeftMax, nums2LeftMax);
        } else {
            return (double) ((Math.max(nums1LeftMax, nums2LeftMax) + Math.min(nums1RightMin, nums2RightMin))) / 2;
        }

    }
}
```

参考：https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/he-bing-yi-hou-zhao-gui-bing-guo-cheng-zhong-zhao-/
