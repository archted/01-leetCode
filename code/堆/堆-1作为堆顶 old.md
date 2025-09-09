# 堆

#### 二叉堆实现优先级队列

##### 一、二叉堆概览

二叉堆（Binary Heap）没什么神秘，性质比二叉搜索树 BST 还简单。其主要操作就两个，`sink`（下沉）和 `swim`（上浮），用以维护二叉堆的性质。其主要应用有两个，首先是一种排序方法「堆排序」，第二是一种很有用的数据结构「优先级队列」。

**二叉堆其实就是一种特殊的二叉树（完全二叉树），只不过存储在数组里。一般的链表二叉树，我们操作节点的指针，而在数组里，我们把数组索引作为指针**:

```java
// 父节点的索引
int parent(int root) {
    return root / 2;
}
// 左孩子的索引
int left(int root) {
    return root * 2;
}
// 右孩子的索引
int right(int root) {
    return root * 2 + 1;
}
```

画个图你立即就能理解了，注意数组的第一个索引 0 空着不用

![image-20210622112053577](https://cdn.jsdelivr.net/gh/archted/markdown-img@main/img/image-20210622112053577.png)



对于一个最大堆，根据其性质，显然堆顶，也就是 arr[1] 一定是所有元素中最大的元素。本文以最大堆为例讲解。

##### 二、优先级队列概览

优先级队列这种数据结构有一个很有用的功能，你插入或者删除元素的时候，元素会自动排序，这底层的原理就是二叉堆的操作。

下面我们实现一个简化的优先级队列，先看下代码框架：

```java
public class MaxPQ
    <Key extends Comparable<Key>> {
    // 存储元素的数组
    private Key[] pq;
    // 当前 Priority Queue 中的元素个数
    private int N = 0;

    public MaxPQ(int cap) {
        // 索引 0 不用，所以多分配一个空间
        pq = (Key[]) new Comparable[cap + 1];
    }

    /* 返回当前队列中最大元素 */
    public Key max() {
        return pq[1];
    }

    /* 插入元素 e */
    public void insert(Key e) {...}

    /* 删除并返回当前队列中最大元素 */
    public Key delMax() {...}

    /* 上浮第 k 个元素，以维护最大堆性质 */
    private void swim(int k) {...}

    /* 下沉第 k 个元素，以维护最大堆性质 */
    private void sink(int k) {...}

    /* 交换数组的两个元素 */
    private void exch(int i, int j) {
        Key temp = pq[i];
        pq[i] = pq[j];
        pq[j] = temp;
    }

    /* pq[i] 是否比 pq[j] 小？ */
    private boolean less(int i, int j) {
        return pq[i].compareTo(pq[j]) < 0;
    }

    /* 还有 left, right, parent 三个方法 */
}
```

##### 三、实现 swim 和 sink

为什么要有上浮 swim 和下沉 sink 的操作呢？为了维护堆结构.

细心的读者也许会问，这两个操作不是互逆吗，所以上浮的操作一定能用下沉来完成，为什么我还要费劲写两个方法？

是的，操作是互逆等价的，但是最终我们的操作只会在堆底和堆顶进行（等会讲原因），显然堆底的「错位」元素需要上浮，堆顶的「错位」元素需要下沉。

```java
private void swim(int k) {
    // 如果浮到堆顶，就不能再上浮了
    while (k > 1 && less(parent(k), k)) {
        // 如果第 k 个元素比上层大
        // 将 k 换上去
        exch(parent(k), k);
        k = parent(k);
    }
}
```

下沉比上浮略微复杂一点，因为上浮某个节点 A，只需要 A 和其父节点比较大小即可；但是下沉某个节点 A，需要 A 和其**两个子节点**比较大小，如果 A 不是最大的就需要调整位置，要把较大的那个子节点和 A 交换。

```java
private void sink(int k) {
    // 如果沉到堆底，就沉不下去了
    while (left(k) <= N) {
        // 先假设左边节点较大
        int older = left(k);
        // 如果右边节点存在，比一下大小
        if (right(k) <= N && less(older, right(k)))
            older = right(k);
        // 结点 k 比俩孩子都大，就不必下沉了
        if (less(older, k)) break;
        // 否则，不符合最大堆的结构，下沉 k 结点
        exch(k, older);
        k = older;
    }
}
```

##### 四、实现 delMax 和 insert

这两个方法就是建立在 `swim` 和 `sink` 上的。

**`insert`** **方法先把要插入的元素添加到堆底的最后，然后让其上浮到正确位置。**

```java
public void insert(Key e) {
    N++;
    // 先把新元素加到最后
    pq[N] = e;
    // 然后让它上浮到正确的位置
    swim(N);
}
```

**`delMax`** **方法先把堆顶元素 A 和堆底最后的元素 B 对调，然后删除 A，最后让 B 下沉到正确位置。**

```java
public Key delMax() {
    // 最大堆的堆顶就是最大元素
    Key max = pq[1];
    // 把这个最大元素换到最后，删除之
    exch(1, N);
    pq[N] = null;
    N--;
    // 让 pq[1] 下沉到正确位置
    sink(1);
    return max;
}
```

至此，一个优先级队列就实现了，插入和删除元素的时间复杂度为 `O(logK)`，`K` 为当前二叉堆（优先级队列）中的元素总数。

##### **五、代码实现优先级队列**

根据二叉堆上浮和下沉的api以及优先级队列插入和删除的api，写出完整的二叉堆实现优先级队列的代码

```java
class MaxPQ<Key extends Comparable<Key>> {
    // 存储元素的数组
    private Key[] pq;
    // 当前 Priority Queue 中的元素个数
    private int N = 0;

    public MaxPQ(int cap) {
        // 索引 0 不用，所以多分配一个空间
        pq = (Key[]) new Comparable[cap + 1];
    }

    /* 返回当前队列中最大元素 */
    public Key max() {
        return pq[1];
    }

    /* 插入元素 e */
    public void insert(Key e) {
        N++;
        // 先把新元素加到最后
        pq[N] = e;
        // 然后让它上浮到正确的位置
        swim(N);
    }

    /* 删除并返回当前队列中最大元素 */
    public Key delMax() {
        // 最大堆的堆顶就是最大元素
        Key max = pq[1];
        // 把这个最大元素换到最后，删除之
        exch(1, N);
        pq[N] = null;
        N--;
        // 让 pq[1] 下沉到正确位置
        sink(1);
        return max;
    }

    /* 上浮第 k 个元素，以维护最大堆性质 */
    private void swim(int k) {
        // 如果浮到堆顶，就不能再上浮了
        while (k > 1 && less(parent(k), k)) {
            // 如果第 k 个元素比上层大
            // 将 k 换上去
            exch(parent(k), k);
            k = parent(k);
        }
    }

    /* 下沉第 k 个元素，以维护最大堆性质 */
    private void sink(int k) {
        // 如果沉到堆底，就沉不下去了
        while (left(k) <= N) {
            // 先假设左边节点较大
            int older = left(k);
            // 如果右边节点存在，比一下大小
            if (right(k) <= N && less(older, right(k)))
                older = right(k);
            // 结点 k 比俩孩子都大，就不必下沉了
            if (less(older, k))
                break;
            // 否则，不符合最大堆的结构，下沉 k 结点
            exch(k, older);
            k = older;
        }
    }

    /* 交换数组的两个元素 */
    private void exch(int i, int j) {
        Key temp = pq[i];
        pq[i] = pq[j];
        pq[j] = temp;
    }

    /* pq[i] 是否比 pq[j] 小？ */
    private boolean less(int i, int j) {
        return pq[i].compareTo(pq[j]) < 0;
    }

    /* 还有 left, right, parent 三个方法 */
    // 父节点的索引
    int parent(int root) {
        return root / 2;
    }

    // 左孩子的索引
    int left(int root) {
        return root * 2;
    }

    // 右孩子的索引
    int right(int root) {
        return root * 2 + 1;
    }
}
```

使用数组0作为二叉堆头结点会不会更适合手撕代码呢？

```java
class MaxPQ<Key extends Comparable<Key>> {
    // 存储元素的数组
    private Key[] pq;
    // 当前 Priority Queue 中的元素个数
    private int N = 0;

    public MaxPQ(int cap) {
        // 索引 0 不用，所以多分配一个空间
        pq = (Key[]) new Comparable[cap];
    }

    /* 返回当前队列中最大元素 */
    public Key max() {
        return pq[0];
    }

    /* 插入元素 e */
    public void insert(Key e) {
        // 先把新元素加到最后
        pq[N] = e;
        // 然后让它上浮到正确的位置
        swim(N);
        N++;
    }

    /* 删除并返回当前队列中最大元素 */
    public Key delMax() {
        // 最大堆的堆顶就是最大元素
        Key max = pq[0];
        // 把这个最大元素换到最后，删除之
        exch(0, N-1);
        pq[N-1] = null;
        N--;
        // 让 pq[0] 下沉到正确位置
        sink(0);
        return max;
    }

    /* 上浮第 k 个元素，以维护最大堆性质 */
    private void swim(int k) {
        // 如果浮到堆顶，就不能再上浮了
        while (k > 0 && less(parent(k), k)) {
            // 如果第 k 个元素比上层大
            // 将 k 换上去
            exch(parent(k), k);
            k = parent(k);
        }
    }

    /* 下沉第 k 个元素，以维护最大堆性质 */
    private void sink(int k) {
        // 如果沉到堆底，就沉不下去了
        while (left(k) < N) {
            // 先假设左边节点较大
            int older = left(k);
            // 如果右边节点存在，比一下大小
            if (right(k) <= N && less(older, right(k)))
                older = right(k);
            // 结点 k 比俩孩子都大，就不必下沉了
            if (less(older, k))
                break;
            // 否则，不符合最大堆的结构，下沉 k 结点
            exch(k, older);
            k = older;
        }
    }

    /* 交换数组的两个元素 */
    private void exch(int i, int j) {
        Key temp = pq[i];
        pq[i] = pq[j];
        pq[j] = temp;
    }

    /* pq[i] 是否比 pq[j] 小？ */
    private boolean less(int i, int j) {
        return pq[i].compareTo(pq[j]) < 0;
    }

    /* 还有 left, right, parent 三个方法 */
    // 父节点的索引
    int parent(int root) {
        return (root-1) / 2;
    }

    // 左孩子的索引
    int left(int root) {
        return root * 2 + 1;
    }

    // 右孩子的索引
    int right(int root) {
        return root * 2 + 2;
    }
}
```

##### 堆排序

```java
class HeapSort {
    public static void heapSort(int[] arr) {
        MaxPQ<Integer> pq = new MaxPQ<>(arr.length);
        for (int data : arr) {
            pq.insert(data);
        }
        int idx = 0;
        while (pq.size() > 0) {
            arr[idx++] = pq.delMax();
        }
    }
}
```

左程云版本：

```java
Code_03_HeapSort   
 public static void heapSort(int[] arr) {
        if (arr == null || arr.length < 2) {
            return;
        }
        for (int i = 0; i < arr.length; i++) {
            heapInsert(arr, i);
        }
        int size = arr.length;
        swap(arr, 0, --size);
        while (size > 0) {
            heapify(arr, 0, size);
            swap(arr, 0, --size);
        }
    }
//可做优先队列，或者删除最大值
//从下往上的插入int arr[]={……4 6 2}
// //heapInsert就是建立大根堆

    public static void heapInsert(int[] arr, int index) {
        while (arr[index] > arr[(index - 1) / 2]) {
            swap(arr, index, (index - 1) / 2);
            index = (index - 1) / 2;
        }
    }
//从上往下调整堆结构
// //heapify就是把堆顶和最后一个数交换位置，然后堆的大小size--

    public static void heapify(int[] arr, int index, int size) {
        int left = index * 2 + 1;
        while (left < size) {
            int largest = left + 1 < size && arr[left + 1] > arr[left] ? left + 1 : left;
            largest = arr[largest] > arr[index] ? largest : index;
            if (largest == index) {
                break;
            }
            //大孩子位置largest
            swap(arr, largest, index);
            index = largest;
            left = index * 2 + 1;
        }
    }
    public static void swap(int[] arr, int i, int j) {
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }

```



##### 七、最后总结

二叉堆就是一种完全二叉树，所以适合存储在数组中，而且二叉堆拥有一些特殊性质。

二叉堆的操作很简单，主要就是上浮和下沉，来维护堆的性质（堆有序），核心代码也就十行。

优先级队列是基于二叉堆实现的，主要操作是插入和删除。**插入是先插到最后，然后上浮到正确位置；删除是调换位置后再删除，然后下沉到正确位置。**核心代码也就十行。







#### [295. 数据流的中位数](https://leetcode-cn.com/problems/find-median-from-data-stream/)

![image-20210621203326557](https://cdn.jsdelivr.net/gh/archted/markdown-img@main/img/image-20210621203326557.png)

**不仅要维护 `large` 和 `small` 的元素个数之差不超过 1，还要维护 `large` 堆的堆顶元素要大于等于 `small` 堆的堆顶元素**。



```java
class MedianFinder {

    private PriorityQueue<Integer> large;
    private PriorityQueue<Integer> small;
    
    public MedianFinder() {
        // 小顶堆
        large = new PriorityQueue<>();
        // 大顶堆
        small = new PriorityQueue<>((a, b) -> {
            return b - a;
        });
    }

    public double findMedian() {
        // 如果元素不一样多，多的那个堆的堆顶元素就是中位数
        if (large.size() < small.size()) {
            return small.peek();
        } else if (large.size() > small.size()) {
            return large.peek();
        }
        // 如果元素一样多，两个堆堆顶元素的平均数是中位数
        return (large.peek() + small.peek()) / 2.0;
    }

    // 正确的代码实现
    public void addNum(int num) {
        if (small.size() >= large.size()) {
            small.offer(num);
            large.offer(small.poll());
        } else {
            large.offer(num);
            small.offer(large.poll());
        }
    }
}
```

**简单说，想要往 `large` 里添加元素，不能直接添加，而是要先往 `small` 里添加，然后再把 `small` 的堆顶元素加到 `large` 中；向 `small` 中添加元素同理**。

为什么呢，稍加思考可以想明白，假设我们准备向 `large` 中插入元素：

如果插入的 `num` 小于 `small` 的堆顶元素，那么 `num` 就会留在 `small` 堆里，为了保证两个堆的元素数量之差不大于 1，作为交换，把 `small` 堆顶部的元素再插到 `large` 堆里。

如果插入的 `num` 大于 `small` 的堆顶元素，那么 `num` 就会成为 `samll` 的堆顶元素，最后还是会被插入 `large` 堆中。

反之，向 `small` 中插入元素是一个道理，这样就巧妙地保证了 `large` 堆整体大于 `small` 堆，且两个堆的元素之差不超过 1，那么中位数就可以通过两个堆的堆顶元素快速计算了。

至此，整个算法就结束了，`addNum` 方法时间复杂度 O(logN)，`findMedian` 方法时间复杂度 O(1)。
