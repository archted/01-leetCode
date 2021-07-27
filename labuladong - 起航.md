### 一、数据结构的存储方式

数据结构的基本存储方式就是链式和顺序两种，基本操作就是增删查改，遍历方式无非迭代和递归。

数组遍历框架，典型的线性迭代结构：

```c
void traverse(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        // 迭代访问 arr[i]
    }
}
```

链表遍历框架，兼具迭代和递归结构：

```c
/* 基本的单链表节点 */
class ListNode {
    int val;
    ListNode next;
}

void traverse(ListNode head) {
    for (ListNode p = head; p != null; p = p.next) {
        // 迭代访问 p.val
    }
}

void traverse(ListNode head) {
    // 递归访问 head.val
    traverse(head.next)
}
```

二叉树遍历框架，典型的非线性递归遍历结构：

```c
/* 基本的二叉树节点 */
class TreeNode {
    int val;
    TreeNode left, right;
}

void traverse(TreeNode root) {
    traverse(root.left)
    traverse(root.right)
}
```

二叉树框架可以扩展为 N 叉树的遍历框架：

```c
/* 基本的 N 叉树节点 */
class TreeNode {
    int val;
    TreeNode[] children;
}

void traverse(TreeNode root) {
    for (TreeNode child : root.children)
        traverse(child);
}
```



# 二叉树

LeetCode 124 题，难度 Hard，让你求二叉树中最大路径和，主要代码如下：

```c
int ans = INT_MIN;
int oneSideMax(TreeNode* root) {
    if (root == nullptr) return 0;
    int left = max(0, oneSideMax(root->left));
    int right = max(0, oneSideMax(root->right));
    ans = max(ans, left + right + root->val);
    return max(left, right) + root->val;
}
```



LeetCode 99 题，难度 Hard，恢复一棵 BST，主要代码如下：

```c
void traverse(TreeNode* node) {
    if (!node) return;
    traverse(node->left);
    if (node->val < prev->val) {
        s = (s == NULL) ? prev : s;
        t = node;
    }
    prev = node;
    traverse(node->right);
}
```





















































# 双指针技巧总结

[141.环形链表](https://leetcode-cn.com/problems/linked-list-cycle)

[142.环形链表II](https://leetcode-cn.com/problems/linked-list-cycle-ii)

[167.两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted)

我把双指针技巧再分为两类，一类是「快慢指针」，一类是「左右指针」。前者解决主要解决链表中的问题，比如典型的判定链表中是否包含环；后者主要解决数组（或者字符串）中的问题，比如二分查找。

## 快慢指针



# twoSum问题的核心思想

[1.两数之和](https://leetcode-cn.com/problems/two-sum)

给你一个数组和一个整数 `target`，可以保证数组中**存在**两个数的和为 `target`，请你返回这两个数的索引。

比如输入 `nums = [3,1,3,6], target = 6`，算法应该返回数组 `[0,2]`，因为 3 + 3 = 6。

[170.两数之和 III - 数据结构设计](https://leetcode-cn.com/problems/two-sum-iii-data-structure-design)



# 烧饼排序

[969.煎饼排序](https://leetcode-cn.com/problems/pancake-sorting)



# 其他算法技巧

# 前缀和数组

前缀和不难，却很有用，主要用于处理数组区间的问题。

前缀和的思路是这样的，对于一个给定的数组 `nums`，我们额外开辟一个前缀和数组进行预处理：

`pre[𝑖]=pre[𝑖−1]+nums[𝑖]`

```
int n = nums.length;
// 前缀和数组
int[] preSum = new int[n + 1];
preSum[0] = 0;
for (int i = 0; i < n; i++)
    preSum[i] = i == 0 ? nums[i] : preSum[i-1] + nums[i];
```

这个前缀和数组 `preSum` 的含义也很好理解，`preSum[i]` 就是 `nums[0..i]` 的和。那么如果我们想求 `nums[j...i]` 的和，只需要一步操作 `preSum[i]-preSum[j-1]` 即可，而不需要重新去遍历数组了。

[560.和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k)

![img](assets/cb19e8180ff444ac0a65442dbd64dd2ccc4aa76c.png)

我们想求有多少个子数组的和为 k，借助前缀和技巧很容易写出一个解法：



```c
int subarraySum(int[] nums, int k) {
    int n = nums.length;
    // 构造前缀和
    int[] sum = new int[n + 1];
    for (int i = 0; i < n; i++)
        sum[i] = sum[i-1] + nums[i];

    int ans = 0;
    // 穷举所有子数组
    for (int i = 1; i <= n; i++)
        for (int j = 0; j < i; j++)
            // sum of nums[j..i-1]
            if (sum[i] - sum[j] == k)
                ans++;

    return ans;
}
```

这个解法的时间复杂度 `O(N^2)` 空间复杂度 `O(N)`，并不是最优的解法。不过通过这个解法理解了前缀和数组的工作原理之后，可以使用一些巧妙的办法把时间复杂度进一步降低。

前面的解法有嵌套的 for 循环：



```
for (int i = 1; i <= n; i++)
    for (int j = 0; j < i; j++)
        if (sum[i] - sum[j] == k)
            ans++;
```

第二层 for 循环在干嘛呢？翻译一下就是，**在计算，有几个** **`j`** **能够使得** **`sum[i]`** **和** **`sum[j]`** **的差为 k。**毎找到一个这样的 `j`，就把结果加一。

我们可以把 if 语句里的条件判断移项，这样写：



```
if (sum[j] == sum[i] - k)
    ans++;
```

优化的思路是：**我直接记录下有几个** **`sum[j]`** **和** **`sum[i] - k`** **相等，直接更新结果，就避免了内层的 for 循环**。我们可以用哈希表，在记录前缀和的同时记录该前缀和出现的次数。

```java
int subarraySum(int[] nums, int k) {
    int n = nums.length;
    // map：前缀和 -> 该前缀和出现的次数
    HashMap<Integer, Integer> 
        preSum = new HashMap<>();
    // base case
    preSum.put(0, 1);

    int ans = 0, sum0_i = 0;
    for (int i = 1; i <= n; i++) {
        sum0_i += nums[i-1];
        // 这是我们想找的前缀和 nums[0..j-1]
        int sum0_j = sum0_i - k;
        // 如果前面有这个前缀和，则直接更新答案
        if (preSum.containsKey(sum0_j))
            ans += preSum.get(sum0_j);
        // 把前缀和 nums[0..i] 加入并记录出现次数
        preSum.put(sum0_i, preSum.getOrDefault(sum0_i, 0) + 1);
    }
    return ans;
}
```

时间复杂度降到了 `O(N)`

# 信封嵌套问题

### 

# 洗牌算法

### 一、洗牌算法

[384.打乱数组](https://leetcode-cn.com/problems/shuffle-an-array)

**分析洗牌算法正确性的准则：产生的结果必须有 n! 种可能，否则就是错误的。这个很好解释，因为一个长度为 n 的数组的全排列就有 n! 种，也就是说打乱结果总共有 n! 种。**

```c
// 得到一个在闭区间 [min, max] 内的随机整数
int randInt(int min, int max);

// 第一种写法
void shuffle(int[] arr) {
    int n = arr.length();
    /******** 区别只有这两行 ********/
    for (int i = 0 ; i < n; i++) {
        // 从 i 到最后随机选一个元素
        int rand = randInt(i, n - 1);
        /*************************/
        swap(arr[i], arr[rand]);
    }
}

// 第二种写法
    for (int i = 0 ; i < n - 1; i++)
        int rand = randInt(i, n - 1);

// 第三种写法
    for (int i = n - 1 ; i >= 0; i--)
        int rand = randInt(0, i);

// 第四种写法
    for (int i = n - 1 ; i > 0; i--)
        int rand = randInt(0, i);
```

### 二、蒙特卡罗方法验证正确性

**第一种思路**，每次进行洗牌算法后，就把得到的打乱结果对应的频数加一，重复进行 100 万次，如果每种结果出现的总次数差不多，那就说明每种结果出现的概率应该是相等的。

**第二种思路**，可以这样想，arr 数组中全都是 0，只有一个 1。我们对 arr 进行 100 万次打乱，记录每个索引位置出现 1 的次数，如果每个索引出现的次数差不多，也可以说明每种打乱结果的概率是相等的。

# 递归详解

437. Path Sum III

给一课二叉树，和一个目标值，节点上的值有正有负，返回树中和等于目标值的路径条数，让你编写 pathSum 函数.

![image-20210525145527004](assets/image-20210525145527004.png)





```c
//root为根节点的树中，和为目标值的路径总数。
int pathSum(TreeNode root, int sum) {
    if (root == null) return 0;
    // 1.以自己为开头的情况
    int pathImLeading = count(root, sum); 
    //2.不以自己开头的情况
    int leftPathSum = pathSum(root.left, sum); // 以左边路径中某个节点开头，计算得到路径总数
    int rightPathSum = pathSum(root.right, sum); // 以右边路径中某个节点开头，计算得到路径总数
    return leftPathSum + rightPathSum + pathImLeading;
}
//以node为路径开头，和为目标值的路径总数。
int count(TreeNode node, int sum) {
    if (node == null) return 0;
    return (node.val == sum) + count(node.left, sum - node.val) + count(node.right, sum - node.val);
}
```

## 分治算法

**归并排序**

```javascript
var sort = function (arr) {
    if (arr.length <= 1) return arr;

    let mid = parseInt(arr.length / 2);
    // 递归调用自身，拆分的数组都是排好序的，最后传入merge合并处理
    return merge(sort(arr.slice(0, mid)), sort(arr.slice(mid)));
}
// 将两个排好序的数组合并成一个顺序数组
var merge = function (left, right) {
    let res = [];
    while (left.length > 0 && right.length > 0) {
        // 不断比较left和right数组的第一项，小的取出存入res
        left[0] < right[0] ? res.push(left.shift()) : res.push(right.shift());
    }
    return res.concat(left, right);
}
```

# 接雨水问题详解

[42.接雨水](https://leetcode-cn.com/problems/trapping-rain-water)



<img src="assets/image-20210525153124610.png" alt="image-20210525153124610" style="zoom:80%;" />

下面就来由浅入深介绍暴力解法 -> 备忘录解法 -> 双指针解法，在 O(N) 时间 O(1) 空间内解决这个问题。

对于位置 `i`，能够装的水为：

```c
water[i] = min(
               # 左边最高的柱子
               max(height[0..i]),  
               # 右边最高的柱子
               max(height[i..end]) 
            ) - height[i]
```

### 二、备忘录优化

**我们开两个数组 `r_max` 和 `l_max` 充当备忘录，`l_max[i]` 表示位置 `i` 左边最高的柱子高度，`r_max[i]` 表示位置 `i` 右边最高的柱子高度**。预先把这两个数组计算好，避免重复计算：

```c
int trap(vector<int>& height) {
    if (height.empty()) return 0;
    int n = height.size();
    int res = 0;
    // 数组充当备忘录
    vector<int> l_max(n), r_max(n);
    // 初始化 base case
    l_max[0] = height[0];
    r_max[n - 1] = height[n - 1];
    // 从左向右计算 l_max
    for (int i = 1; i < n; i++)
        l_max[i] = max(height[i], l_max[i - 1]);
    // 从右向左计算 r_max
    for (int i = n - 2; i >= 0; i--) 
        r_max[i] = max(height[i], r_max[i + 1]);
    // 计算答案
    for (int i = 1; i < n - 1; i++) 
        res += min(l_max[i], r_max[i]) - height[i];
    return res;
}
```

时间复杂度 O(N)，空间复杂度 O(N)

### 三、双指针解法

<img src="assets/image-20210525160531561.png" alt="image-20210525160531561" style="zoom:50%;" />

此时的 `l_max` 是 `left` 指针左边的最高柱子，但是 `r_max` 并不一定是 `left` 指针右边最高的柱子，这真的可以得到正确答案吗？

其实这个问题要这么思考，我们只在乎 `min(l_max, r_max)`。**对于上图的情况，我们已经知道 `l_max < r_max` 了，至于这个 `r_max` 是不是右边最大的，不重要。重要的是 `height[i]` 能够装的水只和较低的 `l_max` 之差有关**：

```c
int trap(vector<int>& height) {
    if (height.empty()) return 0;
    int n = height.size();
    int left = 0, right = n - 1;
    int res = 0;
    
    int l_max = height[0];
    int r_max = height[n - 1];
    
    while (left <= right) {
        l_max = max(l_max, height[left]); //l_max 是 height[0..left] 中最高柱子的高度
        r_max = max(r_max, height[right]);//r_max 是 height[right..end] 的最高柱子的高度。
        
        //接雨水取决于较短的一边
        if (l_max < r_max) {
            res += l_max - height[left];
            left++; 
        } else {
            res += r_max - height[right];
            right--;
        }
    }
    return res;
}
```

时间复杂度 O(N)，空间复杂度 O(1)





- https://leetcode-cn.com/problems/checking-existence-of-edge-length-limited-paths/)







# 如何寻找最长回文子串

[5.最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring)

**寻找回文串的问题核心思想是：从中间开始向两边扩散来判断回文串**。

```c
string longestPalindrome(string s) {
    string res;
    for (int i = 0; i < s.size(); i++) {
        // 以 s[i] 为中心的最长回文子串
        string s1 = palindrome(s, i, i);
        // 以 s[i] 和 s[i+1] 为中心的最长回文子串
        string s2 = palindrome(s, i, i + 1);
        // res = longest(res, s1, s2)
        res = res.size() > s1.size() ? res : s1;f
        res = res.size() > s2.size() ? res : s2;
    }
    return res;
}
//为什么要传入两个指针 l 和 r 呢？因为这样实现可以同时处理回文串长度为奇数和偶数的情况
string palindrome(string& s, int l, int r) {
    // 防止索引越界
    while (l >= 0 && r < s.size()
            && s[l] == s[r]) {
        // 向两边展开
        l--; r++;
    }
    // 返回以 s[l] 和 s[r] 为中心的最长回文串
    return s.substr(l + 1, r - l - 1);
}
```

这个问题可以用动态规划方法解决，时间复杂度一样，但是空间复杂度至少要 O(N^2) 来存储 DP table。这道题是少有的动态规划非最优解法的问题。

另外，这个问题还有一个巧妙的解法 Manacher's Algorithm（马拉车算法），时间复杂度只需要 O(N) 。

step1:预处理，将奇偶变为奇数。

​                               <img src="assets/image-20210526155855642.png" alt="image-20210526155855642" style="zoom:67%;" />

\#可以换成3，因为比对的时候永远是虚轴跟虚轴比对，实轴跟实轴比对

step2: 构造数组pArr[n]，数组pArr[i]来记录manacher字符串chaArr最长回文串,p[i]-1正好是原字符串s最长回文子串的长度。

//R是最右回文边界，C是R对应的回文中心，maxn是记录的最大回文半径

```c
int maxLcsplength(string str) {
    //空字符串直接返回0
    if (str.length() == 0) {
        return 0;
    }
    //记录下manacher字符串的长度，方便后面使用
    int len = (int)(str.length() * 2 + 1);
    //开辟动态数组chaArr记录manacher化的字符串
    //开辟动态数组pArr记录每个位置的回文半径
    char *chaArr = new char[len];
    int* pArr = new int[len];
    int index = 0;
    for (int i = 0; i < len;i++) {
        chaArr[i] = (i & 1) == 0 ? '#' : str[index++];
    }
    //到此完成对原字符串的manacher化
    //R是最右回文边界，C是R对应的最左回文中心，maxn是记录的最大回文半径
    int R = -1;
    int C = -1;
    int maxn = 0;
    //开始从左到右遍历
    for (int i = 0; i < len; i++) {
        //第一步直接取得可能的最短的回文半径，当i>R时，最短的回文半径是1，反之，最短的回文半径可能是i对应的i'的回文半径或者i到R的距离
        pArr[i] = R > i ? min(R - i, pArr[2 * C - i]) : 1;
        //取最小值后开始从边界暴力匹配，匹配失败就直接退出
        while (i + pArr[i]<len && i - pArr[i]>-1) {
            if (chaArr[i + pArr[i]] == chaArr[i - pArr[i]]) {
                pArr[i]++;
            }
            else {
                break;
            }
        }
        //观察此时R和C是否能够更新
        if (i + pArr[i] > R) {
            R = i + pArr[i];
            C = i;
        }
        //更新最大回文半径的值
        maxn = max(maxn, pArr[i]);
    }
    //记得清空动态数组哦
    delete[] chaArr;
    delete[] pArr;
    //这里解释一下为什么返回值是maxn-1，因为manacherstring的长度和原字符串不同，所以这里得到的最大回文半径其实是原字符串的最大回文子串长度加1，有兴趣的可以自己验证试试
    return maxn - 1;
}
```

#### [214. 最短回文串](https://leetcode-cn.com/problems/shortest-palindrome/)

给定一个字符串 ***s***，你可以通过在字符串前面添加字符将其转换为回文串。找到并返回可以用这种方式转换的最短回文串。

**示例 1：**

```
输入：s = "aacecaaa"
输出："aaacecaaa"
```

```java
public class Code_05_Manacher_ShortestEnd {

	public static char[] manacherString(String str) {
		char[] charArr = str.toCharArray();
		char[] res = new char[str.length() * 2 + 1];
		int index = 0;
		for (int i = 0; i != res.length; i++) {
			res[i] = (i & 1) == 0 ? '#' : charArr[index++];
		}
		return res;
	}

	public static String shortestEnd(String str) {
		if (str == null || str.length() == 0) {
			return null;
		}
		char[] charArr = manacherString(str);
		int[] pArr = new int[charArr.length];
        //R是最右回文边界，C是R对应的最左回文中心，maxn是记录的最大回文半径
		int C = -1;
		int pR = -1;
		int maxContainsEnd = -1;
		for (int i = 0; i != charArr.length; i++) {
			pArr[i] = pR > i ? Math.min(pArr[2 * C - i], pR - i) : 1;
			while (i + pArr[i] < charArr.length && i - pArr[i] > -1) {
				if (charArr[i + pArr[i]] == charArr[i - pArr[i]])
					pArr[i]++;
				else {
					break;
				}
			}
			if (i + pArr[i] > pR) {
				pR = i + pArr[i];
				C = i;
			}
			if (pR == charArr.length) {
				maxContainsEnd = pArr[i];
				break;
			}
		}
		char[] res = new char[str.length() - maxContainsEnd + 1];
		for (int i = 0; i < res.length; i++) {
			res[res.length - 1 - i] = charArr[i * 2 + 1];
		}
		return String.valueOf(res);
	}
}

```









# 前缀树

字典树也叫前缀树、Trie。它本身就是一个树型结构，也就是一颗多叉树，学过树的朋友应该非常容易理解，它的核心操作是插入，查找。

其原理也很简单，正如我前面所言，其公共前缀仅会被存储一次，因此如果我想在一堆单词中找某个单词或者某个前缀是否出现，我无需进行完整遍历，而是遍历前缀树即可。本质上，使用前缀树和不使用前缀树减少的时间就是公共前缀的数目。也就是说，一堆单词没有公共前缀，使用前缀树没有任何意义。



**208.implement-trie-prefix-tree**

```java
class Trie {

    class TireNode {
        private boolean isEnd;
        TireNode[] next;

        public TireNode() {
            isEnd = false;
            next = new TireNode[26];
        }
    }

    private TireNode root;

    public Trie() {
        root = new TireNode();
    }

    public void insert(String word) {
        TireNode node = root;
        for (char c : word.toCharArray()) {
            if (node.next[c - 'a'] == null) {
                node.next[c - 'a'] = new TireNode();
            }
            node = node.next[c - 'a'];
        }
        node.isEnd = true;
    }

    public boolean search(String word) {
        TireNode node = root;
        for (char c : word.toCharArray()) {
            node = node.next[c - 'a'];
            if (node == null) {
                return false;
            }
        }
        return node.isEnd;
    }

    public boolean startsWith(String prefix) {
        TireNode node = root;
        for (char c : prefix.toCharArray()) {
            node = node.next[c - 'a'];
            if (node == null) {
                return false;
            }
        }
        return true;
    }
}
```







