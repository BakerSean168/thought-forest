---
tags:
  - tech/lang/algorithm
  - type/concept
  - status/growing
description: Algorithm
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Algorithm MOC]] | [[学科知识 MOC]]

---

https://github.com/algorithmzuo/algorithm-journey

# 归并分治

## 原理
1. 思考一个问题在大范围上的答案是否等于，左部分的答案 + 右部分的答案 + 跨越左右的答案吧。
2. 计算“跨越左右产生的答案”时，如果加上左，右各自有序这个设定，会不会获得计算的便利性

## 例题
### 小和问题
```java
int[] arr = new int[];
int[] help = new int[];
smallSum(0, n-1);

public static long smallSum(int l, int r) {
    if (l == r) {
        return 0;
    }
    int m = (l + r) / 2;
    return smallSum(l, m) + smallSum(m+1, r) + merge(l, m, r);
}
//返回跨左右产生的小和累加和，左侧有序，右侧有序，让左右两侧整体有序
public static long merge(int l, int m, int r) {
    //统计部分
    long ans = 0;
    for (int j = m + 1, i = l, sum = 0; j <= r; j++) {
        while (i <= m && arr[i] <= arr[j]) {
            sum += arr[i++];
        }
        ans += sum;
    }
    //正常merge
    int i = l;
    int a = l;
    int b = m + 1 ;
    while (a <= m && b <= r) {
        help[i++] = arr[a] <= arr[b] ? arr[a++] : arr[b++];
    }
    while (a <= m) {
        help[i++] = arr[a++];
    }
    while (b <= m) {
        help[i++] = arr[b++];
    }
    for (i = l; i <= r; i++) {
        arr[i] = help[i];
    }
    return ans;
}
```

# 随机快排
核心点： 怎么选数字？
当数字是当前范围上的固定位置，则为普通快排
当数字是当前范围上的随机位置，则为随机快排

普通快排，时间复杂度为O(n^2),空间复杂度为O(n)
随机快排，时间复杂度为O(nlogn)，空间复杂度为o(logn)
**Code**：

随机快拍改进版（推荐）
```java
public static void quickSort2(int l, int r) {
    if (l >= r) {
        return;
    }
    // 随机这一下，常数时间比较大
    int x = arr[l + (int) (Math.random() * (r - l + 1))];
    patition2(l, r, x);
    // 为了防止底层的递归过程覆盖全局变量
    // 这里使用临时变量记录first，last
    int left = first;
    int right = last;
    quickSort2(l, left - 1);
    quickSort2(right + 1, r);
}

// 荷兰国旗问题
public static int first, last;

public static boid partition2(int l, int r, int x) {
    first = 1;
    last = r;
    int i = l;
    while (i <= last) {
        if (arr[i] == x) {
            i++;
        } else if (arr[i] < x) {
            swap(first++, i++);
        } else {
            swap(i , last--);
        }
    }
}
```

# 随机选择算法
无序数组中寻找第k大的数(O(n)复杂度)
**code**：
```java
class Solution {
    private static int first, last;

    public static int findKthLargest(int[] nums, int k) {
        return randomizedSelect(nums, nums.length - k);
    }

    public static int randomizedSelect(int[] arr, int i) {
        int ans = 0;
        for (int l = 0, r = arr.length - 1; l <= r;) {
            partition2(arr, l, r, arr[l + (int) (Math.random() * (r - l + 1))]);
            if (i < first) {
                r = first - 1;
            } else if (i > last) {
                l = last + 1;
            } else {
                ans = arr[i];
                break;
            }
        }
        return ans;
    }

    public static void partition2(int[] arr, int l, int r, int x) {
        first = l;
        last = r;
        int i = l;
        while (i <= last) {
            if (arr[i] == x) {
                i++;
            } else if (arr[i] < x) {
                swap(arr, first++, i++);
            } else {
                swap(arr, i , last--);
            }
        }
    }

    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

# 堆结构与堆排序

**堆结构**
完全二叉树和数组前缀范围来对应，大小，单独的变量size来控制。
i的父节点：(i - 1)/2, i'left child: i * 2 + 1, i'right child: i* 2 + 2
堆的定义（小根堆，大根堆）
堆的调整：heapInsert（向上调整），heapify（向下调整）
heapInsert，heapify方法的单次调用，时间复杂度O（log n），完全二叉树的结构决定

**堆排序**
从顶到底建堆，时间复杂度O（n * log n）
从底到顶建堆，时间复杂度O（n）
建好堆之后的调整截断，从最大值到最小值依次归位，时间复杂度O（n * log n）
额外空间复杂度O（1）

**code**：
```java
// i 位置的数，向上调整大根堆
public static void heapInset(int[] arr, int i) {
    while (arr[i] > arr[(i - 1) / 2]) {
        swap(arr, i, (i - 1) / 2);
        i = (i - 1) / 2;
    }
}

// i位置的数变小，向下调整大根堆
public static void heapify(int[] arr, int i, int size) {
    int l = i * 2 + 1;
    while (l < size) {
        int best = l + 1 < size && arr[l + 1] > arr[l] ? l + 1 : l;
        best = arr[best] > arr[i] ? best : i;
        if (best == i) {
            break;
        }
        swap(arr, best, i);
        i = best;
        l = i * 2 + 1;
    }
}

public static void swap(int[] arr, int i, int j) {
    int tmp = arr[i];
    arr[i] = arr[j];
    arr[j] = tmp;
}

// 从顶到底建立大根堆,O（n * log n）
// 依次弹出
public static void heapSort1(int[] arr) {
    int n = arr.length;
    for (int i = 0; i < n; i++) {
        heapInsert(arr, i);
    }
    int size = n;
    while (size > 1) {
        swap(arr, 0, --size);
        heapify(arr, 0, size);
    }
}

// 从底到顶建堆，时间复杂度O（n）
public static void heapSort(int[] arr) {
    int n = arr.length;
    for (int i = n - 1; i >= 0; i--) {
        heapify(arr, i, n);
    }
    int size = n;
    while (size > 1) {
        swap(arr, 0, --size);
        heapify(arr, 0, size);
    }
}
```

# 哈希表，有序表和比较器的用法

哈希表的用法（认为是集合，根据值来做 key 或者 根据内存地址做 key）
- HashSet和HashMap原理一样，有无伴随数据的区别
- 增，删，改，查时间为O（1），但是大常数
- 所以当key的范围是固定的，可控的情况下，可以用数组结构替代哈希表结构
- 对于8大类型（String。。。）
    哈希表中存入的是 值
- 对于其他类型（Object。。）
    哈希表中存入的是地址

有序表的用法（认为是集合，但是有序组织）
- TreeSet和TreeMap原理一样，有无伴随数据的区别
- 增，删，改，查和很多有序相关操作时间为O（log n）
- 有序表比较相同东西会去重，如果不想去重要定制比较器，堆不会去重
- 有序表在java里就是红黑树实现的

比较器：
- 定制比较策略
- 定义类，直接Lamda表达式

# 堆结构常见问题

## 合并K个有序链表

```java

public static ListNode mergeKLists(ArrayList<ListNode> arr) {
    // 小根堆
    PriorityQueue<ListNode> heap = new PriorityQueue<>((a,b) -> a.val - b.val);
    for (ListNode h : arr) {
        if (h != null) {
            heap.add(h);
        }
    }
    if (heap.isEmpty()) {
        return null;
    }
    // 弹出第一个结点做头结点
    ListNode h = heap.poll();
    ListNode pre = h;
    is (pre.next ! null) {
        heap.add(pre.next);
    }
    while (!heap.isEmpty()) {
        ListNode cur = heap.po;;();
        pre.next = cur;
        pre = cur;
        if (cur.next != null) {
            heap.add(cur.next);
        }
    }
    return h;
}

```

## 线段最多重合问题

```java
public class MaxCover {
    public static in MAXN = 10001;
    public static int[][] line = new int [MAXN][2];
    public static int n;
    public static int n;
    public static static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            for (int i = 0; i < n; i++) {
                in.nextToken();
                line[i][0] = (int) in.nval;
                in.nextToken();
                line[i][1] = (int) in.nval;
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }
    public static int compute() {
        // 堆的清空
        size = 0;
        Arrays.sort(line, 0, n, (a, b) -> a[0] - b[0]);
        int max = 0;
        for (int i = 0; i < n; i++) {
            while (size > 0 && heap[0] <= line[i][0]) {
                pop();
            }
            add(line[i][1]);
            max = Math.max(max, size);
        }
        return max;
    }

    public static int[] heap = new int[MAXN];
    public static int size;
    public static void add(int x) {
        heap[size] = x;
        int i = size++;
        while (heap[i] < heap[(i - 1) / 2]) {
            swap(i, (i - 1) / 2);
            i = (i - 1) / 2;
        }
    }
    public static void pop() {
        swap(0, --size);
        int i = 0,l = 1;
        while (l < size) {
            int best = l + 1< size && heap[l + 1] < heap[l] ? l+ 1: l;
            best = heap[best] < heap[i] ? best : i;
            if (best == i) {
                break;
            }
            swap(i, best);
            i = best;
            l = i * 2 + 1;
        }
    }
    public static void swap(int i, int j) {
        int tmp = heap[i];
        heap[i] = heap[j];
        heap[j] = tmp;
    }

}
// 时间复杂度O(n * log n)
// 空间复杂度O(n)
```
## 让数组整体累加和减半的最少操作次数
```
class Solution {
    public static int MAXN = 100001;
    public static long[] heap = new long[MAXN];
    public static int size;

    public static int halveArray(int[] nums) {
        size = nums.length;
        long sum = 0;
        for (int i = size - 1; i >= 0; i--) {
            heap[i] = (long) nums[i] << 20;
            sum += heap[i];
            heapify(i);
        }
        sum /= 2;
        int ans = 0;
        for (long minus = 0; minus < sum; ans++) {
            heap[0] /= 2;
            minus += heap[0];
            heapify(0);
        }
        return ans;
    }

    public static void heapify(int i) {
        int l = i * 2 + 1;
        while (l < size) {
            int best = l + 1 < size && heap[l + 1] > heap[l] ? l + 1 : l;
            best = heap[best] > heap[i] ? best : i;
            if (best == i) {
                break;
            }
            swap(best, i);
            i = best;
            l = i * 2 + 1;
        }
    }

    public static void swap(int i, int j) {
        long temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }

}
```

# 基数排序
**基于比较的排序**
只需要定义好两个对象之间怎么比较即可，对象的数据特征并不关心
**不基于比较的排序**
和比较无关的排序，对于对象的数据特征有要求

计数排序，简单，但数值范围大了就不行，样本是整数，范围窄

基数排序的实现细节
关键点：前缀数量分区的技巧，数字提取某一位的技巧
时间复杂度O(n)，额外空间复杂度复杂度O(m)
样本是10进制的非负整数

```
// 证没有负数 所有数减去最小值
public static int[] sortArray(int[] arr) {
    if (arr.length > 1) {
        int n = arr.length;
        int min = arr[0];
        for (int i = 1; i < n; i ++) {
            min = Math.min(min, arr[i]);
        }
        int max = 0;
        for (int i = 0; i < n: i++) {
            arr[i] -= min;
            max = Math.max(max, arr[i]);
        }
        radixSort(arr, n, bits(max));
        for (int i = 0; i < n; i++) {
            arr[i] += min;
        }
    }
}
// 基数排序核心代码
// arr内要保证没有负数 所有数减去最小值
// n是arr长度
// bits是arr中最大值在BASE进制下有几位
public static void radixSort(int[] arr, int n, int bits) {
    for (int offset = 1; bits > 0; offset *= BASE, bits--) {
        Arrays.fill(cnts, 0);
        for (int i = 0; i< n; i++) {
            // 数字提取某一位的技巧
            cnts[(arr[i] / offset) % BASE]++;
        }
        // 前缀次数累加形式
        for (int i = 1; i< BASE; i++) {
            cnts[i] = cnts[i] + cnts[i - 1];
        }
        for (int i = n - 1; i >= 0; i--) {
            // 前缀数量分区技巧
            help[--cnts[(arr[i] / offset) % BASE]] = arr[i];
        }
        
        for (int i = 0; i < n; i++) {
            arr[i] = help[i];
        }
    }
}

```

# 重要排序算法的总结
**稳定性：**同样大小的样本再排序之后不会改变原始的相对次序

|     排序      |   时间    |  空间   | 稳定性 |
| :-----------: | :-------: | :-----: | :----: |
| SelectionSort |  O(N^2)   |  O(1)   |   无   |
|  BubbleSort   |  O(N^2)   |  O(1)   |   有   |
| InsertionSort |  O(N^2)   |  O(1)   |   有   |
|   MergeSort   | O(N*logN) |  O(N)   |   有   |
|   QuickSort   | O(N*logN) | O(logN) |   无   |
|   HeapSort    | O(N*logN) |  O(1)   |   无   |
|   CountSort   |   O(N)    |  O(M)   |   有   |
|   RadixSort   |   O(N)    |  O(M)   |   有   |

数据量非常小时非常迅速：插入排序
性能优异，实现简单且利于改进，不在乎稳定性：随机快排
性能优异，不在乎额外空间占用，具有稳定性：归并排序
性能优异，额外空间占用要求O(1)，不在乎稳定性：堆排序

# 异或运算的骚操作
**性质**
1. 无进位相加
2. 满足交换律，结合律，同一批数字，不管异或顺序是什么，最后的结果相同
3. 0^n=n,n^n=0
4. 整体异或和如果是x，整体中某部分异或和为y，则剩余部分异或和为x^y

**题目**
交换两个数
    ```
    a = a ^ b;
    b = a ^ b;
    a = a ^ b;
    ```
不用任何判断语句和比较操作，返回两个数的最大值
    ```
    public static int flip(int n) {
        return n ^ 1;
    }

    // 非负数返回1，负数返回0
    public static int sign(int n) {
        return flip(n >>> 31);
    }

    public static int getMax2(int a, int b) {
        // c可能溢出
        int c = a - b;
        // a的符号
        int sa = sign(a);
        // b的符号
        int sb = sign(b);
        // c的符号
        int sc = sign(c);
        // 判断ab符号是不是不一样，如果不一样diffAB=1，如果一样diffAB=0
        int diffAB = sa ^ sb;
        // 判断ab符号是不是一样，如果一样sameAB=1，如果不一样sameAB=0
        int sameAB = flip(diffAB);
        int returnA = diffAB * sa + sameAB * sc;
        int returnB = flip(returnA);
        return a * returnA + b * returnB;
    }
    ```
找到缺失的数字
    ```
    public static int missingNumber(int[] nums) {
        int eorAll = 0, eorHas = 0;
        for (iint i = 0; i< nums.length; i++) {
            eorAll ^= i;
            eorHas ^= nums[i];
        }
        eorAll ^= nums.length;
        return eorAll ^ eorHas;
    }
    ```
数组中1种数出现了奇数次，其他都出现了偶数次，返回奇数次的数
    ```
    int eor = 0;
    eor ^ 所有数；
    ```
取最右侧的1
n的反：n所有位取反再加1
`n&((~n)+1)`
`n&(-n)`
数组中2种数出现了奇数次，其他都出现了偶数次，返回这2种奇数次的数
    ```
    eor1 ^ 所有的数
    结果必定有一位为1
    找到该1所在位置
    则出现奇数次的数必定一个该位含1，一个不含
    将所有数按此分为两种，再分别异或
    ```
数组中1种数出现次数小于m，其他都出现了m次，返回次数小于m的数
    ```
    假设1种数出现k（k<m）次
    二进制所有位数累加和%m=k
    就可以得到该数的二进制数
    int[] cnts = new int[32];
    for (int num : arr) {
        for (int i = 0; i < 32; i++) {
            cnts[i] += (num >> i) & 1;
        }
    }
    int ans = 0;
    for (int i = 0; i < 32; i++) {
        if (cnts[i] % m !=0) {
            ans != 1 << i;
        }
    }
    return ans;
    ```
# 位运算的骚操作
位运算的速度非常快，仅次于赋值操作，常数时间极好
判断一个整数是不是2的幂
    ```
    return n > 0 && n == (n & -n);
    ```
判断一个整数是不是3的幂
    ```
    // 1162261467是int范围内，最大的3的幂，它是3^19
    return n > 0 && 1162261467 % n == 0;
    ```
返回大于等于n的最小的2的幂
    ```
    public static final int near2power(int n) {
        if (n < 0) {
            return 1;
        }
        n--;
        n |n n >>> 1;
        n |n n >>> 2;
        n |n n >>> 4;
        n |n n >>> 8;
        n |n n >>> 16;
        return n + 1;
    }
    ```
区间[left, right]内所有数字 & 的结果
    ```
    public static int rangeBitwiseAnd(int left, int right) {
        while(left < right) {
            right -= right & - right;
        }
        return right;
    }
    ```
反转一个二进制状态（逆序）
    ```
    public static int reverseBits(int n) {
        n = ((n & 0xaaaaaaaa) >>> 1) | ((n & 0x55555555) << 1);
        n = ((n & 0xcccccccc) >>> 2) | ((n & 0x33333333) << 2);
        n = ((n & 0xf0f0f0f0) >>> 4) | ((n & 0x0f0f0f0f) << 4);
        n = ((n & 0xff00ff00) >>> 8) | ((n & 0x00ff00ff) << 8);
        n = (n >>> 16) | (n << 16);
        return n;
    }
    ```
返回一个数二进制中有几个1
    ```
    public static int hammingDistance(int x, int y) {
        return cntOnes(x ^ y);
    }
    
    public static int cntOnes(int n) {
        n = (n & 0x55555555) + ((n >>> 1) & 0x55555555);
        n = (n & 0x33333333) + ((n >>> 1) & 0x33333333);
        n = (n & 0x0f0f0f0f) + ((n >>> 1) & 0x0f0f0f0f);
        n = (n & 0x00ff00ff) + ((n >>> 1) & 0x00ff00ff);
        n = (n & 0x0000ffff) + ((n >>> 1) & 0x0000ffff);
        return n;
    }
    ```
# 位图（集合）
**原理**
用bit组成的数组来存放值，用bit状态1，0表示存在，不存在，取值和存值操作都用位运算
限制是必须为连续范围且不能过大。好处是极大的节省空间，因为1个数字只占用1个bit空间。

**实现**
```
public static class Bitset {
    public int[] set;

    public Bitset(int n) {
        set = new int[(n + 31) / 32];
    }
    public void add(int num) {
        set[num / 32] |= 1 << (num % 32);
    }
    public void remove(int num) {
        set[num / 32] &= ~(1 << (num % 32));
    }
    public void reverse(int num) {
        set[num / 32] ^= 1 <<(num % 32);
    }
    public boolean contains(int num) {
        return ((set[num / 32] >> (num % 32)) & 1) == 1;
    }
}
```

# 位运算实现加减乘除
```
public static int add(int a, int b) {
    int ans =a;
    while (b != 0 ) {
        ans = a ^ b;
        b = (a & b) << 1;
        a = ans;
    }
    return ans;
}

public static int minus(int a, int b) {
    return add(a, neg(b));
}

public static int neg(int n) {
    return add(~n, 1);
}

public static int multiply(int a,int b) {
    int ans = 0;
    while(b != 0) {
        if ((b & 1) != 0) {
            ans = add(ans, a);
        }
        a <<= 1;
        b >>>= 1;
    }
    return ans;
}

// a,b都不是整数最小值，返回a除以b的结果
public static int div(int a, int b) {
    int x = a < 0 ? neg(a) : a;
    int y = a < 0 ? neg(b) : b;
    int ans = 0;
    for (int i = 30; i >= 0; i = minus(i, 1)) {
        if ((x >> i) >= y) {
            ans |= (1 << i);
            x = minus(x, y << i);
        }
    }
    return a < 0 ^ b < 0 ? neg(ans) : ans;
}
```
# 链表高频题和必备技巧
#### 返回两个无环链表相交的第一个结点
```
长链表先走多出的结点个数的步数，然后两个链表一起走
```

#### 每k个结点一组翻转链表

#### 复制带随机指针的链表

#### 判断链表是不是回文结构
容器 用一个栈，先压后弹

```
public static boolean isPalindrome(ListNode head) {
    if (head == null || head.next == null) {
        return true;
    }
    ListNode slow = head, fast = head;
    while (fast.next != null && fast.next.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    // 现在中点就是slow，从中点开始往后的结点逆序
    ListNode pre = slow;
    ListNode cur = pre.next;
    ListNode next = null;
    pre.next = null;
    while (cur != null) {
        next = cur.next;
        cur.next = pre;
        pre = cur;
        cur = next;
    }
    boolean ans = true;
    ListNode left = head;
    ListNode right = pre;
    while (left != null && right != null) {
        if (left.val != right.val) {
            ans = false;
            break;
        }
        left = left.next;
        right = right.next;
    }
    // 把链表调整回原来的样子，再返回结果
    cur = pre.next;
    pre.next = null;
    next = null;
    while (cur != null) {
        next = cur.next;
        cur.next = pre;
        pre = cur;
        cur = next;
    }
    return ans;
}
```
#### 链表第一个入环结点
容器 hashset 
无容器 快慢指针
```
原理：当快慢指针第一次相遇后，快指针回到原点。变为一次跳一步，下一次相遇就在入环结点
```

#### 在链表上排序。时间复杂度O(n * log n)，空间复杂度O(1)，有稳定性

# 数据结构设计高频题
## setAll功能哈希表
```java
public class SetAllHashMap {
    public static HashMap<Integer, int[]> map = new HashMap<>();
    public static int setAllValue;
    public static int setAllTime;
    public static int cnt;

    public static void put(int k, int v) {
        if(map.containsKey(k)) {
            int[] value = map.get(k);
            value[0] = v;
            value[1] = cnt++;
        } else {
            map.put(k, new int[] { v, cnt++ });
        }
    }

    public static void setAll(int v) {
        setAllValue = v;
        setAllTime = cnt++;
    }

    public static int get(int k) {
        if (!map.containsKey(k)) {
            return -1;
        }
        int[] value = map.get(k);
        if (value[1] > setAllTime) {
            return value[0];
        } else {
            return setAllValue;
        }
    }
}
```

## 实现LRU结构
```java
class LRUCache {
    class DoubleNode {
        public int key;
        public int val;
        puulic DoubleNode last;
        public DoubleNode next;

        public DoubleNode(int k, int v) {
            key = k;
            val = v;
        }
    }

    class DoubleList {
        private DoubleNode head;
        private DoubleNode tail;

        public DoubleList() {
            head = null;
            tail = null;
        }

        public void addNode(DoubleNode newNode) {
            if (newNode == null) {
                return;
            }
            if (head == null) {
                head = newNode;
                tail = newNode;
            } else {
                tail.next = newNode;
                newNode.last = tail;
                tail = newNode;
            }
        }

        public void moveNodeToTail(DoubleNode node) {
            if (tail == node) {
                return;
            }
            if (head == node) {
                head = node.next;
                head.last = null;
            } else {
                node.last.next = node.next;
                node.next.last = node.last;
            }
            node.last = tail;
            node.next = null;
            tail.next = node;
            tail = node;
        }

        public DoubleNOde removeHead() {
            if (head == null) {
                return null;
            }
            DoubleNode ans = head;
            if (head == tail) {
                head = null;
                tail = null;
            } else {
                head = ans.next;
                ans.next = null;
                head.last = null;
            }
            return ans;
        }

        private HashMap<Integer, DoubleNode> keyNodeMap;

        private DoubleList nodeList;

        Private final int capacity;

        public LRUCache(int cap) {
            keyNodeMap = new HashMap<>();
            nodeList = new DoubleList();
            capacity = cap;
        }

        public int get(int key) {
            if (keyNodeMap.containsKey(key)) {
                DoubleNode ans = keyNodeMap.get(key);
                nodeList.moveNodeToTail(ans);
                return ans.val;
            }
            return -1;
        }
        
        public void put(int key, int value) {
            if (keyNodeMap.containsKey(key)) {
                DoubleNode node = keyNodeMap.get(key);
                node.val = value;
                nodeLIst.moveNodeToTail(node);
            } else {
                if (keyNodeMap.size() == capacity) {
                    keyNodeMap.remove(nodeList.removeHead().key);
                }
                DoubleNode newNOde = new Double(key, value);
                keyNodeMap.put(key, newNOde);
                nodeList.addNOde(newNode);
            }
        }
    }
}
```
## 插入、删除和获取随机元素O(1)时间的结构

增、删、随即得到
hashset记录值和下表
数组结构里记录值
删除掉一个值时，将最后一个值移动到删除值所在位置

## 插入、删除和获取随机元素O(1)时间且允许有重复数字的结构

## 快速获得数据流的中位数的结构

大根堆（较小的数）、小根堆（较大的数）

## 最大频率栈

## 全O(1)的数据结构

双向链表桶

# 二叉树高频题-不含树型dp

## 二叉树层序遍历
```java
// 二叉树的层序遍历
// 测试链接 : https://leetcode.cn/problems/binary-tree-level-order-traversal/
public class Code01_LevelOrderTraversal {
	// 不提交这个类
	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;
	}
	// 提交时把方法名改为levelOrder，此方法为普通bfs，此题不推荐
	public static List<List<Integer>> levelOrder1(TreeNode root) {
		List<List<Integer>> ans = new ArrayList<>();
		if (root != null) {
			Queue<TreeNode> queue = new LinkedList<>();
			HashMap<TreeNode, Integer> levels = new HashMap<>();
			queue.add(root);
			levels.put(root, 0);
			while (!queue.isEmpty()) {
				TreeNode cur = queue.poll();
				int level = levels.get(cur);
				if (ans.size() == level) {
					ans.add(new ArrayList<>());
				}
				ans.get(level).add(cur.val);
				if (cur.left != null) {
					queue.add(cur.left);
					levels.put(cur.left, level + 1);
				}
				if (cur.right != null) {
					queue.add(cur.right);
					levels.put(cur.right, level + 1);
				}
			}
		}
		return ans;
	}

	// 如果测试数据量变大了就修改这个值
	public static int MAXN = 2001;

	public static TreeNode[] queue = new TreeNode[MAXN];

	public static int l, r;

	// 提交时把方法名改为levelOrder，此方法为每次处理一层的优化bfs，此题推荐
	public static List<List<Integer>> levelOrder2(TreeNode root) {
		List<List<Integer>> ans = new ArrayList<>();
		if (root != null) {
			l = r = 0;
			queue[r++] = root;
			while (l < r) { // 队列里还有东西
				int size = r - l;
				ArrayList<Integer> list = new ArrayList<Integer>();
				for (int i = 0; i < size; i++) {
					TreeNode cur = queue[l++];
					list.add(cur.val);
					if (cur.left != null) {
						queue[r++] = cur.left;
					}
					if (cur.right != null) {
						queue[r++] = cur.right;
					}
				}
				ans.add(list);
			}
		}
		return ans;
	}

}
```
## 二叉树锯齿形层序遍历

## 二叉树的最大特殊宽度

## 求二叉树的最大深度、最小深度

## 二叉树先序序列化和反序列化

## 二叉树按层序列化和反序列化

## 利用先序与中序遍历序列构造二叉树

## 验证完全二叉树

1. 有右节点没有左节点，则不是
2. 发现孩子节点不全后，接下来的都是应该叶子节点，否则不是
## 求完全二叉树的节点个数，要求时间复杂度低于O(n)
```java
public class Code09_CountCompleteTreeNodes {

	// 不提交这个类
	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;
	}

	// 提交如下的方法
	public static int countNodes(TreeNode head) {
		if (head == null) {
			return 0;
		}
		return f(head, 1, mostLeft(head, 1));
	}

	// cur : 当前来到的节点
	// level :  当前来到的节点在第几层
	// h : 整棵树的高度，不是cur这棵子树的高度
	// 求 : cur这棵子树上有多少节点
	public static int f(TreeNode cur, int level, int h) {
		if (level == h) {
			return 1;
		}
		if (mostLeft(cur.right, level + 1) == h) {
			// cur右树上的最左节点，扎到了最深层
			return (1 << (h - level)) + f(cur.right, level + 1, h);
		} else {
			// cur右树上的最左节点，没扎到最深层
			return (1 << (h - level - 1)) + f(cur.left, level + 1, h);
		}
	}

	// 当前节点是cur，并且它在level层
	// 返回从cur开始不停往左，能扎到几层
	public static int mostLeft(TreeNode cur, int level) {
		while (cur != null) {
			level++;
			cur = cur.left;
		}
		return level - 1;
	}

}
```

## 公共祖先

# 面试

## 股票

```js
function maxAsset(n, m, a) {
    let prev = new Map();
    prev.set(0, m); // 初始状态：持有0股，现金为m

    for (let i = 0; i < n; i++) {
        const price = a[i];
        const curr = new Map();

        for (const [h, c] of prev) {
            // 不操作的情况
            if (!curr.has(h) || c > curr.get(h)) {
                curr.set(h, c);
            }

            // 尝试买入
            if (c >= price) {
                const newH = h + 1;
                const newC = c - price;
                if (!curr.has(newH) || newC > curr.get(newH)) {
                    curr.set(newH, newC);
                }
            }

            // 尝试卖出
            if (h > 0) {
                const newH = h - 1;
                const newC = c + price;
                if (!curr.has(newH) || newC > curr.get(newH)) {
                    curr.set(newH, newC);
                }
            }
        }

        prev = curr;
    }

    let maxTotal = 0;
    const lastPrice = a[n - 1];
    for (const [h, c] of prev) {
        const total = h * lastPrice + c;
        if (total > maxTotal) {
            maxTotal = total;
        }
    }

    return maxTotal;
}

// 示例输入处理
const input = `6 2
2 3 1 1 1 2`;
const lines = input.split('\n');
const [n, m] = lines[0].split(' ').map(Number);
const a = lines[1].split(' ').map(Number);

console.log(maxAsset(n, m, a)); // 输出6
```
## 从上到下返回树的节点（要自己构造树）

# 递归

### 二叉树的最近公共祖先

[LeetCode原题](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/description/?envType=study-plan-v2&envId=top-100-liked)

**题解**  
[题解](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/solutions/2023872/fen-lei-tao-lun-luan-ru-ma-yi-ge-shi-pin-2r95/?envType=study-plan-v2&envId=top-100-liked)

递归每一个节点会有以下情况：  
1. 空节点
2. p 节点
3. q 节点
4. 其他节点
    - 左右子树都找到
    - 只有左子树找到
    - 只有右子树找到
    - 都没有找到

递归的过程是，先判断当前节点的值（从 root 开始）：  
- 如果为空的，则**返回空**；  
- 如果是 p 或 q 节点，则返回**该节点**（如果 q 或 p 在 根节点，那么，另一个节点一定是它的子节点吗，它一定是最近公共祖先）；  
- 否则，**返回左右子树中分别找到的节点**。
  - 如果左右子树都找到，则返回**当前节点**（q、p节点分别在 当前节点的左右子树中，当前节点就是最近公共祖先）；
  - 如果只有左子树找到，则返回**左子树**（q、p节点都在左子树中，返回左子树的值，值为 p 或 q 节点）；
  - 如果只有右子树找到，则返回**右子树**。
  - 否则，**返回 null**（没有这两个节点，两个节点一定存在的话，不会出现这种情况）。

**代码**
```JavaScript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @param {TreeNode} p
 * @param {TreeNode} q
 * @return {TreeNode}
 */
var lowestCommonAncestor = function(root, p, q) {
    if (root === p || root === q || root === null) {
        return root;
    }
    const left = lowestCommonAncestor(root.left, p, q);
    const right = lowestCommonAncestor(root.right, p, q);
    if (left && right) {
        return root;
    }
    return left ?? right
};
```

### 从前序与中序遍历序列构造二叉树

[原题](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/?envType=study-plan-v2&envId=top-100-liked)

**题解**

前序遍历的第一个树为根节点，中序遍历的根节点隔开了左子树和右子树（并且在子树中也成立）。  

先根据前序遍历找到根节点，然后去中序遍历找到左右子树。  
左右子树的范围内又找到数组的第一个树（根节点），再去中序遍历找到左右子树的左右子树。

**代码**
```js
var buildTree = function(preorder, inorder) {
    const inorderMap = {};
    inorder.forEach((val, idx) => {
        inorderMap[val] = idx;
    });
    
    const build = (preStart, preEnd, inStart, inEnd) => {
        if (preStart > preEnd || inStart > inEnd) {
            return null;
        }
        
        const rootVal = preorder[preStart];
        const root = new TreeNode(rootVal);
        const rootIndexInInorder = inorderMap[rootVal];
        const leftSubtreeSize = rootIndexInInorder - inStart;
        
        root.left = build(preStart + 1, preStart + leftSubtreeSize, inStart, rootIndexInInorder - 1);
        root.right = build(preStart + leftSubtreeSize + 1, preEnd, rootIndexInInorder + 1, inEnd);
        
        return root;
    };
    
    return build(0, preorder.length - 1, 0, inorder.length - 1);
};
```






# 技巧

用 usd 数组来标识元素是否被选过

```js

var permute = function(nums) {
    let res = [];
    let path = [];
    let n = nums.length;
    let used = new Array(n).fill(false);
    
    function backtrack() {
        if (path.length === n) {
            res.push([...path]);
            return;
        }
        
        for (let i = 0; i < n; i++) {
            if (!used[i]) {
                used[i] = true;
                path.push(nums[i]);
                backtrack();
                path.pop();
                used[i] = false;
            }
        }
    }
    
    backtrack();
    return res;
};

```
