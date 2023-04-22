---
title: 冲刺春招-精选笔面试66题大通关（day1~22）
link: coding-train/leetcode/bytedance-code-train
catalog: true
subtitle: 知识点：链表；冲刺01简单，冲刺02中等，冲刺03困难；冲刺02容易在面试中遇见。
date: 2022-03-08 18:00:52
cover: img/header_img/milky-way-over-bow-lake-alberta-canada-wallpaper-for-1920x1080-63-873.jpg
tags:
- #leetcode
categories:
- [题目记录]
---

#leetcode #数据结构 #算法 #字节校园系列 

# day1

#数据结构/链表 

day1题目：[21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)、[146. LRU 缓存](https://leetcode-cn.com/problems/lru-cache/)、[25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)
学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

<!-- more -->

能用JS实现的我就用JS实现了2333，尽量练一练JS
这一天的都是链表，可以看之前的学习笔记复习：[数据结构学习笔记＜1＞ 线性表](https://blog.csdn.net/qq_45890533/article/details/104528176)


## 21. 合并两个有序链表 （简单）
将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 
- **递归就完事了。**
```js
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} list1
 * @param {ListNode} list2
 * @return {ListNode}
 */
var mergeTwoLists = function(list1, list2) {
    let p1 = list1
    let p2 = list2
    if(!p1 && !p2) return null
    else if(!p1) return p2
    else if(!p2) return p1
    else if(p1.val <= p2.val) {
        p1.next = mergeTwoLists(p1.next, p2)
        return p1
    } else {
        p2.next = mergeTwoLists(p2.next, p1)
        return p2
    }
};
```


## 146.LRU缓存 （中等）
请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
- `LRUCache(int capacity)` 以 **正整数** 作为容量 `capacity` 初始化 `LRU` 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value `；如果不存在，则向缓存中插入该组 `key-value `。如果插入操作导致关键字数量超过 capacity ，则应该 **逐出** 最久未使用的关键字。
函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。

### 思路
双向链表（空头尾结点），链表尾部为最近最少使用，一旦使用了就移至链表头部。

```cpp
struct DLNode {	
    int key, val;
    DLNode* prev;
    DLNode* next;
    DLNode(int key = -1, int val = -1):prev(nullptr), next(nullptr), key(key), val(val){}
};
```
`put` 的时候若关键字不存在，则头插法插入关键字及值，若满了则逐出链表尾部结点 。

```cpp
void put(int key, int value) {
    if(cache.find(key) == cache.end() || cache[key] == nullptr) {
        // 关键字不存在 则插入关键字 头插
        if(size == capacity) {  // 满了 逐出最后一个
            cache[tail->prev->key] = nullptr;
            remove(tail->prev);
        }
        DLNode* newp = insert(key, value);
        cache[key] = newp;
    } else {    // 存在 修改该组值 
        cache[key] = move2head(cache[key]);
        cache[key]->val = value;
    }
}
```
`get` 时若关键字不存在，返回-1，若存在则将其对应结点移至链表头部。

```cpp
int get(int key) {
    if(cache.find(key) == cache.end() || cache[key] == nullptr) 
	    return -1;
    cache[key] = move2head(cache[key]);
    return cache[key]->val;
}
```
### 完整代码

```cpp
struct DLNode {
    int key, val;
    DLNode* prev;
    DLNode* next;
    DLNode(int key = -1, int val = -1):prev(nullptr), next(nullptr), key(key), val(val){}
};
class LRUCache {
public:
    int capacity;
    int size;
    unordered_map<int, DLNode*> cache;
    DLNode* head;
    DLNode* tail;
    LRUCache(int c): capacity(c), size(0) {
        head = new DLNode();
        tail = new DLNode();
        head->next = tail;
        tail->prev = head;
    }
    void remove(DLNode* p) {
        p->prev->next = p->next;
        p->next->prev = p->prev;
        delete p;
        --size;
    }
    DLNode* insert(int key, int val) {  // 头插
        DLNode* temp = head->next;
        DLNode* newp = new DLNode(key, val);
        newp->next = temp;
        newp->prev = head;
        temp->prev = newp;
        head->next = newp;
        ++size;
        return newp;
    }
    DLNode* move2head(DLNode* p) {
        DLNode* newp = insert(p->key, p->val);
        remove(p);
        return newp;
    }
    int get(int key) {
        if(cache.find(key) == cache.end() || cache[key] == nullptr) return -1;
        cache[key] = move2head(cache[key]);
        return cache[key]->val;
    }
    
    void put(int key, int value) {
        if(cache.find(key) == cache.end() || cache[key] == nullptr) {// 关键字不存在 则插入关键字 头插
            if(size == capacity) {  // 满了 逐出最后一个
                cache[tail->prev->key] = nullptr;
                remove(tail->prev);
            }
            DLNode* newp = insert(key, value);
            cache[key] = newp;
        } else {    // 存在 修改该组值 
            cache[key] = move2head(cache[key]);
            cache[key]->val = value;
        }
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```

## 25. K 个一组翻转链表（困难）

给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

### 思路
WA了一次，发现时反转链表的时候prev设置成null了（上一题惯性2333），应当设置成e.next 因为后面可能还有链表。
分解子问题：反转`s` -> `e`这个链表
则需要这样一个函数`reverseList(s, e)`， 返回reverse后链表的头和尾
```js
var reverseList = function(s, e) {
    let prev = e.next;		// 注意这儿哈
    let nowv = s;
    while(prev != e) {		// 还有这儿哈
        let temp = nowv.next;
        nowv.next = prev;
        prev = nowv;
        nowv = temp;
    }
    return [e, s];
}
```
然后就是反转后将其拼接回去咯~
思路如图，利用带空头结点的链表，并且注意，如果不满k个的话不用反转。
![reverse](https://img-blog.csdnimg.cn/a5a0ef1f08a14c4aa980820ae1cecd24.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5L2ZY29z,size_20,color_FFFFFF,t_70,g_se,x_16)
```js
var reverseKGroup = function(head, k) {
    let ehead = new ListNode(0);
    ehead.next = head;
    let prev = ehead;  // 第一个结点
    while(head) {
        let end = prev;
        for(let i = 0; i < k; ++i) {
            end = end.next;
            if(!end) return ehead.next;
        }
        let temp = end.next;
        [head, end] = reverseList(head, end);   
        // 返回的是反转过后的这个链表head & end
        prev.next = head;
        prev = end;
        end.next = temp;
        head = end.next;
    }
    return ehead.next;
};
```
### 完整代码
```js
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
 
var reverseList = function(s, e) {
    let prev = e.next;
    let nowv = s;
    while(prev != e) {
        let temp = nowv.next;
        nowv.next = prev;
        prev = nowv;
        nowv = temp;
    }
    return [e, s];
}
/**
 * @param {ListNode} head
 * @param {number} k
 * @return {ListNode}
 */
var reverseKGroup = function(head, k) {
    let ehead = new ListNode(0);
    ehead.next = head;
    let prev = ehead;  // 第一个结点
    while(head) {
        let end = prev;
        for(let i = 0; i < k; ++i) {
            end = end.next;
            if(!end) return ehead.next;
        }
        let temp = end.next;
        [head, end] = reverseList(head, end);   
        // 返回的是反转过后的这个链表head & end
        prev.next = head;
        prev = end;
        end.next = temp;
        head = end.next;
    }
    return ehead.next;
};
```

---
# day2

#算法/滑动窗口 #数据结构/二叉树

day2题目：[14. 最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/)、[3. 无重复字符的最长子串](https://leetcode-cn.com/problems/lru-cache/)、[124. 二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：字符串、滑动窗口、二叉树，难度仍为简单、中等、困难。

<!-- more -->

## 14. 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

示例 1：

>输入：strs = ["flower","flow","flight"]

输出："fl"

示例 2：

> 输入：strs = ["dog","racecar","car"]

输出：""

解释：输入不存在公共前缀。

### 思路

这道题思路不用多说了吧（）代码一看就懂

### 完整代码

```js

/**

 * @param {string[]} strs

 * @return {string}

 */

var longestCommonPrefix = function(strs) {

    let ans = "";

    let len = strs.length;

    for(let i = 0; strs[0][i]; ++i) {

        let ch = strs[0][i];

        if(!ch) return ans;

        for(let j = 1; j < len; ++j) {

            if(!strs[j][i] || strs[j][i] != ch) return ans;

        }    

        ans = ans+ch;

    }

    return ans

};

```
## 3. 无重复字符的最长子串
给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。

> 示例 1:
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

> 示例 2:
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

> 示例 3:
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

### 思路
剑指offer的时候就做过，滑动窗口，进入窗口的字符若为重复（用hash存判断）的则将左侧窗口滑至该字符（并将hash中相应的key删除）同时记录长度，再继续滑动。

### 完整代码
```js
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    let len = s.length;
    if(len == 0) return 0;
    let m = new Map();
    let [l, r] = [0, 0];
    let ans = 0;
    while(r < len) {
        while(m.has(s[r]))    // 出现过
            m.delete(s[l++]);   // 删除最左边的元素 直至出现过的元素消失
        m.set(s[r], r);
        ++r;
        ans = Math.max(ans, r-l);
    }
    return ans;
};
```
## 124. 二叉树中的最大路径和
**路径** 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点。

**路径和** 是路径中各节点值的总和。

给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。
> 输入：root = [1,2,3]
输出：6
解释：最优路径是 2 -> 1 -> 3 ，路径和为 2 + 1 + 3 = 6

> 输入：root = [-10,9,20,null,null,15,7]
输出：42
解释：最优路径是 15 -> 20 -> 7 ，路径和为 15 + 20 + 7 = 42

### 思路

最大路径和，每个结点的最大路径和等于其左子节点最大路径和+root+右子节点最大路径和，考虑到有负值存在，则路径和有可能小于0，所以需取路径和与0中较大的那个。需要一个maxPath函数 返回左右路径中较大的那个路径和加上根节点值，并在沿途更新ans的最大值。

### 完整代码

```js
/**
 * @param {TreeNode} root
 * @return {number}
 */
var maxPathSum = function(root) {
    let ans = -Infinity;
    var maxPath = function(root) {
        if(!root) return 0;
        let [lp, rp] = [Math.max(maxPath(root.left), 0), Math.max(maxPath(root.right), 0)];
        ans = Math.max(ans, lp+rp+root.val);
        return Math.max(lp, rp) + root.val;
    }
    maxPath(root)
    return ans;
};
```

今天的题目码量倒是很少~

---

# day3

#数据结构/链表 #数据结构/二叉树    

day3题目：206. 反转链表、199. 二叉树的右视图、bytedance-016. 16. 最短移动距离

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：链表、二叉树，难度仍为简单、中等、困难（字节：重新定义简单）。

<!-- more -->

## 206. 反转链表
给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。

>示例 1：
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]

> 示例 2：
输入：head = [1,2]
输出：[2,1]

> 示例 3：
输入：head = []
输出：[]


### 思路

day1做的里边就有这题的思路，也不用多说了

### 完整代码

```js
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var reverseList = function(head) {
    let prev = null;
    let nowv = head;
    while(nowv) {
        let temp = nowv.next;
        nowv.next = prev;
        prev = nowv;
        nowv = temp;
    }
    return prev;
}
```


## 199. 二叉树的右视图
给定一个二叉树的 根节点 root，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。
> 示例1:
输入: [1,2,3,null,5,null,4]
输出: [1,3,4]

>示例 2:
输入: [1,null,3]
输出: [1,3]

>示例 3:
输入: []
输出: []


### 思路

这才是简单题吧啊喂，bfs就完事了，先右后左，高度若是小于maxh则不放入答案数组

### 完整代码

```js
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var rightSideView = function(root) {
    if(!root) return [];
    let maxh = 0;
    let ans = [];
    function bfs(root, h) {
        if(!root) return;
        if(h > maxh) {
            maxh = h;
            ans.push(root.val);
        }
        bfs(root.right, h+1);
        bfs(root.left, h+1);
    }
    bfs(root, 1);
    return ans;
};
```
## bytedance-016. 16. 最短移动距离

给定一棵 n 个节点树。节点 1 为树的根节点，对于所有其他节点 i，它们的父节点编号为 floor(i/2) (i 除以 2 的整数部分)。在每个节点 i 上有 a[i] 个房间。此外树上所有边均是边长为 1 的无向边。
树上一共有 m 只松鼠，第 j 只松鼠的初始位置为 b[j]，它们需要通过树边各自找到一个独立的房间。请为所有松鼠规划一个移动方案，使得所有松鼠的总移动距离最短。

输入：
- 输入共有三行。
- 第一行包含两个正整数 n 和 m，表示树的节点数和松鼠的个数。
- 第二行包含 n 个自然数，其中第 i 个数表示节点 i 的房间数 a[i]。
- 第三行包含 m 个正整数，其中第 j 个数表示松鼠 j 的初始位置 b[j]。
输出：
- 输出一个数，表示 m 只松鼠各自找到独立房间的最短总移动距离。
示例：


> 输入：
     5 4
     0 0 4 1 1
     5 4 2 2
输出：4
解释：前两只松鼠不需要移动，后两只松鼠需要经节点 1 移动到节点 3
提示：

对于 30% 的数据，满足 n,m <=100。
对于 50% 的数据，满足 n,m <=1000。
对于所有数据，满足 n,m<=100000，0<=a[i]<=m, 1<=b[j]<=n。

### 思路

字节，重新定义简单题！还是看评论才有的思路QAQ
注意下标从1开始...
读入房间数的时候，记录总房间数sum，用cnt记录未被使用房间数，读入松鼠时将其所处节点的a[b]减一，后续从最低层开始遍历，遇到松鼠将其往上走，遇到房间且还有需求时将房间往上走（但是只能解决自上而下的最短路径，等官方题解了。）
ps:开不开快读影响很大！
pps:测试用例好像很弱，所以估计还有bug

### 完整代码
```cpp
#include <iostream>
#include <cmath>
#include <algorithm>
using namespace std;
const int maxn = 100005;
int n, m, sum, cnt;
int a[maxn], b;
int ans;
int main() {
    ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
    cin >> n >> m;
    for(int i = 1; i <= n; ++i) {
        cin >> a[i];
        sum += a[i];   //总房间数
    }
    for(int i = 1; i <= m; ++i) {
        cin >> b;
        --a[b];  // 占用该房间，可能使其为
    }
    cnt = sum-m;    // 剩余未被使用房间数
    for(int i = n; i > 1; --i) {
        if(!a[i]) continue;
        if(a[i] > 0 && cnt > 0) {  // 该房间还有用
            int nowh = a[i];
            if(cnt > nowh) cnt -= nowh;
            else {
                int cost = nowh-cnt;
                cnt -= cost;
                a[i/2] += cost;
                ans += cost;
            }
        } else {    // 松鼠 往上走
            int cost = abs(a[i]);
            ans += cost;
            a[i/2] += a[i];
            a[i] = 0;
        }
    }
    cout << ans << endl;
    return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/64d0c97f77624818bed3d56e5669386a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5L2ZY29z,size_20,color_FFFFFF,t_70,g_se,x_16)


---

# day4

#算法/单调栈 #算法/双指针 

day4题目：[1. 两数之和](https://leetcode-cn.com/problems/two-sum/)、[15. 三数之和](https://leetcode-cn.com/problems/3sum/)、[42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

今日知识点：数组、双指针、单调栈，难度仍为简单、中等、困难

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)


<!-- more -->

## 1. 两数之和
给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

> 示例 1：
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
### 思路
做烂了都,hash存一下出现过的下标，出现过则返回这一对
### 完整代码
```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    let m = new Map();
    let len = nums.length;
    for(let i = 0; i < len; ++i) {
        let nowp = nums[i];
        if(m.has(target-nowp)) {
            return [m.get(target-nowp), i];
        } else m.set(nowp, i);
    }
};
```
## 15. 三数之和
给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

> 示例 1：
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]

### 思路
有个小坑，js例排序不指定排序规则函数的话默认是以**字符串**形式**从小到大**排序的~
遍历+双指针防止重复，重复的就跳过。
### 完整代码
```js
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
var threeSum = function(nums) {
    nums.sort((a, b) => a-b);
    let len = nums.length;
    let ans = []
    for(let i = 0; i < len; ++i) {
        if(i > 0 && nums[i] === nums[i-1]) continue;        // 不重复枚举
        let tar = -nums[i];
        let r = len-1;
        for(let l = i+1; l < len; ++l) {
            if(l > i+1 && nums[l] === nums[l-1]) continue;   // 不重复枚举
            while(l < r && nums[l]+nums[r] > tar) --r;
            if(l === r) break;
            if(nums[l]+nums[r] === tar) {
                ans.push([nums[i], nums[l], nums[r]]);
            }
        }
    }
    return ans;
};
```

## 42. 接雨水
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

>示例 1：
![在这里插入图片描述](https://img-blog.csdnimg.cn/c9ea51f7298b4b69a8a39483b8e20af9.png)
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 

### 思路
单调栈，维护一个单调递减的栈(存的是下标)，遍历高度数组 `h[r]`，每次与栈顶比较，若栈不为空且 `h[r]` 大于栈顶，则将栈顶出栈为 `top` ，若此时栈为空则说明前面没有能挡住水的地方故不需计算结束，将 `h[r]` 入栈；若不为空则将此时的栈顶元素即 `l` 与当前元素 `r` 比较高度，取较矮的那个（`min(height[l], height[r])`）减去  `h[top]` 作为高度，宽度则为 `r-l-1`，
### 完整代码
```js
/**
 * @param {number[]} height
 * @return {number}
 */
var trap = function(height) {
    let s = []; // 存下标
    let ans = 0;
    let l = 0;
    let n = height.length;
    for(let r = 0; r < n; ++r) {
        let size = s.length;
        while(size != 0 && height[r] > height[s[size-1]]) { // 跟栈顶比较
            let top = s.pop();  // 栈顶出
            --size;
            if(size == 0) break;
            l = s[size-1];  // 为栈顶底下的一个元素
            ans += (r-l-1)*(Math.min(height[l], height[r]) - height[top]);  // 宽*高
        }
        s.push(r);  // 小于等于栈顶，则进栈
    }
    return ans;
};
```


---

# day5

#数据结构/快速排序 #数据结构/链表 

day5题目：[ 7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)、[215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)、[23. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：链表、数组、快排，难度为中等、中等、困难

<!-- more -->

## 7. 整数反转

给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 [−231,  231 − 1] ，就返回 0。

假设环境不允许存储 64 位整数（有符号或无符号）。

> 示例 1：
> 输入：x = 123
> 输出：321

### 思路

[整数反转-题解](https://leetcode-cn.com/problems/reverse-integer/solution/zheng-shu-fan-zhuan-by-leetcode-solution-bccn/)

看了题解才晓得，是数学题嗷，通过不等式变换
![在这里插入图片描述](https://img-blog.csdnimg.cn/ab6f52e151fa4b2981ff5a239430c580.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5L2ZY29z,size_20,color_FFFFFF,t_70,g_se,x_16)
这我是真没想到（）
取巧一些的话就用long long存防止溢出

### 完整代码

```js
class Solution {
public:
    int reverse(int x) {
        int ans = 0;
        while(x) {
            if(ans > INT_MAX/10 || ans < INT_MIN/10) return 0;
            ans = (ans*10) + x%10;
            x /= 10;
        }
        return ans;
    }
};
```

## 215. 数组中的第K个最大元素

给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

示例 1:
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5

示例 2:
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4

### 思路

取巧一点就直接调sort排好序之后返回。
否则的话手写快排咯~~具体可以看排序那期的学习笔记：[数据结构学习笔记＜8＞ 排序](https://blog.csdn.net/qq_45890533/article/details/108246044)

### 完整代码

暴力（时间反而很快

```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end(), greater<int>());
        return nums[k-1];
    }
};
```

手写快排，主元在s~e中随机抽取以避免最坏情况。

```cpp
class Solution {
public:
    int quickSort(vector<int>& nums, int k, int s, int e) {
        int idx = rand()%(e-s+1)+s;
        int m = nums[idx];    // 主元为s~e中随机的一个
        int l = s, r = e;
        nums[idx] = nums[l];
        while(l != r) {
            while(l < r && nums[r] <= m) --r; // 遇到右边有比他大的才停下
            nums[l] = nums[r];
            while(l < r && nums[l] >= m) ++l;
            nums[r] = nums[l];
        }// 处理完后主元m到了正确的位置nums[l]，左边元素都比他大，右边元素都比他小
        nums[l] = m;
        if(l == k-1) return nums[l];
        else if(l > k-1) return quickSort(nums, k, s, l-1);    // 快排左边部分
        else return quickSort(nums, k, l+1, e);
    }
    int findKthLargest(vector<int>& nums, int k) {
        int len = nums.size();
        return quickSort(nums, k, 0, len-1);
    }
};
```

## 23. 合并K个升序链表

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

> 示例 1：
> 输入：lists = [[1,4,5],[1,3,4],[2,6]]
> 输出：[1,1,2,3,4,4,5,6]
> 解释：链表数组如下：
> [
> 1->4->5,
> 1->3->4,
> 2->6
> ]
> 将它们合并到一个有序链表中得到。
> 1->1->2->3->4->4->5->6

### 思路

day1就写过合并两个有序链表，这次的只需要暴力就好了。
但要优化的话，可以使用优先队列每次链表头的结点，取出最顶上的结点放至答案链表后。

### 完整代码

暴力版

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
 
var mergeList = function(list1, list2) {
    let p1 = list1;
    let p2 = list2;
    if(!p1 && !p2) return null;
    else if(!p1) return p2;
    else if(!p2) return p1;
    else if(p1.val <= p2.val) {
        p1.next = mergeList(p1.next, p2);
        return p1;
    } else {
        p2.next = mergeList(p2.next, p1);
        return p2;
    }
}
/**
 * @param {ListNode[]} lists
 * @return {ListNode}
 */
var mergeKLists = function(lists) {
    let ans = null;
    let len = lists.length;
    for(let i = 0; i < len; ++i) {
        ans = mergeList(ans, lists[i]);
    }
    return ans;
};
```

优先队列版

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    struct Node {
        int val;
        ListNode *nowp;
        bool operator<(const Node &p1) const {
            return val > p1.val;
        }
    };
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        priority_queue<Node> q;
        ListNode head;
        ListNode *tail = &head;
        for(auto h: lists) {
            if(h) q.push({h->val, h});
        }
        while(!q.empty()) {
            Node nowv = q.top();
            q.pop();
            tail->next = nowv.nowp;
            tail = tail->next;
            ListNode* nexp = nowv.nowp->next;
            if(nexp) q.push({nexp->val, nexp});
        }
        return head.next;
    }
};
```


---

# day6 

#算法/背包 #算法/二分 

day6题目：[33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)、[54. 螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)、[bytedance-006. 夏季特惠](https://leetcode-cn.com/problems/tJau2o/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：二分、模拟、01背包，难度为中等、中等、字节の简单
<!-- more -->

## 33. 搜索旋转排序数组

整数数组 nums 按升序排列，数组中的值 互不相同 。

在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了 旋转，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标 从 0 开始 计数）。例如， [0,1,2,4,5,6,7] 在下标 3 处经旋转后可能变为 [4,5,6,7,0,1,2] 。

给你 旋转后 的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的下标，否则返回 -1 。

**示例 1：**
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4

**示例 2：**
输入：nums = [4,5,6,7,0,1,2], target = 3
输出：-1

**示例 3：**
输入：nums = [1], target = 0
输出：-1
### 思路
两次二分，第一次查找分界点如例1、2中的0（[153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)），分界点左侧数一定都比右侧大且为升序，故根据target与第一个数比较判断在左侧找还是右侧找。注意特判一下nums[n-1] > nums[0]的情况（即旋转多次回到原数组的情况）
### 代码
```cpp
class Solution {
public:
    int binarySearch(vector<int>& nums, int target, int s, int e) {
        int l = s, r = e;
        while(l <= r) { 
            int mid = (l+r)>>1;
            if(nums[mid] == target) return mid;
            if(nums[mid] < target) l = mid+1;
            else r = mid-1;
        }
        return -1;
    }
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        int l = 0, r = n-1;
        int idx;
        if(nums[r] > nums[l]) idx = n-1;
        else {
            while(l < r) { // 找分界点 如0
                int mid = (l+r)>>1;
                if(nums[mid] == target) return mid;
                if(nums[mid] > nums[l]) l = mid;
                else r = mid;
            }
            idx = l;
        }
        if(target < nums[0])
            return binarySearch(nums, target, idx+1, n-1);
        else 
            return binarySearch(nums, target, 0, idx);
    }
};
```
## 54. 螺旋矩阵
给你一个 m 行 n 列的矩阵 matrix ，请按照 顺时针螺旋顺序 ，返回矩阵中的所有元素。

**示例 1：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/eeb6eacb50c44affa8389b18a5d292fc.png)
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
### 思路
某蓝桥杯经典真题稍稍变形- -
模拟每次就一直往右下左上的顺序走就行了
### 代码
```js
/**
 * @param {number[][]} matrix
 * @return {number[]}
 */
var spiralOrder = function(matrix) {
    let m = matrix.length;
    let n = matrix[0].length;
    let sum = n*m;
    let vis = -200;
    let ans = [matrix[0][0]];
    matrix[0][0] = vis;
    let [i, j] = [0, 0];
    let cnt = 1;
    while(cnt < sum) {
        while(j+1 < n && matrix[i][j+1] != vis) {   // 往右走
            ans.push(matrix[i][j+1]);
            matrix[i][++j] = vis;
            ++cnt;
        }
        while(i+1 < m && matrix[i+1][j] != vis) {  // 往下走
            ans.push(matrix[i+1][j]);
            matrix[++i][j] = vis;
            ++cnt;
        }
        while(j-1 >= 0 && matrix[i][j-1] != vis) {  // 往左走
            ans.push(matrix[i][j-1]);
            matrix[i][--j] = vis;
            ++cnt;
        }
        while(i-1 >= 0 && matrix[i-1][j] != vis) {  // 往上走
            ans.push(matrix[i-1][j]);
            matrix[--i][j] = vis;
            ++cnt;
        }
    }
    return ans;
};
```
## bytedance-006. 夏季特惠
某公司游戏平台的夏季特惠开始了，你决定入手一些游戏。现在你一共有X元的预算，该平台上所有的 n 个游戏均有折扣，标号为 i 的游戏的**原价a[i]**元，**现价**只要 **b[i]** 元（也就是说该游戏可以**优惠 a[i]-b[i]** 元）并且你购买该游戏能获得快乐值为 w[i] ，由于优惠的存在，你可能做出一些冲动消费导致最终买游戏的总费用超过预算，但只要满足**获得的总优惠金额**不低于**超过预算的总金额**，那在心理上就不会觉得吃亏。现在你希望在心理上不觉得吃亏的前提下，获得尽可能多的快乐值。
- 输入
	- 第一行包含两个数 n 和 x 。
	- 接下来 n 行包含每个游戏的信息，原价 ai , 现价 bi，能获得的快乐值为 wi 。
- 输出
	- 输出一个数字，表示你能获得的最大快乐值。

**示例 1：**
输入：
     4 100
     100 73 60
     100 89 35
     30 21 30
     10 8 10
输出：100
解释：买 1、3、4 三款游戏，获得总优惠 38 元，总金额 102 元超预算 2 元，满足条件，获得 100 快乐值。

**示例 2：**
输入：
     3 100
     100 100 60
     80 80 35
     21 21 30
输出：60
解释：只能买下第一个游戏，获得 60 的快乐值。
### 思路
经典01背包问题，难点在于如何转换成背包问题qwq，看了题解才晓得
背包问题可看这篇博客：[01背包问题详解（浅显易懂）](https://blog.csdn.net/Iseno_V/article/details/100001133)
由题意易知（其实想了半天）每买一个游戏都会**使预算加上该游戏的优惠价**，再**从预算里减掉现价**，那么设优惠价为dis，若当前游戏优惠价 >= 现价的话买了是绝对不亏的（预算反而增加了），否则就将该游戏视作等待进行01背包的一员（取或不取）
还要注意下数据范围，w贼大，所以要用long long
### 完整代码
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
typedef long long ll;
const int maxn = 505;
const int maxm = 3e5;
int n, x, a, b, c, cnt;  // x为预算
int v[maxn];// 容量
ll ans, w[maxn];
int main() {
    cin >> n >> x;
    for(int i = 0; i < n; ++i) {
        cin >> a >> b >> c;    // 原价 现价 快乐值
        int dis = a-b;    // 优惠 每买一个游戏都会使预算加上优惠价，再从预算里减掉现价。
        if(dis > b) { // 所以优惠价大于现价的话一定买
            x += dis-b;
            ans += c;
        } else {
            v[cnt] = b-dis;
            w[cnt++] = c;
        }
    }
    vector<ll> dp(maxm, 0);
    for(int i = 0; i < cnt; ++i) {
        for(int j = x; j >= v[i]; --j) {
            dp[j] = max(dp[j], dp[j-v[i]]+w[i]);
        }
    }
    ans += dp[x]; 
    cout << ans << endl;
    return 0;
}
```

---

# day7 

#算法/动态规划 

day7题目：[53. 最大子数组和](https://leetcode-cn.com/problems/maximum-subarray/)、[152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)、[41. 缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：数组、动态规划、哈希，难度为简单、中等、困难
<!-- more -->

## 53. 最大子数组和
给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

子数组 是数组中的一个连续部分。

给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

子数组 是数组中的一个连续部分。

**示例 1：**

输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。


### 思路
经典做法就是，每次如果加上去之后当前和<0了，那么这个只会让后面的变小所以直接将nowsum置为0。
### 代码
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int ans = -100000;
        int nowsum = 0;
        int n = nums.size();
        for(int i = 0; i < n; ++i) {
            nowsum += nums[i];
            if(nowsum > ans) ans = nowsum;
            if(nowsum < 0) nowsum = 0;
        }
        return ans;
    }
};
```
## 152. 乘积最大子数组
给你一个整数数组 `nums` ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

测试用例的答案是一个 **32-位** 整数。

**子数组** 是数组的连续子序列。 

**示例 1:**

```
输入: nums = [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

**示例 2:**

```
输入: nums = [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```

### 思路
> 当前位置如果是一个负数的话，那么我们希望以它前一个位置结尾的某个段的积也是个负数，这样就可以负负得正，并且我们希望这个积尽可能「负得更多」，即尽可能小。如果当前位置是一个正数的话，我们更希望以它前一个位置结尾的某个段的积也是个正数，并且希望它尽可能地大。

好，我觉得官方题解说的很清楚，主要就这一句话

### 代码
```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxProduct = function(nums) {
    let minp, maxp, ans;
    minp = maxp = ans = nums[0];
    let n = nums.length;
    for(let i = 1; i < n; ++i) {
        let [tmax, tmin] = [maxp, minp];
        maxp = Math.max(tmax*nums[i], tmin*nums[i], nums[i]);
        minp = Math.min(tmax*nums[i], tmin*nums[i], nums[i]);
        ans = Math.max(ans, maxp)
    }
    return ans;
};
```


## 41. 缺失的第一个正数

给你一个未排序的整数数组 `nums` ，请你找出其中没有出现的最小的正整数。

请你实现时间复杂度为 `O(n)` 并且只使用常数级别额外空间的解决方案。

**示例 1：**

```
输入：nums = [1,2,0]
输出：3
```

**示例 2：**

```
输入：nums = [3,4,-1,1]
输出：2
```

**提示：**

- 1 <= nums.length <= 5 * 10^5^
- -2^31^ <= nums[i] <= 2^31^ - 1

### 思路

一开始的思路，就是非常暴力的hash存，遇到不在的就直接返回...这就很不困难题，也不满足题意（

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var firstMissingPositive = function(nums) {
    let m = new Map();
    for(let i of nums)
        m.set(i, 1);
    let len = nums.length;
    for(let i = 1; i <= 1000005; ++i) {
        if(!m.has(i)) return i;
    }
};
```

看完题解：哦~由于我们只在意 [1, N] 中的数，因此我们可以先对数组进行遍历，把不在 [1, N][1,*N*] 范围内的数修改成任意一个大于 N的数（例如 N+1）。这样一来，**数组中的所有数就都是正数了**，因此我们就可以将「标记」表示为「负号」，这样一来，每次遇到的有可能是负数但是其绝对值就是原来的数，遍历完后，若数组中每一个数都为负数则答案为N+1，否则就是第一个正数的位置。

### 代码
```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var firstMissingPositive = function(nums) {
    let n = nums.length;
    for(let i = 0; i < n; ++i) {
        if(nums[i] <= 0) 
            nums[i] = n+1;
    } 
    for(let i = 0; i < n; ++i) {
        let nowp = Math.abs(nums[i]);
        if(nowp <= n) {
            nums[nowp-1] = -Math.abs(nums[nowp-1]);
        }
    } 
    for(let i = 0; i < n; ++i) {
        if(nums[i] > 0) 
            return i+1;
    } 
    return n+1;
};
```

---
title: 冲刺春招-精选笔面试66题大通关day8
link: coding-train/leetcode/bytedance/bytedance-day8
catalog: true
subtitle: 今日知识点：栈、深搜/广搜、滑动窗口，难度为简单、中等、困难
 
# day8

#算法/滑动窗口 #算法/搜索 #算法/滑动窗口  #数据结构/栈

day8题目：[20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)、[200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)、[76. 最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：栈、深搜/广搜、滑动窗口，难度为简单、中等、困难
<!-- more -->

## 20. 有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
 
 **示例 1：**
输入：s = "()"
输出：true

**示例 2：**
输入：s = "()[]{}"
输出：true

### 思路
典中典堆栈判断有效括号，遇到左括号将其入栈，遇到右括号跟栈顶比较，若不匹配或者栈为空则返回false，全部比较完都没问题且栈为空了则返回true。
### 代码
```js
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function(s) {
    let stack = [];
    for(let i = 0; i < s.length; ++i) {
        if(s[i] == '{' || s[i] == '(' || s[i] == '[') {
            stack.push(s[i]);
        } else {
            if(stack.length == 0) return false;
            let top = stack.length-1;
            if(s[i] == '}' && stack[top] == '{') stack.pop();
            else if(s[i] == ')' && stack[top] == '(') stack.pop();
            else if(s[i] == ']' && stack[top] == '[') stack.pop();
            else return false;
        }
    }
    return stack.length == 0;
};
```
## 200. 岛屿数量
给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

**示例 1：**
输入：
```
grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
```
输出：`1`

**示例 2：**
输入：
```
grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
```
输出：`3`
 

**提示：**
```
m == grid.length
n == grid[i].length
1 <= m, n <= 300
grid[i][j] 的值为 '0' 或 '1'
```
### 思路
一眼题，一眼就用dfs就行了，标记数组vis表示是否访问过，对每个没访问过的1进行dfs并++ans。也可以用并查集做。

### 代码
```cpp
class Solution {
public:
    vector<vector<bool> > vis;
    vector<vector<char> > g;
    int n, m;
    int dx[4] = {-1, 1, 0, 0};// 上下左右
    int dy[4] = {0, 0, -1, 1};// 上下左右
    Solution():vis(305, vector<bool>(305, false)) {}
    void dfs(int i, int j) {
        if(vis[i][j]) return;
        vis[i][j] = true;
        for(int k = 0; k < 4; ++k) {
            int nowx = i+dx[k];
            int nowy = j+dy[k];
            if(nowx >= 0 && nowx < m && nowy >= 0 && nowy < n && g[nowx][nowy] == '1') 
                dfs(nowx, nowy); 
        }
    }
    int numIslands(vector<vector<char>>& grid) {
        g = grid;
        m = grid.size();
        n = grid[0].size();
        int ans = 0;
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(vis[i][j] || grid[i][j] == '0') continue;
                dfs(i, j);
                ++ans;
            }
        }
        return ans;
    }
};
```


## 76. 最小覆盖子串


给你一个字符串 s 、一个字符串 t 。返回 s 中涵盖 t 所有字符的最小子串。如果 s 中不存在涵盖 t 所有字符的子串，则返回空字符串 "" 。

**注意：**
对于 t 中重复字符，我们寻找的子字符串中该字符数量必须不少于 t 中该字符数量。
如果 s 中存在这样的子串，我们保证它是唯一的答案。

**示例 1：**
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"

**示例 2：**
输入：s = "a", t = "a"
输出："a"

**示例 3:**
输入: s = "a", t = "aa"
输出: ""
解释: t 中两个字符 'a' 均应包含在 s 的子串中，
因此没有符合条件的子字符串，返回空字符串。
 

**提示：**
1 <= s.length, t.length <= 105
s 和 t 由英文字母组成
### 思路
滑动窗口，首先遍历一遍t（t[i]为该字符的ASCII码），将所有出现过的m[t[i]]--，这样遍历完后，m中小于0的就是需要满足的字符数，然后双指针开始滑动窗口，使用cnt作为判断，若cnt为t中字符数则满足条件，将左指针尽可能往右移动。
### 代码
```js
/**
 * @param {string} s
 * @param {string} t
 * @return {string}
 */
var minWindow = function(s, t) {
    if(s.length < t.length) return '';
    let m = new Array(130);
    m.fill(0);
    for(let i = 0; i < t.length; ++i) 
        --m[t.charCodeAt(i)];
    let ans = '';
    let cnt = 0;    // 满足字符个数
    for(let l = 0, r = 0; r < s.length; ++r) {
        let k = s.charCodeAt(r);
        if(m[k] < 0) ++cnt;
        ++m[k];
        while(cnt == t.length && m[s.charCodeAt(l)] > 0) 
            m[s.charCodeAt(l++)]--;
        if(cnt == t.length && (ans == '' || r-l+1 <= ans.length) ) {
            ans = s.substring(l, r+1);
        }
    }
    return ans;
};
```

---

# day9

#数据结构/层序遍历  #数据结构/二叉树 

day9题目：[105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)、[103. 二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)、[bytedance-010. 数组组成最大数](https://leetcode-cn.com/problems/9nsGSS/)
https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/
学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：树、层序遍历、处理输入输出？（bushi），难度为中等、中等、字节の简单
<!-- more -->

## 105. 从前序与中序遍历序列构造二叉树
给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。

**示例 1:**
输入: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
输出: [3,9,20,null,null,15,7]

**示例 2:**
输入: preorder = [-1], inorder = [-1]
输出: [-1]
 
> 提示:
1 <= preorder.length <= 3000
inorder.length == preorder.length
-3000 <= preorder[i], inorder[i] <= 3000
preorder 和 inorder 均 **无重复** 元素
inorder 均出现在 preorder
preorder **保证** 为二叉树的前序遍历序列
inorder **保证** 为二叉树的中序遍历序列
### 思路
也是老题了，因为无重复，所以找出根节点在中序遍历中的下标，算出左右子节点数量，切分递归构建即可。
### 代码
```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {number[]} preorder
 * @param {number[]} inorder
 * @return {TreeNode}
 */
var buildTree = function(preorder, inorder) {
    if(!preorder || !inorder) return null;
    let root = new TreeNode(preorder[0]);
    let idx = inorder.findIndex((v) => (v == preorder[0]));
    let len = preorder.length;
    let lnum = idx;
    let rnum = lnum == -1 ? len-1 : len-1-lnum;
    if(lnum > 0) 
        root.left = buildTree(preorder.slice(1, lnum+1), inorder.slice(0, lnum+1));
    if(rnum > 0) 
        root.right = buildTree(preorder.slice(len-rnum), inorder.slice(len-rnum));
    return root;
};
```
## 103. 二叉树的锯齿形层序遍历
给你二叉树的根节点 root ，返回其节点值的 锯齿形层序遍历 。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

**示例 1：**
输入：root = [3,9,20,null,null,15,7]
输出：[[3],[20,9],[15,7]]

**示例 2：**
输入：root = [1]
输出：[[1]]

**示例 3：**
输入：root = []
输出：[]

>提示：
树中节点数目在范围 [0, 2000] 内
-100 <= Node.val <= 100

### 思路
层序遍历将结果存至level数组中，然后反转奇数层的结果，简单粗暴の解法，记得特判一下root为空的情况
### 代码
```js
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var zigzagLevelOrder = function(root) {
    let level = []  // 每一层的
    let q = []; // 队列
    if(!root) return [];
    q.push(root);
    let nowl = [];
    while(q.length != 0) {
        let nodelist = q.slice(0);
        q = [];
        for(let v of nodelist) {
            nowl.push(v.val);
            if(v.left) q.push(v.left);
            if(v.right) q.push(v.right);
        }
        level.push(nowl);
        nowl = [];
    }
    if(nowl.length != 0) level.push(nowl);
    for(let i = 0; i < level.length; ++i) {
        if(i % 2 == 1) level[i].reverse();
    }
    return level;
};
```
## bytedance-010. 数组组成最大数
给定一组非负整数，重新排列它们的顺序使之组成一个最大的整数。

**示例 1：**
输入：[10,1,2]
输出：2110

**示例 2：**
输入：[3,30,34,5,9]
输出：9534330

ACM模式！！
### 思路
别问，问就是前些天面试刚碰到过，蚌，终生难忘，重载一下排序规则即可。
天哪真有人拿acm模式放核心输出的样例吗，输入输出还是看评论学的，蚌。`var input = require('fs').readFileSync('/dev/stdin', 'utf8').trim().split('\n')[0];
`，字符串处理还是js来吧，lei了。

重载排序顺序，利用字符串排序规则，重载小于号a < b 变为a+b > b+a（字符串形式），这样的话比如30， 21 就是3021 > 2130，12，2就是212>12，990，9就是9990 > 9909
### 代码
```js
var input = require('fs').readFileSync('/dev/stdin', 'utf8').trim().split('\n')[0];
input = input.slice(1, input.length-1).split(',');
var ans = input.sort((a, b) => ((b+a) - (a+b))).join('');
console.log(ans)
```

---
title: 冲刺春招-精选笔面试66题大通关day10
link: coding-train/leetcode/bytedance/bytedance-day10
catalog: true
subtitle: 今日知识点：树的遍历、字符串、栈，难度为简单、中等、中等
date: 2022-03-17 19:10:22
cover: img/header_img/milky-way-over-bow-lake-alberta-canada-wallpaper-for-1920x1080-63-873.jpg
tags:
- leetcode
- 树
- 字符串
categories:
- [题目记录, 字节校园]
---



---

# day10

#数据结构/层序遍历  #数据结构/二叉树 #数据结构/中序遍历 #数据结构/栈 

day10题目：[94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)、[102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)、[394. 字符串解码](https://leetcode-cn.com/problems/decode-string/)


学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：树的遍历、字符串、栈，难度为简单、中等、中等

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day9](https://ysx.cosine.ren/cn/%E5%86%B2%E5%88%BA%E6%98%A5%E6%8B%9B-%E7%B2%BE%E9%80%89%E7%AC%94%E9%9D%A2%E8%AF%95%2066%20%E9%A2%98%E5%A4%A7%E9%80%9A%E5%85%B3%20day9/)
<!-- more -->
## 94. 二叉树的中序遍历
给定一个二叉树的根节点 `root` ，返回它的 **中序** 遍历。

**示例 1：**

```
输入： root = [1,null,2,3]
输出： [1,3,2]
```

**示例 2：**

```
输入： root = []
输出： []
```

### 思路
easy题，中序遍历就是 左-根-右 ，前序就是根左右，后序就是左右根。
### 代码
```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    var ans = [];
    var inorder = function(rt) {
        if(!rt) return;
        inorder(rt.left);
        ans.push(rt.val);
        inorder(rt.right);
    }
    inorder(root);
    return ans;
};
```

## 102. 二叉树的层序遍历
给你二叉树的根节点 `root` ，返回其节点值的 **层序遍历** 。 （即逐层地，从左到右访问所有节点）。

**示例 1：**
```
输入： root = [3,9,20,null,null,15,7]
输出： [[3],[9,20],[15,7]]
```

**示例 2：**

```
输入： root = [1]
输出： [[1]]
```

**示例 3：**

```
输入： root = []
输出： []
```
### 思路
比昨天的题简单，每一层的结点存至q然后用nodelist暂存当前队列中所有结点（即该层的结点）后将q置空，遍历nodelist将每一个节点的孩子节点放入q
### 代码
```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var levelOrder = function(root) {
    if(!root) return [];
    let ans = [];
    let nowlist = [];
    let q = [];
    q.push(root)
    while(q.length != 0) {
        let nodes = q.slice(0);
        q = [];
        for(let v of nodes) {
            nowlist.push(v.val);
            if(v.left) q.push(v.left);
            if(v.right) q.push(v.right);
        }
        ans.push(nowlist);
        nowlist = [];
    }
    return ans;
};
```

## 394. 字符串解码
给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: `k[encoded_string]`，表示其中方括号内部的 `encoded_string` 正好重复 `k` 次。注意 `k` 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 `k` ，例如不会出现像 `3a` 或 `2[4]` 的输入。

**示例 1：**

```
输入： s = "3[a]2[bc]"
输出： "aaabcbc"
```

**示例 2：**

```
输入： s = "3[a2[c]]"
输出： "accaccacc"
```

**示例 3：**

```
输入： s = "2[abc]3[cd]ef"
输出： "abcabccdcdcdef"
```

**示例 4：**

```
输入： s = "abc3[cd]xyz"
输出： "abccdcdcdxyz"
```

**提示：**

-   `1 <= s.length <= 30`
-   `s` 由小写英文字母、数字和方括号 `'[]'` 组成
-   `s` 保证是一个 **有效** 的输入。
-   `s` 中所有整数的取值范围为 `[1, 300]`

### 思路
遍历，若为数字则过滤出来该数字作为k，用一个栈stack存左括号，遇到右括号且栈中左括号数量不为1时出栈，当栈中只有一个左括号时，将该当前tempStr字符串递归解码后返回，重复k次后拼接至答案ans，若为其他则正常存入。
### 代码
```js
/**
 * @param {string} s
 * @return {string}
 */
 var decodeString = function(s) {
    let stack = [];
    let ans = '';
    let len = s.length;
    for(let i = 0; i < len; ++i) {
        if(s[i] >= '0' && s[i] <= '9') {
            let tk = s[i++];
            while(i < len && s[i] >= '0' && s[i] <= '9')
                tk += s[i++];
            let k = parseInt(tk);
            let tempStr = '';
            stack.push(s[i++]);   // 放入'['
            while(i < len && (s[i] != ']' || stack.length != 1)) { // 若s[i] == ']' 并且 stack中只有一个时结束
                if(s[i] == '[') {
                    stack.push(s[i]);
                } else if(s[i] == ']') 
                    stack.pop();
                tempStr += s[i];
                ++i;
            }
            stack.pop();
            let decode = decodeString(tempStr); // 核心
            ans += `${decode.repeat(k)}`;
        } else ans += s[i];
    }
    return ans;
};
```

---

# day11

#算法/动态规划 #算法/模拟 

day11题目：[415. 字符串相加](https://leetcode-cn.com/problems/add-strings/)、[5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)、[72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：字符串、模拟、动态规划，难度为简单、中等、困难

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day10](https://juejin.cn/post/7076029414655393822)

## 415. 字符串相加
给定两个字符串形式的非负整数 `num1` 和`num2` ，计算它们的和并同样以字符串形式返回。

你不能使用任何內建的用于处理大整数的库（比如 `BigInteger`）， 也不能直接将输入的字符串转换为整数形式。

**示例 1：**

```
输入： num1 = "11", num2 = "123"
输出： "134"
```

**示例 2：**

```
输入： num1 = "456", num2 = "77"
输出： "533"
```

**示例 3：**

```
输入： num1 = "0", num2 = "0"
输出： "0"
```

**提示：**

-   `1 <= num1.length, num2.length <= 104`
-   `num1` 和`num2` 都只包含数字 `0-9`
-   `num1` 和`num2` 都不包含任何前导零
### 思路
flag为进位标志，从右侧开始往左走，若sum大于等于10则进位，非常典型的大数相加了。

### 代码

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
var addStrings = function(num1, num2) {
    let [len1, len2] = [num1.length, num2.length];
    let flag = 0;   // 进位标志
    let ans = '';
    for(let i = len1-1, j = len2-1; i >= 0 || j >= 0 || flag == 1; --i, --j) {
        let x, y;
        x = i >= 0? parseInt(num1[i]): 0;
        y = j >= 0? parseInt(num2[j]): 0;
        let sum = x+y+flag;
        if(sum >= 10) {
            ans += (sum%10);
            flag = 1;
        } else {
            ans += sum;
            flag = 0;
        }
    }
    return ans.split('').reverse().join('');
};
```
## 5. 最长回文子串
给你一个字符串 `s`，找到 `s` 中最长的回文子串。

**示例 1：**

```
输入： s = "babad"
输出： "bab"
解释： "aba" 同样是符合题意的答案。
```

**示例 2：**

```
输入： s = "cbbd"
输出： "bb"
```

**提示：**

-   `1 <= s.length <= 1000`
-   `s` 仅由数字和英文字母组成

### 思路
也是非常经典的做过好多遍的题了（起码做了有4次了- -），动态规划就行了，核心就是**dp[i][j] 表示i~j是否为回文串**，先遍历一遍，将dp[i][i]都置为true，dp[i][i-1]看情况置为true（前后相同），然后从长度为3开始dp。
###
```js
/**
 * @param {string} s
 * @return {string}
 */
var longestPalindrome = function(s) {
    let len = s.length;
    let dp = new Array(len).fill(0).map(v => (new Array(len).fill(0))); // JS初始化二维数组全为0
    let maxlen = 1;
    let ans = s[0];
    for(let i = 0; i < len; ++i) {
        dp[i][i] = 1;
        if(i > 0 && s[i-1] == s[i]) {
            dp[i-1][i] = 1;
            maxlen = 2;
            ans = s[i-1]+s[i];
        }
    }
    for(let k = 3; k <= len; ++k) {
        for(let l = 0, r = l+k-1; r < len; ++l, ++r) {
            if(s[l] == s[r] && dp[l+1][r-1] == 1) {
                dp[l][r] = 1;
                if(k > maxlen) {
                    maxlen = k;
                    ans = s.substring(l, r+1);
                }
            }
        }
    }
    return ans;
};
```

## 72. 编辑距离
给你两个单词 `word1` 和 `word2`， *请返回将 `word1` 转换成 `word2` 所使用的最少操作数*  。

你可以对一个单词进行如下三种操作：

-   插入一个字符
-   删除一个字符
-   替换一个字符

**示例 1：**

```
输入： word1 = "horse", word2 = "ros"
输出： 3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

**示例 2：**

```
输入： word1 = "intention", word2 = "execution"
输出： 5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

**提示：**

-   `0 <= word1.length, word2.length <= 500`
-   `word1` 和 `word2` 由小写英文字母组成
### 思路
动态规划，没想出来，看了题解。
dp[i][j] 表示word1中前i个字符变成word2中前j个字符最少需要多少步
- `i=0` 的时候（即第一行）为word2前j个数变为空字符串需要几步（自然是j步）
- `j=0` 时（即第一列）为word1前 `i` 个数变为空字符串需要几步
- 开始遍历，过程中若当前 `word1[i] == word2[j]`，则说明无需变化，`dp[i][j] = dp[i-1][j-1]; `
- 否则 `dp[i][j]`为`dp[i-1][j-1]`、`dp[i-1][j]`、 `dp[i][j-1])+1`中最小步数+1
    - 这里最小值若为 `dp[i-1][j-1]` 表示修改一个字符，`dp[i][j-1]` 表示往word2[j-1]后面添加一个字符，`dp[i-1][j]` 表示往word1[i-1]后面添加一个字符
### 代码
```js
/**
 * @param {string} word1
 * @param {string} word2
 * @return {number}
 */
var minDistance = function(word1, word2) {
    let [m, n] = [word1.length+1, word2.length+1];
    let dp = new Array(m).fill(0).map((v) => (new Array(n).fill(0)));
    for(let i = 0; i < m; ++i)
        dp[i][0] = i;   // word1前i个字符串变为''需要多少步
    for(let i = 0; i < n; ++i)
        dp[0][i] = i;   // word2前i个字符串变为''需要多少步
    for(let i = 1; i < m; ++i) {
        for(let j = 1; j < n; ++j) {
            if(word1[i-1] == word2[j-1])
                dp[i][j] = dp[i-1][j-1];
            else dp[i][j] = Math.min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1])+1;
        }
    }
    return dp[m-1][n-1];
};
```

---

# day12

#算法/动态规划 #算法/二分  

day12题目：[64. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)、[300. 最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)、[bytedance-004. 机器人跳跃问题](https://leetcode-cn.com/problems/yBGFyZ/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：数组、动态规划、二分，难度为中等、中等、字节の简单

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day11](https://juejin.cn/post/7076378813386457119)

## 64. 最小路径和
给定一个包含非负整数的 `m x n` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：** 每次只能向下或者向右移动一步。

**示例 1：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/732e95892805438d85ec5315d927cdfc~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： grid = [[1,3,1],[1,5,1],[4,2,1]]
输出： 7
解释： 因为路径 1→3→1→1→1 的总和最小。
```

**示例 2：**

```
输入： grid = [[1,2,3],[4,5,6]]
输出： 12
```

**提示：**

-   `m == grid.length`
-   `n == grid[i].length`
-   `1 <= m, n <= 200`
-   `0 <= grid[i][j] <= 100`
### 思路
一眼动态规划的题目
- dp[i][j]表示到达(i,j)这个点的最小路径和
- 转移公式为dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j]
- 先初始化第一行第一列，由于dp过程中只涉及相邻的两个结点，故可以将grid原来的值直接覆盖。
### 代码

```js
/**
 * @param {number[][]} grid
 * @return {number}
 */
 var minPathSum = function(grid) {
    let [m, n] = [grid.length, grid[0].length];
    for(let i = 1; i < m; ++i) 
        grid[i][0] = grid[i-1][0] + grid[i][0]; // 第一列
        
    for(let i = 1; i < n; ++i) 
        grid[0][i] = grid[0][i-1] + grid[0][i]; // 第一行

    for(let i = 1; i < m; ++i)
        for(let j = 1; j < n; ++j)
            grid[i][j] = Math.min(grid[i-1][j], grid[i][j-1]) + grid[i][j];    // 左侧和上侧中走过来较小的呢个

    return grid[m-1][n-1];
};
```


## 300. 最长递增子序列
给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

**子序列** 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。

**示例 1：**

```
输入： nums = [10,9,2,5,3,7,101,18]
输出： 4
解释： 最长递增子序列是 [2,3,7,101]，因此长度为 4 。
```

**示例 2：**

```
输入： nums = [0,1,0,3,2,3]
输出： 4
```

**示例 3：**

```
输入： nums = [7,7,7,7,7,7,7]
输出： 1
```

**提示：**

-   `1 <= nums.length <= 2500`
-   `-104 <= nums[i] <= 104`


**进阶：**

-   你能将算法的时间复杂度降低到 `O(n log(n))` 吗?
### 思路
动态规划，`dp[i]` 表示 `0~i` 中最长递增子序列长度，每次在 `0` 到 `i-1` 中找可以与当前 `i` 构成递增序列的 `j` ，取其中最大的那个作为 `dp[i]`

### 代码
```js
/**
 * @param {number[]} nums
 * @return {number}
 */
 var lengthOfLIS = function(nums) {
    let len = nums.length;
    let dp = new Array(len).fill(1);    // dp[i]表示0~i中最长递增子序列长度
    let ans = 1;
    for(let i = 1; i < len; ++i) {
        for(let j = 0; j <= i-1; ++j) {
            if(nums[i] > nums[j])  // 大，可以与当前i构成递增序列
                dp[i] = Math.max(dp[i], dp[j]+1);
        }
        ans = Math.max(ans, dp[i]);
    }
    return ans;
};
```

## bytedance-004. 机器人跳跃问题
机器人正在玩一个古老的基于 DOS 的游戏。游戏中有 N+1 座建筑——从 0 到 N 编号，从左到右排列。编号为 0 的建筑高度为 0 个单位，编号为 i 的建筑的高度为 H(i) 个单位。\
起初， 机器人在编号为 0 的建筑处。每一步，它跳到下一个（右边）建筑。假设机器人在第 k 个建筑，且它现在的能量值是 E, 下一步它将跳到第个 k+1 建筑。它将会得到或者失去正比于与 H(k+1) 与 E 之差的能量。如果 H(k+1) > E 那么机器人就失去 H(k+1) - E 的能量值，否则它将得到 E - H(k+1) 的能量值。\
游戏目标是到达第个 N 建筑，在这个过程中，能量值不能为负数个单位。现在的问题是机器人以多少能量值开始游戏，才可以保证成功完成游戏？

**格式：**

```
输入：
- 第一行输入，表示一共有 N 组数据.
- 第二个是 N 个空格分隔的整数，H1, H2, H3, ..., Hn 代表建筑物的高度
输出：
- 输出一个单独的数表示完成游戏所需的最少单位的初始能量
```

**示例 1：**
输入
```
5
3 4 3 2 4
```
输出
```
4
```

**示例 2：**

输入
```
3
4 4 4
```
输出
```
4
```

**示例 3：**

```
3
1 6 4
```

**提示：**

-   `1 <= N <= 10^5`
-   `1 <= H(i) <= 10^5`
### 思路
没什么思路，去看了一眼评论恍然大悟。这题核心是解一下方程，虽然题意忽悠我们说了一大堆，实际上是这样的：
设当前编号为 `i`，高度为`h[i]`，能量为`e[i]`，则想到下一个点有 `e[i+1] = e[i]+(e[i]-h[i+1])` 或者 `e[i+1] = e[i]-(h[i+1]-e[i])`，可以发现这两者其实都是等价 `e[i+1] = 2*e[i]-h[i+1]` 的，故当最后一个也就是 `e[n-1]` 为0时初始所需能量最少，即 `2*e[i]-h[i+1] = 0`时，解得 `e[i] = h[i+1]/2`（向上取整）那么从后往前遍历一遍即可。
### 代码
```cpp
#include <iostream>
using namespace std;
const int maxn = 100005;
int n, h[maxn], E;
int main() {
    ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
    cin >> n;
    for(int i = 0; i < n; ++i) 
        cin >> h[i];
    for(int i = n-1; i >= 0; --i)
        E = (E+h[i]+1)/2;
    cout << E << endl;
    return 0;
}
```


---

# day13

#算法/二分  #算法/双指针 

day13题目：[88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)、[31. 下一个排列](https://leetcode-cn.com/problems/next-permutation/)、[4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：数组、双指针、二分，难度为简单、中等、困难

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day12](https://juejin.cn/post/7076832822635266079)
## [88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

给你两个按 **非递减顺序** 排列的整数数组 `nums1` 和 `nums2`，另有两个整数 `m` 和 `n` ，分别表示 `nums1` 和 `nums2` 中的元素数目。

请你 **合并** `nums2` **到 `nums1` 中，使合并后的数组同样按 **非递减顺序** 排列。

**注意：** 最终，合并后数组不应由函数返回，而是存储在数组 `nums1` 中。为了应对这种情况，`nums1` 的初始长度为 `m + n`，其中前 `m` 个元素表示应合并的元素，后 `n` 个元素为 `0` ，应忽略。`nums2` 的长度为 `n` 。

**示例 1：**
```
输入： nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
输出： [1,2,2,3,5,6]
解释： 需要合并 [1,2,3] 和 [2,5,6] 。
合并结果是 [1,2,2,3,5,6] ，其中斜体加粗标注的为 nums1 中的元素。
```

**示例 2：**

```
输入： nums1 = [1], m = 1, nums2 = [], n = 0
输出： [1]
解释： 需要合并 [1] 和 [] 。
合并结果是 [1] 。
```

**示例 3：**

```
输入： nums1 = [0], m = 0, nums2 = [1], n = 1
输出： [1]
解释： 需要合并的数组是 [] 和 [1] 。
合并结果是 [1] 。
注意，因为 m = 0 ，所以 nums1 中没有元素。nums1 中仅存的 0 仅仅是为了确保合并结果可以顺利存放到 nums1 中。
```

**提示：**

-   `nums1.length == m + n`
-   `nums2.length == n`
-   `0 <= m, n <= 200`
-   `1 <= m + n <= 200`
-   `-109 <= nums1[i], nums2[j] <= 109`

**进阶：** 你可以设计实现一个时间复杂度为 `O(m + n)` 的算法解决此问题吗？
### 思路
显而易见的双指针
-   设 `i`、`j` 分别指向`nums1`、`nums2`最后一个元素，`k`指向合并后下标

```js
let [i, j, k] = [m-1, n-1, m+n-1];
```

-   比较 `nums1[i]` 和 `nums2[j]`，谁大就将谁放至 `nums1[k]` 将其对应 `i` 或 `j` 前移

```js
while(i >= 0 && j >= 0) {
    if(nums1[i] >= nums2[j]) {
        nums1[k--] = nums1[i];
        --i;
    } else {
        nums1[k--] = nums2[j];
        --j;
    }
}
```

-   需注意的是当一轮循环结束后仍有 `j >= 0` 说明 `nums2` 中仍有元素未被合并，这个时候直接将其放入 `nums1` 即可

```js
while(j >= 0) {
    nums1[k--] = nums2[j];
    --j;
}
```

### 代码

```js
/**
 * @param {number[]} nums1
 * @param {number} m
 * @param {number[]} nums2
 * @param {number} n
 * @return {void} Do not return anything, modify nums1 in-place instead.
 */
var merge = function(nums1, m, nums2, n) {
    let [i, j, k] = [m-1, n-1, m+n-1];
    while(i >= 0 && j >= 0) {
        if(nums1[i] >= nums2[j]) {
            nums1[k--] = nums1[i];
            --i;
        } else {
            nums1[k--] = nums2[j];
            --j;
        }
    }
    while(j >= 0) {
        nums1[k--] = nums2[j];
        --j;
    }
    return nums1;
};
```

## [31. 下一个排列](https://leetcode-cn.com/problems/next-permutation/)

整数数组的一个 **排列**  就是将其所有成员以序列或线性顺序排列。

-   例如，`arr = [1,2,3]` ，以下这些都可以视作 `arr` 的排列：`[1,2,3]`、`[1,3,2]`、`[3,1,2]`、`[2,3,1]` 。

整数数组的 **下一个排列** 是指其整数的下一个字典序更大的排列。更正式地，如果数组的所有排列根据其字典顺序从小到大排列在一个容器中，那么数组的 **下一个排列** 就是在这个有序容器中排在它后面的那个排列。如果不存在下一个更大的排列，那么这个数组必须重排为字典序最小的排列（即，其元素按升序排列）。

-   例如，`arr = [1,2,3]` 的下一个排列是 `[1,3,2]` 。
-   类似地，`arr = [2,3,1]` 的下一个排列是 `[3,1,2]` 。
-   而 `arr = [3,2,1]` 的下一个排列是 `[1,2,3]` ，因为 `[3,2,1]` 不存在一个字典序更大的排列。

给你一个整数数组 `nums` ，找出 `nums` 的下一个排列。

必须 **[原地](https://baike.baidu.com/item/%E5%8E%9F%E5%9C%B0%E7%AE%97%E6%B3%95)** 修改，只允许使用额外常数空间。

**示例 1：**

```
输入： nums = [1,2,3]
输出： [1,3,2]
```

**示例 2：**

```
输入： nums = [3,2,1]
输出： [1,2,3]
```

**示例 3：**

```
输入： nums = [1,1,5]
输出： [1,5,1]
```

**提示：**

-   `1 <= nums.length <= 100`
-   `0 <= nums[i] <= 100`

### 思路
- 不讲武德版：调algorithm库里的全排列函数捏
```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        next_permutation(nums.begin(), nums.end());
    }
};
```
- 正经思路
没啥思路x屈服于题解了：

>首先，下一个排列总是比当前排列要 **大**，除非该排列已经是最大的排列。所以我们需要找到一个 **大于当前序列** 的新序列，且 **变大的幅度** 尽可能 **小**。具体地有：
> 1. 将一个左边的 **较小数** 与一个右边的 **较大数** 交换，以能够**让当前排列变大**，从而得到下一个排列
> 2. 同时这个 **较小数** 需要尽量 **靠右**，而 **较大数** 需要尽可能 **小**
> 3. 交换完成后，**较大数右边的数** 需要 **按照升序重新排列**吗，这样可以在保证新排列大于原来排列的情况下，使变大的幅度尽可能小。
> 具体地，我们这样描述该算法，对于长度为 n 的排列 a：
> 1.  首先从后向前查找第一个顺序对 `(i,i+1)`，满足 `a[i] < a[i+1]`。这样 **较小数** 即为 `a[i]`。此时 `[i+1,n)` **必然是下降序列**。
> 2.  如果找到了顺序对，那么在区间 `[i+1,n)` 中 **从后向前** 查找第一个元素 `j` ，满足 `a[i] < a[j]`。这样 **较大数** 即为 `a[j]`。
> 3.  交换 `a[i]` 与 `a[j]`，此时可以证明区间 `[i+1,n)` 必为 **降序**。
> 4. 接使用 **双指针** 反转区间 `[i+1,n)` 使其 **变为升序**。
### 代码
```js
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var nextPermutation = function(nums) {
    let len = nums.length;
    let i = len-2;
    while(i >= 0 && nums[i] >= nums[i+1]) --i;
    if(i >= 0) {
        let j = len-1;
        while(j >= 0 && nums[i] >= nums[j]) --j;
        [nums[i], nums[j]] = [nums[j], nums[i]];    // 交换
    }
    var reverseList = function(nums, s, e) {
        if(s >= e) return nums;
        while(s <= e) {
            [nums[s], nums[e]] = [nums[e], nums[s]];
            ++s,--e;
        }
        return nums;
    }
    return reverseList(nums, i+1, len-1);
};
```

## [4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。

算法的时间复杂度应该为 `O(log (m+n))` 。

 
**示例 1：**

```
输入： nums1 = [1,3], nums2 = [2]
输出： 2.00000
解释： 合并数组 = [1,2,3] ，中位数 2
```

**示例 2：**

```
输入： nums1 = [1,2], nums2 = [3,4]
输出： 2.50000
解释： 合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```

**提示：**

-   `nums1.length == m`
-   `nums2.length == n`
-   `0 <= m <= 1000`
-   `0 <= n <= 1000`
-   `1 <= m + n <= 2000`
-   `-106 <= nums1[i], nums2[i] <= 106`

### 思路
- 思路1：按[88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)中合并两个有序数组到 `nums1` 中后，直接找其中位数
```js
let len = m+n;
if(len%2 == 0) {
    return (nums1[len/2]+nums1[(len/2)-1])/2;
} else return nums1[(len-1)/2];
```
### 代码
思路一
```js
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number}
 */
var findMedianSortedArrays = function(nums1, nums2) {
    let [m, n] = [nums1.length, nums2.length];
    nums1 = nums1.concat(new Array(n).fill(0));
    let [i, j, k] = [m-1, n-1, m+n-1];
    while(i >= 0 && j >= 0) {
        if(nums1[i] >= nums2[j]) {
            nums1[k--] = nums1[i];
            --i;
        } else {
            nums1[k--] = nums2[j];
            --j;
        }
    }
    while(j >= 0) {
        nums1[k--] = nums2[j];
        --j;
    }
    let len = m+n;
    if(len%2 == 0) {
        return (nums1[len/2]+nums1[(len/2)-1])/2;
    } else return nums1[(len-1)/2];
};
```


---

# day14 

#算法/动态规划 

day14题目：[121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)、[56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)、[135. 分发糖果](https://leetcode-cn.com/problems/candy/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：数组、动态规划、排序等，难度为简单、中等、困难

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day13](https://juejin.cn/post/7076832822635266079)

## [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

**示例 1：**

```
输入： [7,1,5,3,6,4]
输出： 5
解释： 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

**示例 2：**

```
输入： prices = [7,6,4,3,1]
输出： 0
解释： 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

**提示：**

-   `1 <= prices.length <= 10^5`
-   `0 <= prices[i] <= 10^4`
### 思路
思路非常非常之清晰啊，因为本质上就是最小值与最大值的一个差让其最大化，且最大值必须在最小值之后出现，所以我们
- 用 `minp` 记录当前最小值，`ans` 记录最大利润
    - 若当前 `price[i] <= minp`，则更新 `minp`
    - 否则，记录最大利润，`ans = max(ans, prices[i]-minp)`
### 代码

```js
/**
 * @param {number[]} prices
 * @return {number}
 */
 var maxProfit = function(prices) {
    let len = prices.length;
    let ans = 0;
    let minp = Number.MAX_VALUE;
    for(let i = 0; i < len; ++i) {
        if(prices[i] <= minp) minp = prices[i];
        else ans = Math.max(ans, prices[i]-minp);
    }
    return ans;
};
```

## [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回 **一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间** 。

**示例 1：**

```
输入： intervals = [[1,3],[2,6],[8,10],[15,18]]
输出： [[1,6],[8,10],[15,18]]
解释： 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例 2：**

```
输入： intervals = [[1,4],[4,5]]
输出： [[1,5]]
解释： 区间 [1,4] 和 [4,5] 可被视为重叠区间。
```
**提示：**

-   `1 <= intervals.length <= 10^4`
-   `intervals[i].length == 2`
-   `0 <= starti <= endi <= 10^4`
### 思路
思路非常的清晰啊，因为要合并区间，所以需要先按照每个区间左端点大小升序排列（这样就可以每次与左边的合并即可）
- 排序 `intervals.sort((a, b) => { return a[0] - b[0]; });`
- 用 `ans` 存结果区间数组，以 `k` 记录 `ans` 当前下标
- 每次设 `[prel, prer] = ans[k]`、`[l, r] = intervals[i];`
- 因为排过序了，所以 `prel <= l` 是必然成立的
    - 只需要判断当前区间左端点 `l` 与上一个区间右端点 `prer` 之间关系
    - 若 `l > prer`则 **无需合并**，`push` 之后 `++k`，否则进行一个合并，更改区间右端点。
    
### 代码
```js
/**
 * @param {number[][]} intervals
 * @return {number[][]}
 */
var merge = function(intervals) {
    intervals.sort((a, b) => { return a[0] - b[0]; });
    let ans = [intervals[0]];
    let k = 0;
    let len = intervals.length;
    for(let i = 1; i < len; ++i) {
        let [prel, prer] = ans[k];
        let [l, r] = intervals[i];
        if(l > prer) {
            ans.push(intervals[i]);
            ++k;
        } else ans[k][1] = Math.max(prer, r);
    }
    return ans;
};
```

## [135. 分发糖果](https://leetcode-cn.com/problems/candy/)

`n` 个孩子站成一排。给你一个整数数组 `ratings` 表示每个孩子的评分。

你需要按照以下要求，给这些孩子分发糖果：

-   每个孩子至少分配到 `1` 个糖果。
-   相邻两个孩子评分更高的孩子会获得更多的糖果。

请你给每个孩子分发糖果，计算并返回需要准备的 **最少糖果数目** 。


**示例 1：**

```
输入： ratings = [1,0,2]
输出： 5
解释： 你可以分别给第一个、第二个、第三个孩子分发 2、1、2 颗糖果。
```

**示例 2：**

```
输入： ratings = [1,2,2]
输出： 4
解释： 你可以分别给第一个、第二个、第三个孩子分发 1、2、1 颗糖果。
     第三个孩子只得到 1 颗糖果，这满足题面中的两个条件。
```


**提示：**
-   `n == ratings.length`
-   `1 <= n <= 2 * 10^4`
-   `0 <= ratings[i] <= 2 * 10^4`

### 思路
由题意易知 每个孩子要跟左右两边个比较一次，那么我们从左往右扫一遍，然后再从右往左扫一遍取最大即可
- 从左往右，`l[i]` 存储 `i` 孩子与左边的比较后所需最少糖果数，若 `ratings[i] > ratings[i-1]` 则 `l[i] = l[i-1]+1` ，这里需要用数组暂存是因为接下来要跟右边比较
- 从右往左，`r` 记录 `i` 孩子与右边比较后所需最少糖果数，若 `ratings[i] > ratings[i+1]` 则 `++r`，否则 `r = 1`，之后与 `l[i]` 比较二者取较大的一个数作为该孩子所需最少糖果数
### 代码

```js
/**
 * @param {number[]} ratings
 * @return {number}
 */
var candy = function(ratings) {
    let len = ratings.length;
    let l = new Array(len).fill(1);
    for(let i = 0; i < len; ++i) 
        if(i-1 >= 0 && ratings[i] > ratings[i-1]) 
            l[i] = l[i-1]+1;
    let [ans, r] = [0, 1];
    for(let i = len-1; i >= 0; --i) {
        if(i < len-1 && ratings[i] > ratings[i+1])
            ++r;
        else r = 1;
        ans += Math.max(l[i], r);
    }
    return ans;
};
```


---
# day15

#数据结构/栈 #算法/回溯 #数据结构/队列 

day15题目：[232. 用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)、[22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)、[128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：栈、队列、回溯、哈希表等，难度为简单、中等、中等

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day14](https://juejin.cn/post/7077510120686485541)
## [232. 用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)
请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（`push`、`pop`、`peek`、`empty`）：

实现 `MyQueue` 类：

-   `void push(int x)` 将元素 x 推到队列的末尾
-   `int pop()` 从队列的开头移除并返回元素
-   `int peek()` 返回队列开头的元素
-   `boolean empty()` 如果队列为空，返回 `true` ；否则，返回 `false`

**说明：**

-   你 **只能** 使用标准的栈操作 —— 也就是只有 `push to top`, `peek/pop from top`, `size`, 和 `is empty` 操作是合法的。
-   你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。

**示例 1：**

```
输入：
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
输出：
[null, null, null, 1, 1, false]

解释：
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```
**提示：**

-   `1 <= x <= 9`
-   最多调用 `100` 次 `push`、`pop`、`peek` 和 `empty`
-   假设所有操作都是有效的 （例如，一个空的队列不会调用 `pop` 或者 `peek` 操作）

**进阶：**

-   你能否实现每个操作均摊时间复杂度为 `O(1)` 的队列？换句话说，执行 `n` 个操作的总时间复杂度为 `O(n)` ，即使其中一个操作可能花费较长时间。
### 思路
两个栈模拟队列
- `push` 时直接推入栈 `s1`
- 需要 `pop` 或者 `peek` 时利用栈 `s2`
    - 只要 `s2` 为空，将栈 `s1` 中的元素全部依次出栈，并推入栈 `s2`，再对 `s2` 进行 `pop` 或者 `peek` 操作即可得到正确的先进先出顺序
    - 若 `s2` 不为空，直接对 `s2` 进行 `pop` 或者 `peek` 操作即可
### 代码

```cpp
class MyQueue {
public:
    stack<int> s1;
    stack<int> s2;
    MyQueue() {
    }
    
    void push(int x) {
        s1.push(x);
    }
    
    int pop() {
        if(s2.empty()) {
            while(!s1.empty()) {
                s2.push(s1.top());
                s1.pop();
            }
        }
        int t = s2.top();
        s2.pop();
        return t;
    }
    
    int peek() {
        if(s2.empty()) {
            while(!s1.empty()) {
                s2.push(s1.top());
                s1.pop();
            }
        }
        return s2.top();
    }
    
    bool empty() {
        return s1.empty() && s2.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
 ```
 

## [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)
 
数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

**示例 1：**

```
输入： n = 3
输出： ["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**

```
输入： n = 1
输出： ["()"]
```

**提示：**
-   `1 <= n <= 8`

### 思路
这道题是 [2020蓝桥杯省模拟赛题目记录](https://blog.csdn.net/qq_45890533/article/details/105668209) 中的括号序列（一模一样），回溯法
- 因为要生成 `n` 对括号，故传入括号总数 `sum` 初始为 `n*2`，已使用左括号数 `lnum` 和右括号数 `rnum`
- 先排列左括号，再排右括号，这样若 `lnum < rnum` 就直接返回
- 当剩余待排括号数 `sum == 0` 时，取出这个括号序列 `par` 并返回
- 当左括号数 `lnum < n`，即还可以放左括号进去，就 `push` 一个左括号入 `par`，并继续生成，返回后则 `pop` 以恢复之前的括号序列。 
- 右括号逻辑同理
### 代码
```js
/**
 * @param {number} n
 * @return {string[]}
 */
 var generateParenthesis = function(n) {
    let ans = [];
    let par = [];   // 当前括号序列
    let generate = function(sum, lnum, rnum) {    // 剩余括号数， 已使用左括号数，已使用右括号数
        if(lnum < rnum) return;
        if(sum == 0) {
            ans.push(par.join(''));
            return;
        }
        if(lnum < n) {
            par.push('(');
            generate(sum-1, lnum+1, rnum);
            par.pop();
        }
        if(rnum < n) {
            par.push(')');
            generate(sum-1, lnum, rnum+1);
            par.pop();
        }
    }
    generate(n*2, 0, 0);
    return ans;
};
```

## [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 `O(n)` **的算法解决此问题。

**示例 1：**

```
输入： nums = [100,4,200,1,3,2]
输出： 4
解释： 最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

**示例 2：**

```
输入： nums = [0,3,7,2,5,8,4,6,0,1]
输出： 9
```

**提示：**

-   `0 <= nums.length <= 10^5`
-   `-10^9 <= nums[i] <= 10^9`

### 思路
- 利用hash表存 `nums[i]` 是否存在
- 枚举数组中的每个数 `nums[i]`
    - 以 `nums[i]` 为起点，判断`nums[i]-1`、`nums[i]-2` …… `y`是否存在并更新最大长度，直到`y` 不存在
    - 若 `nums[i]+1` 存在则当前 `nums[i]` 无需判断，直接跳过
### 代码
```js
/**
 * @param {number[]} nums
 * @return {number}
 */
 var longestConsecutive = function(nums) {
    if(nums.length == 0) return 0;
    let m = new Map()
    for(let i = 0; i < nums.length; ++i) 
        m.set(nums[i], true);
    let ans = 1;
    for(let i = 0; i < nums.length; ++i) {
        if(!m.has(nums[i]+1)) {
            let num = nums[i]-1;
            let cnt = 1;
            while( m.has(num) ) {
                if(++cnt > ans) ans = cnt;
                --num;
            }
        }
    }
    return ans;
};
```


---
# day16

#算法/搜索 #数据结构/二叉树  #算法/滑动窗口  

day16题目：[bytedance-007. 化学公式解析](https://leetcode-cn.com/problems/fF9c0W/)、[129. 求根节点到叶节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/)、[239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：正则、树、dfs、滑动窗口，难度为字节の简单、中等、困难

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day15](https://juejin.cn/post/7077942843163017253)

## [bytedance-007. 化学公式解析](https://leetcode-cn.com/problems/fF9c0W/)

给定一个用字符串展示的化学公式，计算每种元素的个数。规则如下：
-   元素命名采用驼峰命名，例如 `Mg`
-   `()` 代表内部的基团，代表阴离子团
-   `[]` 代表模内部链节通过化学键的连接，并聚合

例如：H2O => H2O1 Mg(OH)2 => H2Mg1O2

**格式：**

```
输入：
- 化学公式的字符串表达式，例如：K4[ON(SO3)2]2 。
输出：
- 元素名称及个数：K4N2O14S4，并且按照元素名称升序排列。
```

**示例：**

```
输入：K4[ON(SO3)2]2
输出：K4N2O14S4
```
### 思路
评论区大神思路，惊为天人。
- 正则匹配先将所有小括号展开
- 再将所有大括号展开
- 然后将原子展开：`Mg4` -> `MgMgMgMg`
- 最后就是统计词频然后排序即可
### 代码
```js
var input = require('fs').readFileSync('/dev/stdin', 'utf8').trim().split('\n')[0]
function replacer(match, str, k) {
    k = k? parseInt(k): 1;
    return str.repeat(k)
}
input = input.replace(/\((.+)\)(\d+)/g, replacer)   // 小括号替换
input = input.replace(/\[(.+)\](\d+)/g, replacer)   // 中括号替换
input = input.replace(/([A-Z][a-z]?)(\d+)/g, replacer)   // 原子展开
var map = new Map()
var ans = []
input = input.match(/[A-Z][a-z]?/g)
for(let i = 0; i < input.length; ++i) {
    let ch = input[i]
    if(!map.has(ch)) {
        map.set(ch, ans.length)
        ans.push([ch, 1])
    } else {
        let key = map.get(ch)
        ++ans[key][1]
    }
}
ans = ans.sort((a, b) => a[0].localeCompare(b[0]));
console.log(ans.toString().replace(/\,/g, ''))
```

## [129. 求根节点到叶节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/)

给你一个二叉树的根节点 `root` ，树中每个节点都存放有一个 `0` 到 `9` 之间的数字。

每条从根节点到叶节点的路径都代表一个数字：

-   例如，从根节点到叶节点的路径 `1 -> 2 -> 3` 表示数字 `123` 。

计算从根节点到叶节点生成的 **所有数字之和** 。

**叶节点** 是指没有子节点的节点。

 
**示例 1：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c2582dda288345e1bfc0171997da14de~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： root = [1,2,3]
输出： 25
解释：
从根到叶子节点路径 1->2 代表数字 12
从根到叶子节点路径 1->3 代表数字 13
因此，数字总和 = 12 + 13 = 25
```

**示例 2：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84606833c16d4dc19afbceb9af735f99~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： root = [4,9,0,5,1]
输出： 1026
解释：
从根到叶子节点路径 4->9->5 代表数字 495
从根到叶子节点路径 4->9->1 代表数字 491
从根到叶子节点路径 4->0 代表数字 40
因此，数字总和 = 495 + 491 + 40 = 1026
```

**提示：**

-   树中节点的数目在范围 `[1, 1000]` 内
-   `0 <= Node.val <= 9`
-   树的深度不超过 `10`

### 思路
一眼题，dfs就完事了，到叶子节点将 sum 加到答案中
### 代码
```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
var sumNumbers = function(root) {
    let ans = 0
    let dfs = function(rt, sum) {
        if(!rt) return
        sum = sum*10 + rt.val
        if(!rt.left && !rt.right) {
            ans += sum
            return
        }
        if(rt.left) dfs(rt.left, sum);
        if(rt.right) dfs(rt.right, sum);
    }
    dfs(root, 0);
    return ans;
};
```

## [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)


给你一个整数数组 `nums`，有一个大小为 `k` **的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

返回 *滑动窗口中的最大值* 。

 
**示例 1：**

```
输入： nums = [1,3,-1,-3,5,3,6,7], k = 3
输出： [3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7      5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**示例 2：**

```
输入： nums = [1], k = 1
输出： [1]
```

**提示：**

-   `1 <= nums.length <= 10^5`
-   `-10^4 <= nums[i] <= 10^4`
-   `1 <= k <= nums.length`

### 思路
队列q存储下标，其对应元素单调递减
- 若滑动窗口中两个元素 `j < i` 并且 `nums[j] <= nums[i]` ，只要 `j` 还在窗口中，那么 `i` 一定也还在窗口中，所以最值一定不是 `nums[j]`，故可以将其移除
- 滑动过程中记录，若队尾元素小于等于当前新元素，则弹出，直到为空或者队尾元素大于新元素
- 同时若队头所存下标 `q[0]` 小于窗口左侧 `l`，则不断将队首弹出
### 代码
```js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
 var maxSlidingWindow = function(nums, k) {
    let q = []
    for(let i = 0; i < k; ++i) {    // 可以移除就移除
        while(q.length != 0 && nums[i] >= nums[q[q.length-1]]) 
            q.pop()
        q.push(i)
    }
    let ans = [nums[q[0]]]
    let len = nums.length
    for(let l = 1; l+k-1 < len; ++l) {
        let r = l+k-1
        while(q.length != 0 && nums[r] >= nums[q[q.length-1]]) 
            q.pop()
        q.push(r)
        while(q.length != 0 && q[0] < l) 
            q.shift() // 队首弹出
        ans.push(nums[q[0]])
    }
    return ans
};
```

---
# day17

#数据结构/链表 #算法/双指针 #数据结构/链表 #算法/搜索  

day17题目：[141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)、[236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)、[92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点：快慢指针、dfs、链表，难度为简单、中等、中等

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day16](https://juejin.cn/post/7078327858397118477)

## [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

给你一个链表的头节点 `head` ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。**注意：`pos` 不作为参数进行传递** 。仅仅是为了标识链表的实际情况。

*如果链表中存在环* ，则返回 `true` 。 否则，返回 `false` 。


**示例 1：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/194b1ae012b044ec9c443d9fba166f04~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [3,2,0,-4], pos = 1
输出： true
解释： 链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c7e0b3ec5858499488fb3eca9d0a5ed7~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [1,2], pos = 0
输出： true
解释： 链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5eac2477a0684c81910ffe905f60d7d7~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [1], pos = -1
输出： false
解释： 链表中没有环。
```

**提示：**

-   链表中节点的数目范围是 `[0, 10^4]`
-   `-10^5 <= Node.val <= 10^5`
-   `pos` 为 `-1` 或者链表中的一个 **有效索引** 。

**进阶：** 你能用 `O(1)`（即，常量）内存解决此问题吗？

### 思路
快慢指针，快指针一次走两步，慢指针一次走一步，若有环则一定会遇上的，否则没有环
### 代码
```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */

/**
 * @param {ListNode} head
 * @return {boolean}
 */
var hasCycle = function(head) {
    if(!head) return false
    let [f, s] = [head, head]
    while(s.next && f.next && f.next.next) {
        f = f.next.next;
        s = s.next
        if(f === s) return true; 
    }
    return false;
};
```
## [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

[百度百科](https://baike.baidu.com/item/%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

 

**示例 1：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3dc124e9ec624327a6c317cee3761d2f~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出： 3
解释： 节点 5 和节点 1 的最近公共祖先是节点 3 。
```

**示例 2：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b2ebbac4b57b4255840806caa1d46ef9~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出： 5
解释： 节点 5 和节点 4 的最近公共祖先是节点 5 。因为根据定义最近公共祖先节点可以为节点本身。
```

**示例 3：**

```
输入： root = [1,2], p = 1, q = 2
输出： 1
```

**提示：**

-   树中节点数目在范围 `[2, 10^5]` 内。
-   `-10^9 <= Node.val <= 10^9`
-   所有 `Node.val` `互不相同` 。
-   `p != q`
-   `p` 和 `q` 均存在于给定的二叉树中。

### 思路
先从一个节点往上走将沿途的vis置为true，再从另一个结点往上走，遇到vis为true的就是最近共同祖先，返回。

### 代码

```js
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
    let fa = new Map()
    let vis = new Map()
    let init = function(rt) {
        vis.set(rt.val, false);
        if(rt.left) {
            fa.set(rt.left.val, rt)
            init(rt.left)
        }
        if(rt.right) {
            fa.set(rt.right.val, rt)
            init(rt.right)
        }
    }
    fa.set(root.val, null)
    init(root)
    let ptr = p
    while(ptr && fa.has(ptr.val)) {
        vis.set(ptr.val, true)
        ptr = fa.get(ptr.val)
    }
    ptr = q
    while(ptr && fa.has(ptr.val)) {
        if(vis.get(ptr.val)) return ptr
        ptr = fa.get(ptr.val)
    }
}
```

## [92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

给你单链表的头指针 `head` 和两个整数 `left` 和 `right` ，其中 `left <= right` 。请你反转从位置 `left` 到位置 `right` 的链表节点，返回 **反转后的链表** 。

**示例 1：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e7e0b18aac5140ec946a0d1f90bdd11e~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [1,2,3,4,5], left = 2, right = 4
输出： [1,4,3,2,5]
```

**示例 2：**

```
输入： head = [5], left = 1, right = 1
输出： [5]
```

**提示：**

-   链表中节点数目为 `n`
-   `1 <= n <= 500`
-   `-500 <= Node.val <= 500`
-   `1 <= left <= right <= n`

**进阶：**  你可以使用一趟扫描完成反转吗？

### 思路
哎，先来一个反转链表的函数，day1的K组一个反转链表里边我们就写过：
```js
var reverseList = function(s, e) {
    let prev = e.next
    let nowv = s
    while(prev != e) {
        let temp = nowv.next
        nowv.next = prev
        prev = nowv
        nowv = temp
    }
    return [e, s]
}
```
然后，当务之急是找到 `left` 和 `right` 对应的结点指针 `l` 和 `r`，以及 `l` 前面的 `prev`
- 翻转 `let [s, e] = reverseList(l, r)`， 记得在之前先把 `r.next` 存到 `temp`
- 为了让反转后子链表能够顺利的拼回去原链表，需将反转后的 `pre.next` 指向`s`， `e.next` 指向 `temp`

### 代码

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
 var reverseList = function(s, e) {
    let prev = e.next
    let nowv = s
    while(prev != e) {
        let temp = nowv.next
        nowv.next = prev
        prev = nowv
        nowv = temp
    }
    return [e, s]
}
/**
 * @param {ListNode} head
 * @param {number} left
 * @param {number} right
 * @return {ListNode}
 */
var reverseBetween = function(head, left, right) {
    let ehead = new ListNode(0, head)
    let [prev, nowv, prel, l, r] = [ehead, head, ehead, head, head]
    for(let cnt = 1; nowv; ++cnt, nowv = nowv.next) {
        if(cnt === left) {
            prel = prev
            l = nowv
        }
        if(cnt === right) r = nowv
        prev = nowv
    }
    let temp = r.next
    let [s, e] = reverseList(l, r)
    prel.next = s
    e.next = temp
    return ehead.next
};
```

---

# day18

#算法/动态规划 

day18题目：[322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)、[198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)、 [bytedance-003. 古生物血缘远近判定](https://leetcode-cn.com/problems/LJXRel/)

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

今日知识点： 数组、动态规划，难度为中等、中等、字节の简单

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day17](https://juejin.cn/post/7078591217184800805)

## [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。

计算并返回可以凑成总金额所需的 **最少的硬币个数** 。如果没有任何一种硬币组合能组成总金额，返回 `-1` 。

你可以认为每种硬币的数量是无限的。

**示例 1：**

```
输入： coins = [1, 2, 5], amount = 11
输出： 3 
解释： 11 = 5 + 5 + 1
```

**示例 2：**

```
输入： coins = [2], amount = 3
输出： -1
```

**示例 3：**

```
输入： coins = [1], amount = 0
输出： 0
```

**提示：**
-   `1 <= coins.length <= 12`
-   `1 <= coins[i] <= 2^31 - 1`
-   `0 <= amount <= 10^&4`

### 思路
动态规划
- `dp[i]` 表示凑成金额 `i` 所需最少的硬币个数 
- 转移方程为 `dp[i] = min(dp[i-coins[j]]+1, dp[i])`，`j` 为第j个硬币面值，当且仅当这个硬币的面值比当前金额小
- 最后若 `dp[amount] > amount` 说明无法凑成

### 代码
```javascript
/**
 * @param {number[]} coins
 * @param {number} amount
 * @return {number}
 */
var coinChange = function(coins, amount) {
    coins.sort((a, b) => b-a)
    let maxa = amount+1
    let dp = new Array(amount+1).fill(maxa) // 凑成金额i所需最少的硬币个数 
    dp[0] = 0
    for(let i = 1; i <= amount; ++i) {
        for(let j = 0; j < coins.length; ++j) {
            if(coins[j] > i) continue
            let prea = i-coins[j]
            dp[i] = Math.min(dp[prea]+1, dp[i])
        }
    }
    return dp[amount] > amount? -1: dp[amount]
};
```
## [198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。

**示例 1：**

```
输入： [1,2,3,1]
输出： 4
解释： 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

**示例 2：**

```
输入： [2,7,9,3,1]
输出： 12
解释： 偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

**提示：**

-   `1 <= nums.length <= 100`
-   `0 <= nums[i] <= 400`
### 思路

首先，简单的一思索就可以得出这么一个dp：
- `dp[0][i]` 表示偷第 `i` 家可偷得的最大数量， `dp[1][i]` 表示不偷第 `i` 家可偷得的最大数量
- 转移方程就很容易得出：
    - 若偷当前这家，则不可以偷上一家，故 `dp[0][i] = dp[1][i-1]+nums[i]`
    - 若不偷当前这家，则可以偷上一家也可以不偷，取两者中较大的金额 `dp[1][i] = max(dp[0][i-1], dp[1][i-1])` 
```js
var rob = function(nums) {
    let dp = new Array(2).fill(0).map((v) => new Array(nums.length).fill(0))
    let ans = nums[0]
    dp[0][0] = nums[0]  // 0为偷 
    dp[1][0] = 0        // 1为不偷
    for(let i = 1; i < nums.length; ++i) {
        dp[0][i] = dp[1][i-1]+nums[i]
        dp[1][i] = Math.max(dp[0][i-1], dp[1][i-1])
    }
    return Math.max(dp[0][nums.length-1], dp[1][nums.length-1])
};
```

其次，可以看到这个 `dp` 每次只和 `i-1` 也就是上一家比较，故可以优化
- 直接用 `pre[0]` 和 `pre[1]` 代替 `dp[0][i-1]` 和 `dp[1][i-1]` 即可，省了一大笔空间！
代码如下：
### 代码
```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var rob = function(nums) {
    let pre = [nums[0], 0]      // pre[0]为偷上一个 pre[1]为不偷上一个
    let ans = nums[0]
    for(let i = 1; i < nums.length; ++i) {
        let t = pre[0]
        pre[0]= pre[1]+nums[i]
        pre[1] = Math.max(t, pre[1])
    }
    return Math.max(pre[0], pre[1])
};
```

## [bytedance-003. 古生物血缘远近判定](https://leetcode-cn.com/problems/LJXRel/)

DNA 是由 ACGT 四种核苷酸组成，例如 AAAGTCTGAC，假定自然环境下 DNA 发生异变的情况有：

1.  基因缺失一个核苷酸
1.  基因新增一个核苷酸
1.  基因替换一个核苷酸

且发生概率相同。\
古生物学家 Sam 得到了若干条相似 DNA 序列，Sam 认为一个 DNA 序列向另外一个 DNA 序列转变所需的最小异变情况数可以代表其物种血缘相近程度，异变情况数越少，血缘越相近，请帮助 Sam 实现获取两条 DNA 序列的最小异变情况数的算法。

**格式：**

```
输入：
- 每个样例只有一行，两个 DNA 序列字符串以英文逗号“,”分割
输出：
- 输出转变所需的最少情况数，类型是数字
```

**示例：**

```
输入：ACT,AGCT
输出：1
```

**提示：**

-   每个 DNA 序列不超过 100 个字符

### 思路
其实就是一个字符串，每次可以进行
- 增加某字符
- 删除某字符
- 修改某字符
求将其变成另一个字符串所需最小次数

像不像之前某个困难题：编辑距离？不过字符的种类只有 `A C G T` 四个罢了

指路：[冲刺春招-精选笔面试 66 题大通关 day11](https://ysx.cosine.ren/cn/%E5%86%B2%E5%88%BA%E6%98%A5%E6%8B%9B-%E7%B2%BE%E9%80%89%E7%AC%94%E9%9D%A2%E8%AF%95%2066%20%E9%A2%98%E5%A4%A7%E9%80%9A%E5%85%B3%20day11/#%E6%80%9D%E8%B7%AF-3)

`dp[i][j]` 表示 `dna1` 中前 `i` 个字符变成 `dna2` 中前j个字符最少需要多少步

- `i=0` 的时候（即第一行）为 `dna2` 前j个数变为空字符串需要几步（自然是j步）

- `j=0` 时（即第一列）为 `dna1` 前 `i` 个数变为空字符串需要几步

- 开始遍历，过程中若当前 `dna1[i] == dna2[j]`，则说明无需变化，`dp[i][j] = dp[i-1][j-1];`

- 否则 `dp[i][j]` 为 `dp[i-1][j-1]`、`dp[i-1][j]`、 `dp[i][j-1])+1` 中最小步数+1

    - 这里最小值若为 `dp[i-1][j-1]` 表示修改一个字符
    
    - 若为 `dp[i][j-1]` 表示往 `dna2[j-1]` 后面添加一个字符，`dp[i-1][j]` 表示往 `dna1[i-1]` 后面添加一个字符

### 代码
```js
var [dna1, dna2] = require('fs').readFileSync('/dev/stdin', 'utf8').trim().split('\n')[0].split(',')
let [len1, len2] = [dna1.length+1, dna2.length+1]
let dp = new Array(len1).fill(0).map(() => new Array(len2).fill(0))
// dp[i][j]为dna1的0~i-1变到dna2的0~j-1所需最少次数
for(let i = 1; i < len1; ++i)
    dp[i][0] = dp[i-1][0]+1
for(let i = 1; i < len2; ++i)
    dp[0][i] = dp[0][i-1]+1
for(let i = 1; i < len1; ++i) {
    for(let j = 1; j < len2; ++j) {
        if(dna1[i-1] === dna2[j-1]) dp[i][j] = dp[i-1][j-1]
        else dp[i][j] = Math.min(dp[i-1][j-1], dp[i][j-1], dp[i-1][j])+1
    }
}
console.log(dp[len1-1][len2-1])
```


---
# day19

#数据结构/链表 #算法/双指针 

day19题目：[160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)、[143. 重排链表](https://leetcode-cn.com/problems/reorder-list/)、[142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

今日知识点：链表、递归、双指针，难度为简单、中等、中等

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day18](https://juejin.cn/post/7079006098585288740)

## [160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 `null` 。

图示两个链表在节点 `c1` 开始相交 **：**

[![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8cdf75325fcb46ccbb7c8ae0cedef329~tplv-k3u1fbpfcp-zoom-1.image)](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

题目数据 **保证** 整个链式结构中不存在环。

**注意**，函数返回结果后，链表必须 **保持其原始结构** 。

**自定义评测：**

**评测系统** 的输入如下（你设计的程序 **不适用** 此输入）：

-   `intersectVal` - 相交的起始节点的值。如果不存在相交节点，这一值为 `0`
-   `listA` - 第一个链表
-   `listB` - 第二个链表
-   `skipA` - 在 `listA` 中（从头节点开始）跳到交叉节点的节点数
-   `skipB` - 在 `listB` 中（从头节点开始）跳到交叉节点的节点数

评测系统将根据这些输入创建链式数据结构，并将两个头节点 `headA` 和 `headB` 传递给你的程序。如果程序能够正确返回相交节点，那么你的解决方案将被 **视作正确答案** 。

 

**示例 1：**

[![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/06c967f2dff445c7b02e8a61b1f48fca~tplv-k3u1fbpfcp-zoom-1.image)](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)

```
输入： intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
输出： Intersected at '8'
解释： 相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,6,1,8,4,5]。
在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

**示例 2：**

[![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f1b335701854e6b816e2c877c454de4~tplv-k3u1fbpfcp-zoom-1.image)](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)

```
输入： intersectVal = 2, listA = [1,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出： Intersected at '2'
解释： 相交节点的值为 2 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [1,9,1,2,4]，链表 B 为 [3,2,4]。
在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```

**示例 3：**

[![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/25631057c2ec45428562c5baef969d86~tplv-k3u1fbpfcp-zoom-1.image)](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

```
输入： intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出： null
解释： 从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。
由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
这两个链表不相交，因此返回 null 。
```

**提示：**

-   `listA` 中节点数目为 `m`
-   `listB` 中节点数目为 `n`
-   `1 <= m, n <= 3 * 10^4`
-   `1 <= Node.val <= 10^5`
-   `0 <= skipA <= m`
-   `0 <= skipB <= n`
-   如果 `listA` 和 `listB` 没有交点，`intersectVal` 为 `0`
-   如果 `listA` 和 `listB` 有交点，`intersectVal == listA[skipA] == listB[skipB]`

**进阶：** 你能否设计一个时间复杂度 `O(m + n)` 、仅用 `O(1)` 内存的解决方案？

### 思路
双指针，利用 `prea` 和 `preb` 作为a和b的前一个结点，当 `prea === preb` 时说明这当前节点上一个节点都是同一个结点

### 代码
```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */

/**
 * @param {ListNode} headA
 * @param {ListNode} headB
 * @return {ListNode}
 */
var getIntersectionNode = function(headA, headB) {
    if(!headA || !headB) return null
    let [prea, preb] = [headA, headB]
    while(prea !== preb) {
        prea = prea.next
        preb = preb.next
        if(prea === preb) return prea
        if(!prea) prea = headB
        if(!preb) preb = headA
    }
    return prea
};
```


## [143. 重排链表](https://leetcode-cn.com/problems/reorder-list/)

给定一个单链表 `L` **的头节点 `head` ，单链表 `L` 表示为：

```
L0 → L1 → … → Ln - 1 → Ln
```

请将其重新排列后变为：

```
L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
```

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

**示例 1：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e316b5a26066492aa1c495e4d9cddf98~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [1,2,3,4]
输出： [1,4,2,3]
```

**示例 2：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/076404a90c2e4eadac340d9ba9285c3d~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [1,2,3,4,5]
输出： [1,5,2,4,3]
```

**提示：**

-   链表的长度范围为 `[1, 5 * 10^4]`
-   `1 <= node.val <= 1000`

### 思路
- 若没有结点或只有一两个结点，则无需操作。
- 每次将最后一个结点 `nowv` 接在头结点 `head` 后面
- 然后将倒数第二个结点 `prev` 置为最后一个节点
- 再对递归的原本的第三个结点 `nowv.next` 以及之后的链表进行该操作
### 代码
```js
/**
 * @param {ListNode} head
 * @return {void} Do not return anything, modify head in-place instead.
 */
var reorderList = function(head) {
    if(!head || !head.next) return;
    let [prev, nowv] = [null, head];
    while(nowv.next) {
        prev = nowv
        nowv = nowv.next
    }
    if(!prev || prev === head)  return
    prev.next = null
    nowv.next = head.next
    head.next = nowv
    reorderList(nowv.next)
};
```
## [142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

**不允许修改** 链表。

**示例 1：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a96b1a0bf3914cabb562383b7cd8a266~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [3,2,0,-4], pos = 1
输出： 返回索引为 1 的链表节点
解释： 链表中有一个环，其尾部连接到第二个节点。
```

**示例 2：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b17258179ba14652afcc35109bcd0a3b~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [1,2], pos = 0
输出： 返回索引为 0 的链表节点
解释： 链表中有一个环，其尾部连接到第一个节点。
```

**示例 3：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ddd3659d9e8246168e5f8175b4ed98c0~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： head = [1], pos = -1
输出： 返回 null
解释： 链表中没有环。
```

**提示：**

-   链表中节点的数目范围在范围 `[0, 10^4]` 内
-   `-10^5 <= Node.val <= 10^5`
-   `pos` 的值为 `-1` 或者链表中的一个有效索引

**进阶：** 你是否可以使用 `O(1)` 空间解决此题？

### 思路

环形链表在[冲刺春招-精选笔面试 66 题大通关 day17](https://ysx.cosine.ren/cn/%E5%86%B2%E5%88%BA%E6%98%A5%E6%8B%9B-%E7%B2%BE%E9%80%89%E7%AC%94%E9%9D%A2%E8%AF%95%2066%20%E9%A2%98%E5%A4%A7%E9%80%9A%E5%85%B3%20day17/#%E6%80%9D%E8%B7%AF)中有做过，主要思想是快慢指针，快指针一次走两步，慢指针一次走一步，若有环则一定会遇上，否则没有环。

而这里需要返回入环处的结点，那么，当快指针 `fast` 等于慢指针 `slow` 时，设置一个新的指针 `ptr` 跟着 `slow` 一块走，最终 `ptr` 和 `slow` 就会在入环结点相遇。
推导如下，直接看官方的吧：

> 如下图所示，设链表中环外部分的长度为 `a`。`slow` 指针进入环后，又走了 `b` 的距离与 `fast` 相遇。此时，假设 `fast` 指针已经走完了环的 `n` 圈，则 `fast` 走过的总距离为 `a+n(b+c)+b` 化简得 `a+(n+1)b+nc`。
> ![fig1](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d8cd476489a4a58b00cead81fb28ea9~tplv-k3u1fbpfcp-zoom-1.image)
> 由于快指针每次都比慢指针多走一步，故 `fast` 走过的距离是 `slow` 的两倍，而 `slow` 走过的总距离为 `a+b`，所以有 `a+(n+1)b+nc = 2(a+b)`，解得 `a = c+(n-1)(b+c)`
> - 也就是说，从相遇点到入环点的距离 `c` 加上`n-1`圈环长（b+c），恰好等于链表头部到入环点的距离 `a`
> - 因此，当发现 `slow` 与 `fast` 相遇时，我们再额外使用一个指针 `ptr`。起始，`ptr` 指向链表头部。随后，`ptr` 和 `slow` 每次向后移动一个位置。最终，它们会在入环点相遇。

### 代码
```js
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var detectCycle = function(head) {
    let [slow, fast] = [head, head];
    while(fast && fast.next) {
        slow = slow.next
        fast = fast.next.next
        if(slow === fast) break
    }
    if(!fast || !fast.next) return null
    let p = head
    while(p !== slow) {
        p = p.next
        slow = slow.next
    }
    return p
};
```

---
# day20

#算法/模拟 #算法/二分 

day20题目：[704. 二分查找](https://leetcode-cn.com/problems/binary-search/)、[43. 字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)、[bytedance-002. 发下午茶](https://leetcode-cn.com/problems/OMrszv/)

今日知识点： 数组、二分、模拟，难度为简单、中等、字节の简单

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day19](https://juejin.cn/post/7079361446315819044)

## [704. 二分查找](https://leetcode-cn.com/problems/binary-search/)

给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`  ，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。

**示例 1:**

```
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
```

**示例 2:**

```
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

**提示：**

1.  你可以假设 `nums` 中的所有元素是不重复的。
2.  `n` 将在 `[1, 10000]`之间。
3.  `nums` 的每个元素都将在 `[-9999, 9999]`之间。

### 思路
直接二分，老朋友了
- 每次令 `mid = (l+r)/2`，若 `nums[mid] == target` 则直接返回下标 `mid`
- 若 `nums[mid] < target`，在 `mid` 右侧搜索，`l = mid+1`
- 否则，在 `mid` 左侧搜索，`l = mid+1`
- 若最后 `l > r` 了则说明找不到，返回 `-1`
### 代码
```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function(nums, target) {
    let [l, r] = [0, nums.length - 1];
    while (l <= r) {
        let mid = (l+r) >> 1;
        if(nums[mid] == target) return mid;
        else if(nums[mid] < target) l = mid + 1;
        else r = mid - 1;
    }
    return -1;
};
```

## [43. 字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)

给定两个以字符串形式表示的非负整数 `num1` 和 `num2`，返回 `num1` 和 `num2` 的乘积，它们的乘积也表示为字符串形式。

**注意：** 不能使用任何内置的 BigInteger 库或直接将输入转换为整数。

**示例 1:**

```
输入: num1 = "2", num2 = "3"
输出: "6"
```

**示例 2:**

```
输入: num1 = "123", num2 = "456"
输出: "56088"
```

**提示：**

-   `1 <= num1.length, num2.length <= 200`
-   `num1` 和 `num2` 只能由数字组成。
-   `num1` 和 `num2` 都不包含任何前导零，除了数字0本身。

### 思路
模拟竖式乘法
![模拟竖式乘法](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/698642a0e3724164ad93ef46326d1f8a~tplv-k3u1fbpfcp-watermark.image?)
- 开头先特判一下是否有哪个数有为 `0` 的情况，直接返回 `0`
- 被乘数 `num1` 位数为 `n` ，乘数 `num2` 位数为 `m`， `num1 * num2` 的结果 `res` 最大总位数为 `n+m`
- `num1[i] * num2[j]` 的结果 `mul`，第一位位于 `res[i+j]`，第二位位于 `res[i+j+1]`。
- 最后需去除前导0 
### 代码
```javascript
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
var multiply = function(num1, num2) {
    if(num1 == '0' || num2 == '0') 
        return '0'
    let [n, m] = [num1.length, num2.length]
    let res = new Array(n+m).fill(0)
    for(let i = n-1; i >= 0; i--) {
        for(let j = m-1; j >= 0; j--) {
            let mul = parseInt(num1[i]) * parseInt(num2[j])
            let p1 = i + j, p2 = i + j + 1
            let sum = mul + res[p2]
            res[p1] += Math.floor(sum / 10)
            res[p2] = sum % 10  // 取余
        }
    }
    let idx = res.findIndex(x => x != 0)  // 去除前导0
    return idx == -1? '0' : res.slice(idx).join('')
};
```

## [bytedance-002. 发下午茶](https://leetcode-cn.com/problems/OMrszv/)

有 K 名字节君，每天下午都要推着推车给字节的同学送下午茶，字节的同学分布在不同的工区，字节的工区分布和字节君的位置分布如下。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/842b1a582fce4013b8375fd96ccc629b~tplv-k3u1fbpfcp-zoom-1.image)

在上图中，每个方框内的单位长度为 1。已知字节君的推车可以装无限份下午茶，所以不需要字节君回到初始地点补充下午茶。每个字节君只有两个动作。

1.  把推车向前移动一个单位。
1.  把一份下午茶投放到当前工区。

现在告诉你字节君的数量以及每个工具需要的下午茶个数请问，所有的字节君最少花费多长时间才能送完所有的下午茶？

**格式：**

```
输入：
- 第一行是字节君的数量K和工区的数量 N
- 第二行 N 个数字是每个工区需要的下午茶数量 Ti
输出：
- 输出一个数字代表所有字节均最少花费多长时间才能送完所有的下午茶
```

**示例 1：**

```
输入：
     3 3
     7 1 1
输出：5
解释：
字节君1：右移->放置->放置->放置->放置
字节君2：右移->放置->放置->放置
字节君3：右移->右移->放置->右移->放置
```

**示例 2：**

```
输入：
     2 4
     3 3 1 1
输出：7
解释：
字节君1：右移->放置->放置->放置->右移->放置->放置
字节君2：右移->右移->放置->右移->放置->右移->放置
```

**提示：**

-   `0< K, N <= 1000`
-   `0<= Ti <= 10000`
### 思路
又看了评论区大神的解答（）
- 由于配送时间最好情况下为`0`（根本无须配送），最坏情况下为 `N+T中所有数`（一个人配送）
- 故答案可在 `0~N+T中所有数` 中进行二分，若还能有更好情况则取更好情况。
- 在 `check` 函数中检查该配送时间是否足够送完所有工区
### 代码
```cpp
#include <iostream>
using namespace std;
const int maxn = 1005;
int K, N;
int t[maxn], t2[maxn];
bool check(int time) {
    for(int i = 0; i < N; i++) 
        t2[i] = t[i];
    for(int i = 0; i < K; i++) {    // 每个人
        int rest = time;    // 可用时间
        for(int j = 0; j < N; j++) { // 每个工区
            --rest;  // 移动过来。
            if(rest <= 0) break;    // 无时间了
            if(t2[j] < rest) {  // 剩余时间足够送完该工区
                rest -= t2[j];
                t2[j] = 0;
            } else {    // 当前剩余时间不够送完该工区的
                t2[j] -= rest;
                break;
            }
                
        }
    }
    for(int i = 0; i < N; ++i) {
        if(t2[i] > 0) return false;
    }
    return true;
}
int main() {
    ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
    cin >> K >> N;
    int l = 0, r = N;
    for(int i = 0; i < N; i++) {
        cin >> t[i];
        r += t[i];
    }
    while(l < r) {
        int mid = (l + r) >> 1;
        if (check(mid)) r = mid;
        else l = mid + 1;
    }
    cout << l << endl;
    return 0;
}
```

---
# day21

#算法/动态规划 

day21题目：[69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)、[912. 排序数组](https://leetcode-cn.com/problems/sort-an-array/)、[887. 鸡蛋掉落](https://leetcode-cn.com/problems/super-egg-drop/)

今日知识点：数组、排序、动态规划，难度为简单、中等、困难

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day20](https://juejin.cn/post/7079764184023433230)

## [69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

给你一个非负整数 `x` ，计算并返回 `x` 的 **算术平方根** 。

由于返回类型是整数，结果只保留 **整数部分** ，小数部分将被 **舍去 。**

**注意：** 不允许使用任何内置指数函数和算符，例如 `pow(x, 0.5)` 或者 `x ** 0.5` 。

**示例 1：**

```
输入： x = 4
输出： 2
```

**示例 2：**

```
输入： x = 8
输出： 2
解释： 8 的算术平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。
```

**提示：**

-   `0 <= x <= 2^31 - 1`

### 思路
- 二分，平分根在`0`到`x`之间，且`0`到`x`有序。
- 当 `mid*mid <= x` 说明 `mid` 小了，在右半侧搜寻
### 代码
```javascript
/**
 * @param {number} x
 * @return {number}
 */
var mySqrt = function(x) {
    let [l, r] = [0, x]
    while (l <= r) {
        let mid = (l+r)>>1
        if (mid*mid <= x) {
            l = mid+1
        } else r = mid-1
    }
    return r
};
```
## [912. 排序数组](https://leetcode-cn.com/problems/sort-an-array/)

给你一个整数数组 `nums`，请你将该数组升序排列。

**示例 1：**

```
输入： nums = [5,2,3,1]
输出： [1,2,3,5]
```

**示例 2：**

```
输入： nums = [5,1,1,2,0,0]
输出： [0,0,1,1,2,5]
```

**提示：**

-   `1 <= nums.length <= 5 * 10^4`
-   `-5 * 10^4 <= nums[i] <= 5 * 10^4`

### 思路
- 不讲武德版：直接调用排序函数
```javascript
var sortArray = function(nums) {
    return nums.sort((a, b) => a - b);
};
```
要练习各种排序算法的话，这个题目就挺合适的，这里给一个归并排序的版本吧。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2bd159fb46df41c193aeaa62e5dcf87e~tplv-k3u1fbpfcp-watermark.image?)
### 代码
```javascript
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var sortArray = function(nums) {
    let merge = function(arr, s, m, e, tmp) {
        let [i, j, k] = [s, m+1, 0]
        while(i <= m && j <= e)
            tmp[k++] = arr[i] < arr[j] ? arr[i++] : arr[j++]
        while(i <= m) 
            tmp[k++] = arr[i++]
        while(j <= e)
            tmp[k++] = arr[j++]
        for(let i = 0; i < k; i++) 
            arr[s+i] = tmp[i]
    }
    let mergesort = function (arr, s, e, tmp) {
        if (s >= e) return
        let mid = (s+e)>>1
        mergesort(arr, s, mid, tmp)        // 归并左半部分
        mergesort(arr, mid + 1, e, tmp)    // 归并右半部分
        merge(arr, s, mid, e, tmp)         // 合并两部分
    }
    mergesort(nums, 0, nums.length-1, new Array(nums.length))
    return nums
};
```
## [887. 鸡蛋掉落](https://leetcode-cn.com/problems/super-egg-drop/)

给你 `k` 枚相同的鸡蛋，并可以使用一栋从第 `1` 层到第 `n` 层共有 `n` 层楼的建筑。

已知存在楼层 `f` ，满足 `0 <= f <= n` ，任何从 **高于** `f` 的楼层落下的鸡蛋都会碎，从 `f` 楼层或比它低的楼层落下的鸡蛋都不会破。

每次操作，你可以取一枚没有碎的鸡蛋并把它从任一楼层 `x` 扔下（满足 `1 <= x <= n`）。如果鸡蛋碎了，你就不能再次使用它。如果某枚鸡蛋扔下后没有摔碎，则可以在之后的操作中 **重复使用** 这枚鸡蛋。

请你计算并返回要确定 `f` **确切的值** 的 **最小操作次数** 是多少？

**示例 1：**

```
输入： k = 1, n = 2
输出： 2
解释：
鸡蛋从 1 楼掉落。如果它碎了，肯定能得出 f = 0 。 
否则，鸡蛋从 2 楼掉落。如果它碎了，肯定能得出 f = 1 。 
如果它没碎，那么肯定能得出 f = 2 。 
因此，在最坏的情况下我们需要移动 2 次以确定 f 是多少。 
```

**示例 2：**

```
输入： k = 2, n = 6
输出： 3
```

**示例 3：**

```
输入： k = 3, n = 14
输出： 4
```

**提示：**
-   `1 <= k <= 100`
-   `1 <= n <= 10^4`

### 思路
- 又是一道完全没有思路的题，看了题解之后，感觉第三种解法最好理解，推荐阅读：[题目理解 + 基本解法 + 进阶解法 - 鸡蛋掉落](https://leetcode-cn.com/problems/super-egg-drop/solution/ji-ben-dong-tai-gui-hua-jie-fa-by-labuladong/)
- 逆向思维，将问题转换为 `k` 个鸡蛋，最少掉落次数为 `f` 次，最大能测的楼层数 `n` 是多少
    - 当 `k == 1` 即只有一个鸡蛋或 `f == 1` 即只有一次掉落机会时，最大楼层数为 `f+1`
    - 其他时候，最大楼层数为鸡蛋碎了的情况，与鸡蛋没碎的情况下能测的最大楼层数加起来
- 当最大能测的楼层数为 `n` 时，我们就返回当前 `f`
### 代码
```javascript
/**
 * @param {number} k
 * @param {number} n
 * @return {number}
 */
var superEggDrop = function(k, n) {
    let check = function(k, f) {    // k个鸡蛋，f次掉落次数，返回最大的n
        // 1个鸡蛋或1次掉落，最大n为f+1
        if(k === 1 || f === 1) return f + 1;
        return check(k-1, f-1) + check(k, f-1)
    }
    let f = 1
    while(check(k, f) <= n) ++f
    return f
};
```

---

# day22

#数据结构/链表  #算法/回溯 

day22题目：[151. 颠倒字符串中的单词](https://leetcode-cn.com/problems/reverse-words-in-a-string/)、[46. 全排列](https://leetcode-cn.com/problems/permutations/)、[2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

今日知识点：字符串、递归、链表，难度为中等、中等、中等

学习计划链接：[冲刺春招-精选笔面试 66 题大通关](https://leetcode-cn.com/study-plan/bytedancecampus/?progress=dcmyjb3)

昨日题目链接：[冲刺春招-精选笔面试 66 题大通关 day21](https://juejin.cn/post/7080127605222932517)

## [151. 颠倒字符串中的单词](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

给你一个字符串 `s` ，颠倒字符串中 **单词** 的顺序。

**单词** 是由非空格字符组成的字符串。`s` 中使用至少一个空格将字符串中的 **单词** 分隔开。

返回 **单词** 顺序颠倒且 **单词** 之间用单个空格连接的结果字符串。

**注意：** 输入字符串 `s`中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。

**示例 1：**

```
输入： s = "the sky is blue"
输出： "blue is sky the"
```

**示例 2：**

```
输入： s = "  hello world  "
输出： "world hello"
解释： 颠倒后的字符串中不能存在前导空格和尾随空格。
```

**示例 3：**

```
输入： s = "a good   example"
输出： "example good a"
解释： 如果两个单词间有多余的空格，颠倒后的字符串需要将单词间的空格减少到仅有一个。
```

**提示：**

-   `1 <= s.length <= 104`
-   `s` 包含英文大小写字母、数字和空格 `' '`
-   `s` 中 **至少存在一个** 单词


**进阶：** 如果字符串在你使用的编程语言中是一种可变数据类型，请尝试使用 `O(1)` 额外空间复杂度的 **原地** 解法。

### 思路
不讲武德版：一行代码，先去除前后空格，再利用正则将字符串从空白处分割后反转再拼回去
```javascript
var reverseWords = function(s) {
    return s.trim().split(/[ ]+/).reverse().join(' ').trim();
};
```

`O(1)` 额外空间复杂度的 **原地** 解法：JS字符串不可变，需要O(n)的空间将字符串转换为数组，不行，换成c++的话就是O(1)了。
- 双指针进行反转
```javascript
let reverse = function(str, s, e) {
    while(s < e) {
        [str[s], str[e]] = [str[e], str[s]]
        ++s, --e
    }
}
```
- 将字符串转换为数组`ans`的同时去除前后空格和多余空格 `trim2arr`
- 反转整个数组 `reverse(ans, 0, ans.length - 1)`
- 反转每个单词使单词内部顺序保持不变 `reverseWord(ans)`

### 代码
```javascript
/**
 * @param {string} s
 * @return {string}
 */
var reverseWords = function(s) {
    let trim2arr = function(str) {
        let arr = []
        let [l, r] = [0, 0]
        while(l < str.length) {
            while(l < str.length && str[l] === ' ') ++l
            r = l
            while(r < str.length && str[r] !== ' ') ++r
            arr.push(...str.slice(l, r), ' ')
            l = r
        }
        while(arr[arr.length-1] == ' ') arr.pop()
        return arr
    }
    let reverse = function(str, s, e) {
        while(s < e) {
            [str[s], str[e]] = [str[e], str[s]]
            ++s, --e
        }
    }
    let reverseWord = function(arr) {
        let [l, r] = [0, 0]
        while(l < arr.length) {
            while(l < arr.length && arr[l] === ' ') ++l
            r = l
            while(r < arr.length && arr[r] !== ' ') ++r
            reverse(arr, l, r - 1)
            l = r
        }
    }
    let ans = trim2arr(s)               // JS字符串不可变，需要转换成数组
    reverse(ans, 0, ans.length - 1)     // 反转整个字符串
    reverseWord(ans)                    // 反转每个单词
    return ans.join('')                 // 数组转换成字符串
};
```
## [46. 全排列](https://leetcode-cn.com/problems/permutations/)

给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

**示例 1：**

```
输入： nums = [1,2,3]
输出： [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**示例 2：**

```
输入： nums = [0,1]
输出： [[0,1],[1,0]]
```

**示例 3：**

```
输入： nums = [1]
输出： [[1]]
```

 

**提示：**

-   `1 <= nums.length <= 6`
-   `-10 <= nums[i] <= 10`
-   `nums` 中的所有整数 **互不相同**

### 思路

### 代码
```javascript
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
 var permute = function(nums) {
    let ans = []
    let len = nums.length
    if (len === 0) return ans
    if (len === 1) return [nums]
    let vis = new Array(len).fill(false)
    let permu = function(arr) {
        if (arr.length === len) {
            ans.push(arr.slice())
            return
        }
        for (let i = 0; i < len; i++) {
            if (vis[i]) continue
            vis[i] = true
            arr.push(nums[i])
            permu(arr)
            arr.pop()
            vis[i] = false
        }
    }
    permu([])
    return ans
};
```

## [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例 1：**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/83bf665da88c4f18b7a726d818ca51fa~tplv-k3u1fbpfcp-zoom-1.image)

```
输入： l1 = [2,4,3], l2 = [5,6,4]
输出： [7,0,8]
解释： 342 + 465 = 807.
```

**示例 2：**

```
输入： l1 = [0], l2 = [0]
输出： [0]
```

**示例 3：**

```
输入： l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出： [8,9,9,9,0,0,0,1]
```

**提示：**
-   每个链表中的节点数在范围 `[1, 100]` 内
-   `0 <= Node.val <= 9`
-   题目数据保证列表表示的数字不含前导零

### 思路
跟大数加没什么区别，就是换成了链表而已~
### 代码
```javascript
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var addTwoNumbers = function(l1, l2) {
    let ehead = new ListNode()
    let nowp = ehead 
    let flag = 0    // 进位
    while(l1 || l2 || flag) {
        let x = l1? l1.val: 0
        let y = l2? l2.val: 0
        let sum = x + y + flag
        flag = sum >= 10? 1: 0
        nowp.next = new ListNode(sum % 10)
        nowp = nowp.next
        if(l1) l1 = l1.next
        if(l2) l2 = l2.next
    }
    return ehead.next
};
```


好耶！完成！
![小徽章.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a14ef457f04f4d85a697dc13ca164927~tplv-k3u1fbpfcp-watermark.image?)

明天开始剑指offer的刷题

刷完剑指offer就去刷剑指的专项