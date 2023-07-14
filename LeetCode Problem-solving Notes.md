#  数组

## 数组理论基础

**·数组的在内存空间的地址是连续的，所以我们在删除或者增添元素的时候，就难免要移动其他元素的地址。**

**·二维数组的存储地址是连续的吗？   ----取决于语言**

以C++为例，在C++中二维数组是连续分布的

![image-20230410193543213](C:\Users\kobedai\AppData\Roaming\Typora\typora-user-images\image-20230410193543213.png)

像Java是没有指针的，同时也不对程序员暴露其元素的地址，二维数组的每一行头结点的地址是没有规则的，更谈不上连续。![image-20230410193620264](C:\Users\kobedai\AppData\Roaming\Typora\typora-user-images\image-20230410193620264.png)

## 二分查找 -- 二分法

### 704.二分查找  Easy

“两端闭区间，重置需+1”

```cpp
class Solution {
public:  //num有序
    int search(vector<int>& nums, int target) {
        int ans=-1;
        int l=0, r=nums.size()-1, mid=(l+r)/2;
        while(l<=r){         //加上'='特判数组长为1且满足的情况
            mid = (l+r)/2;
            if(nums[mid] == target){
                ans=mid;    break;
            }else if(nums[mid] < target){
                l=mid+1;     //注意这里不+1会死循环
            }else{
                r=mid-1;
            }
        }
        return ans;
    }
};
```

### 35. 搜索插入位置  Easy

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int ans=0;
        bool flag=false;
        int l=0, r=nums.size()-1, mid=(l+r)/2;
        while(l<=r){
            mid = (l+r)/2;
            if(nums[mid] == target){
                ans = mid; flag=true; break;
            }else if(nums[mid] < target){
                l=mid+1;
            }else{
                r=mid-1;
            }
        }
        if(!flag){
            ans = r+1;	//写几个例子试一下即可
        }
        return ans;
    }
};
```

### 34.查找元素的第一个和最后一个位置  Medium

给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int l=0, r=nums.size()-1, mid=(l+r)/2;
        bool flag = false;
        vector<int> ans;
        while(l<=r){
            mid = (l+r)/2;
            if(nums[mid] == target){
                flag=true;
                break;
            }else if(nums[mid] < target){
                l=mid+1;
            }else{
                r=mid-1;
            }
        } 
        
        if(flag){    //分别向左，向右寻找端点
        	int left,right,m;
	
	        left = l; right = mid;
	        while(left<=right){
	            m = (left+right)/2;
	            if(nums[m] == target){
	                right = m-1;
	            }
	            else if(nums[m] < target){
	                left = m+1;
	            }
	        }
	        ans.push_back(left);  //向左找，返回左端点
	
	        left = mid; right = r;
	        while(left<=right){
	            m = (left+right)/2;
	            if(nums[m] == target){
	                left = m+1;
	            }
	            else if(nums[m] > target){
	                right = m-1;
	            }
	        }
	        ans.push_back(right); //向右找，返回右端点
		}

        else{
             ans.push_back(-1);      ans.push_back(-1);
        }
        return ans;
    }
};
```

### 69. x 的平方根  Easy

只保留 **整数部分**

```cpp
class Solution {
public:
    int mySqrt(int x) {
        int ans;
        double l=0, r=x, mid=(l+r)/2;
        const double miss = 1e-5;
        while(l<=r){
            mid = (l+r)/2;
            if(l*l==x)  return l; //特判:若无，求1的平方根会输出0
            if(r*r==x)  return r;
            
            if(mid*mid-x <= miss && mid*mid-x >= -miss){
                ans =  (int)mid;    break;
            }else if(mid*mid-x > miss){
                r=mid;
            }else {
                l=mid;
            }
        }
        return ans;
    }
};
```

### 367.有效的完全平方数  Easy

给你一个正整数 `num` 。如果 `num` 是一个完全平方数，则返回 `true` ，否则返回 `false` 。

```cpp
class Solution {
public:
    bool isPerfectSquare(int num) {
        bool flag=false;
        long long l=0,r=num,mid=(l+r)/2; //开longlong源于前期mid^2会很大
        while(l<=r){
            mid=(l+r)/2;
            if(mid*mid==num){
                flag=true;  break;
            }else if(mid*mid<num){
                l=mid+1;
            }else{
                r=mid-1;
            }
        }
        return flag;
    }
};
```

## 移除元素 -- 双指针

### 27. 移除元素  Easy

给你一个数组 nums 和一个值 val，你需要 **原地** 移除所有数值等于 val 的元素，并返回移除后数组的新长度。不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并**原地**修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

```cpp
class Solution {   //双指针算法
public:
    int removeElement(vector<int>& nums, int val) {
        int leftIndex = 0, rightIndex = nums.size() - 1;
        while (leftIndex <= rightIndex) {
            // 找左边等于val的元素
            while (leftIndex <= rightIndex && nums[leftIndex] != val){
                ++leftIndex;
            }
            // 找右边不等于val的元素
            while (leftIndex <= rightIndex && nums[rightIndex] == val){
                -- rightIndex;
            }
            // 将左边等于val的元素换到队末去(核心)
            if (leftIndex < rightIndex) {
                swap(nums[leftIndex],nums[rightIndex]);
                leftIndex++;  rightIndex--;
            }
        }
        return (leftIndex); // leftIndex一定指向了最终数组末尾的下一个元素
    }
};
```

### 26.删除排序数组中的重复项  Easy

给你一个 **升序排列** 的数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使每个元素 **只出现一次** ，返回删除后数组的新长度。元素的 **相对顺序** 应该保持 **一致** 。

由于在某些语言中不能改变数组的长度，所以必须将结果放在数组nums的第一部分。更规范地说，如果在删除重复项之后有 `k` 个元素，那么 `nums` 的前 `k` 个元素应该保存最终结果。

将最终结果插入 `nums` 的前 `k` 个位置后返回 `k` 。

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int k = nums.size();
        int l = 0, r = 1;
        int cnt = 1;
        while(r < nums.size()){
            while(r<nums.size() && nums[l]==nums[r]){  //注意越界条件检查
                r++;    k--;     //r指向第一个不为l的元素
            }
            if(r<nums.size()) 	nums[cnt]=nums[r];
            l=r;   r++;
            cnt++;			    //每个值只保留一个，故cnt指针每次循环后移一格
        }
        return k;
    }
};
```

### 283.移动零  Easy

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**请注意** ，必须在不复制数组的情况下原地对数组进行操作。

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int l=0, r=0;
        while(r < nums.size()){
            while(l < nums.size() && nums[l] != 0)  l++;   //找到第一个0
            r = l;
            while(r < nums.size() && nums[r] == 0)  r++;   //找到之后第一个不为0的元素
            if(r < nums.size())     swap(nums[l],nums[r]); //交换(0往后放)
        }
    }
};
```

### 844.比较含退格的字符串  Easy

给定 `s` 和 `t` 两个字符串，当它们分别被输入到空白的文本编辑器后，如果两者相等，返回 `true` 。`#` 代表退格字符。

**注意：**如果对空文本输入退格字符，文本继续为空。

```cpp
class Solution {   //用栈做，相当自然的想法
public:
    bool backspaceCompare(string S, string T) {
        return (build(S) == build(T));
    }

    string build(string str) {
        string ret;
        for (char ch : str) {
            if (ch != '#') {
                ret.push_back(ch);
            } else if (!ret.empty()) {
                ret.pop_back();   //“回退”过程
            }
        }
        return ret;
    }
};
```

## 有序数组的平方

### 977.有序数组的平方  Easy

给你一个按 **非递减顺序** 排序的整数数组 `nums`，返回 **每个数字的平方** 组成的新数组，要求也按 **非递减顺序** 排序。

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        vector<int> ans;
        int i;
        //先把最接近0的数位置找到
        for(i=0; i<nums.size()-1 && abs(nums[i+1])<=abs(nums[i]); i++);
        int l=i,r=i;
        ans.push_back(nums[l]*nums[l]);
        l--;    r++;
        //从中间往两边一次找平方小的push_back（双指针）
        while(l>=0 && r<nums.size()){
            if(nums[l]*nums[l]<=nums[r]*nums[r]){
                ans.push_back(nums[l]*nums[l]);
                l--;
            }else{
                ans.push_back(nums[r]*nums[r]);
                r++;
            }
        }
        //把还剩下的push_back
        while(l>=0){
            ans.push_back(nums[l]*nums[l]);
            l--;
        }
        while(r<nums.size()){
            ans.push_back(nums[r]*nums[r]);
            r++;
        }
        return ans;
    }        
};
```

## *长度最小的子数组 -- 滑动窗口

### *滑动窗口思路总结：*

0.左、右端点都是单调改变的，故复杂度为O(n)

1.(无论如何)右端点一步一步往外扩【枚举变量】

2.在保证条件的情况下，左端点尽可能内收，缩小窗口

3.调整到窗口正好达到临界情况

4.更新所求量的极值



### 209.长度最小的子数组  Medium

给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**

找出该数组中满足其和 `≥ target` 的长度最小的 **连续子数组** `[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。

```cpp
//标答：滑动窗口(双指针)  O(n)
//滑动窗口的标准思路：右扩左缩
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int result = 1e9;
        int sum = 0;       // 滑动窗口数值之和
        int i = 0;         // 滑动窗口起始位置
        int subLength = 0; // 滑动窗口的长度
        //找到每一个j结尾窗口对应的最大起点i，i/j都是单调的，故复杂度为O(n)
        for (int j = 0; j < nums.size(); j++) {
            sum += nums[j];
            // 每次更新 i（起始位置），并不断比较子序列是否符合条件
            while (sum >= s) {
                subLength = (j - i + 1); 
                result = min(result, subLength);
                sum -= nums[i++]; // 这里体现出滑动窗口的精髓之处，不断变更i（子序列的起始位置）
            }
        }
        // 如果result没有被赋值的话，说明没有符合条件的子序列
        return result == 1e9 ? 0 : result;
    }
};
```

```cpp
//我的解答O(n)：前缀和+二分(查找对每个s[i]满足Σi~j>target最小的j)  
//实际运行效率优于滑动窗口
class Solution {
public:
    int binary_search(vector<int> &nums, int target, int left, int right){
        int l=left, r=right;
        while(l<=r){
            int mid=(l+r)/2;
            if(nums[mid]==target)       return mid;
            else if(nums[mid]<target)   l=mid+1;
            else                        r=mid-1;
        }
        return l;
    }
    
    int minSubArrayLen(int target, vector<int>& nums) {
        int len = nums.size();
        int s[len+1];
        s[0]=0;
        for(int i=1;i<=len;i++){
            s[i] = s[i-1]+nums[i-1];
        }
        if(s[len]<target)   return 0;

        int ans = 1e9;
        for(int i=0;i<len;i++){
            if(s[len]-s[i]<target){
                break;
            }else{
                int j = binary_search(nums,s[i]+target,i,len);
                ans = min(ans,j-i);
            }            
        }        
        return ans;
    }
};
```

### 904.水果成篮  Medium

你正在探访一家农场，农场从左到右种植了一排果树。这些树用一个整数数组 `fruits` 表示，其中 `fruits[i]` 是第 `i` 棵树上的水果 **种类** 。

你想要尽可能多地收集水果。然而，农场的主人设定了一些严格的规矩，你必须按照要求采摘水果：

- 你只有 **两个** 篮子，并且每个篮子只能装 **单一类型** 的水果。每个篮子能够装的水果总量没有限制。
- 你可以选择任意一棵树开始采摘，你必须从 **每棵** 树（包括开始采摘的树）上 **恰好摘一个水果** 。采摘的水果应当符合篮子中的水果类型。每采摘一次，你将会向右移动到下一棵树，并继续采摘。
- 一旦你走到某棵树前，但水果不符合篮子的水果类型，那么就必须停止采摘。

给你一个整数数组 `fruits` ，返回你可以收集的水果的 **最大** 数目。

```cpp
class Solution {
public:
    //原题等价于，找到由两种元素组成的最长子串
    int totalFruit(vector<int>& fruits) {
        int ans=0;
        int l,r;
        //按滑动窗口起点枚举
        if(fruits.size()==1)    return 1;  //特判
        int num1, num2, cnt=1;
        for(l=0,r=1; l<fruits.size(); cnt=1){
            num1 = fruits[l];
            while(r+1<fruits.size() && fruits[r]==fruits[l])    r++;                    
            if(r<fruits.size())    
                num2 = fruits[r];
            while(r+1<fruits.size() && (fruits[r+1]==num1 || fruits[r+1]==num2)){
                //cnt动态记录到每一个r为止最长的由nums[r]构成的串长
                if(fruits[r+1]==fruits[r])  cnt++;
                else                        cnt=1;
                r++;
            }    
            ans = max(ans,r-l+1);
            //(核心)l的回退：如上一个窗口结束时为1 6 1 6 6，则l须回退到连续的第一个6，而不必更向前回退(包含在现有答案里)
            l=r-cnt+1;   r=l+1;
        }
        return ans;
    }
};
```

### 76. 最小覆盖子串 Hard

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` **所有字符**的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""`

**注意：**

- 对于 `t` 中重复字符，我们寻找的**子字符串中该字符数量必须不少于 `t` 中**该字符数量。

- 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

  
  
- Q：还能怎样优化？

  A：对s串做预处理，”扔掉”s中本就不属于t的字符。
  
  ```cpp
  class Solution {
  public:
      string minWindow(string s, string t) {
          unordered_map<char, int> hs, ht;
          for(auto c: t)		   ht[c]++;
          string res;
          //(核心)cnt记录s子串中的“有效”字符总数，即当cnt=t.length时结束此窗口
          int cnt = 0;
          for (int l = 0, r = 0; r<s.length(); r++){
              hs[s[r]]++;  //后移右端点
              //如果添加s[r]是“必需”的,那么有效计数器cnt+1
              if (hs[s[r]] <= ht[s[r]]) 	   cnt++;
  		   //在符合条件的前提下，尽量后移左端点缩短区间
              while (hs[s[l]] > ht[s[l]])    hs[s[l++]]--;
              //以r为结尾的窗口枚举完毕，更新结果
              if (cnt == t.length()){
                  if (res.empty() || r-l+1 < res.size())
                      res = s.substr(l, r-l+1);
                  	//注意substr参数：起点+长度
              }
          }
          return res;
      }
  };
  ```
  

## 螺旋矩阵II -- 模拟

### 59.螺旋矩阵II  Medium

给你一个正整数 `n` ，生成一个包含 `1` 到 `n2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 。

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> mat(n, vector<int>(n)); //二维数组的初始化
        int sts=0;                      //状态码，0向右，1向下，2向左，3向上
        int i=1;
        int ub=0, db=n-1, lb=0, rb=n-1; //up.down.left.right_boundary
        while(i <= n*n){
            int cnt = 0;
            if(sts == 0){
                while(lb+cnt <= rb){
                    mat[ub][lb+cnt] = i;	i++;	cnt++;
                }
            }else if(sts == 1){
                while(ub+cnt <= db){
                    if(cnt != 0){       //每次改变方向的第一个点(拐角)不能重复初始化
                        mat[ub+cnt][rb] = i;	i++;
                    }
                    cnt++;   
                }
            }else if(sts == 2){
                while(rb-cnt >= lb){
                    if(cnt != 0){
                        mat[db][rb-cnt] = i;	i++;
                    }
                    cnt++; 
                }
            }else if(sts == 3){
                while(db-cnt >= ub+1){
                    if(cnt != 0){
                       mat[db-cnt][lb] = i; 	i++;
                    }
                    cnt++; 
                }
            }

            if(sts==3){rb--; db--; lb++; ub++;}  //走完一圈，更新边界
            sts = (sts+1)%4;           //类比循环队列的处理方式
        }
        return mat;
    }
};
```

### 54.螺旋矩阵  Medium

给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector <int> ans;
        if(matrix.empty()) return ans; //若数组为空，直接返回答案
        int u = 0; //赋值上下左右边界
        int d = matrix.size() - 1;
        int l = 0;
        int r = matrix[0].size() - 1;
        while(true)
        {
            for(int i = l; i <= r; ++i) ans.push_back(matrix[u][i]); //向右移动直到最右
            if(++ u > d) break; //重设上边界，若上边界大于下边界，则遍历遍历完成，下同
            for(int i = u; i <= d; ++i) ans.push_back(matrix[i][r]); //向下
            if(-- r < l) break; //重设右边界
            for(int i = r; i >= l; --i) ans.push_back(matrix[d][i]); //向左
            if(-- d < u) break; //重设下边界
            for(int i = d; i >= u; --i) ans.push_back(matrix[i][l]); //向上
            if(++ l > r) break; //重设左边界
        }
        return ans;
    }
};
```

# 链表

## 链表理论基础

leetcode链表的节点都默认定义好了，直接用就行，但还是要熟悉自己手写链表

**·C/C++的定义链表节点方式**

```cpp
// 单链表
struct ListNode {
    int val;  // 节点上存储的元素
    ListNode *next;  // 指向下一个节点的指针
    ListNode(int x) : val(x), next(NULL) {}  // 节点的构造函数
};
```

不定义构造函数行不行，答案是可以的，C++默认生成一个构造函数。

但是这个构造函数不会初始化任何成员变量，下面举两个例子：

```cpp
//通过自己定义构造函数初始化节点：
ListNode* head = new ListNode(5);
```

```cpp
//使用默认构造函数初始化节点：
ListNode* head = new ListNode();
head->val = 5;
```

所以如果不定义构造函数使用默认构造函数的话，在初始化的时候就不能直接给变量赋值。

## 移除链表元素 -- 虚拟头指针

### 203.移除链表元素  Easy

给你一个链表的头节点 `head` 和一个整数 `val` ，请你删除链表中所有满足 `Node.val == val` 的节点，并返回 **新的头节点** 。

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if(head == NULL)    return head;
        //快慢双指针删除
        ListNode* s = (ListNode*)malloc(sizeof(ListNode));
        ListNode* f = (ListNode*)malloc(sizeof(ListNode));
        s = head, f = s->next;
        while(s && f){
            if(f->val == val){
                s->next = f->next;
                f = f->next;
            }else if(f->val != val){
                s = f;
                if(s != NULL)  f = s->next;
            }
        }
        if(head->val == val)  head = head->next;
        return head; 
    }
};
```

**Better Solution：**

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        ListNode* dummyHead = new ListNode(); //设置一个虚拟头结点
        //核心
        dummyHead->next = head; 
        ListNode* cur = dummyHead;
        while (cur->next != NULL) {
            //由于虚拟头指针的存在，可以统一用一个if判定
            if(cur->next->val == val) {
                cur->next = cur->next->next;
            } else {
                cur = cur->next;
            }
        }
        head = dummyHead->next;
        delete dummyHead;
        return head;
    }
};
```

## 设计链表

###  707.设计链表  Medium

你可以选择使用单链表或者双链表，设计并实现自己的链表。

单链表中的节点应该具备两个属性：`val` 和 `next` 。`val` 是当前节点的值，`next` 是指向下一个节点的指针/引用。

如果是双向链表，则还需要属性 `prev` 以指示链表中的上一个节点。假设链表中的所有节点下标从 **0** 开始。

实现 `MyLinkedList` 类：

- `MyLinkedList()` 初始化 `MyLinkedList` 对象。
- `int get(int index)` 获取链表中下标为 `index` 的节点的值。如果下标无效，则返回 `-1` 。
- `void addAtHead(int val)` 将一个值为 `val` 的节点插入到链表中第一个元素之前。在插入完成后，新节点会成为链表的第一个节点。
- `void addAtTail(int val)` 将一个值为 `val` 的节点追加到链表中作为链表的最后一个元素。
- `void addAtIndex(int index, int val)` 将一个值为 `val` 的节点插入到链表中下标为 `index` 的节点之前。如果 `index` 等于链表的长度，那么该节点会被追加到链表的末尾。如果 `index` 比长度更大，该节点将 **不会插入** 到链表中。
- `void deleteAtIndex(int index)` 如果下标有效，则删除链表中下标为 `index` 的节点。

```cpp
//对于边界条件的判断一定要仔细，是否要判断p==NULL? 还是p->next==NULL? 要做到不重不漏
class MyLinkedList {
public:
    //定义节点
    struct Node {
        int val;
        Node* next;
        Node(int val):val(val), next(NULL){}
    };

    MyLinkedList() {
        head = new Node(0);   //虚拟头结点(在这一题的类定义情形中，这是必需的)
    }
    
    int get(int index) {
        Node* p = head->next;
        int i=0;
        while(i<index && p->next!=NULL){
            p = p->next;
            i++;
        }
        if(i<index || p==NULL)    return -1;
        else    return p->val;
    }
    
    void addAtHead(int val) {
        Node* tmp = new Node(val);
        tmp->next = head->next;
        head->next = tmp;
    }
    
    void addAtTail(int val) {
        Node* p = head;
        while(p->next != NULL){
            p = p->next;
        }
        Node* tmp = new Node(val);
        tmp->next = NULL;
        p->next = tmp;
    }
    
    void addAtIndex(int index, int val) {
        Node* p = head;
        int i=0;
        while(i<index && p->next!=NULL){
            p = p->next;
            i++;
        }
        if(p->next!=NULL || (p->next==NULL && i==index)){
            Node* tmp = new Node(val);
            tmp->next = p->next;
            p->next = tmp;
        }   
    }
    
    void deleteAtIndex(int index) {
        Node* p = head;
        int i=0;
        while(i<index && p->next!=NULL){
            p = p->next;
            i++;
        }
        if(p->next != NULL)
            p->next = p->next->next;
    }
//定义类变量(虚拟头结点)
private: 
    Node* head;
};
```

## 反转链表

### 206. 反转链表  Easy

给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

![image-20230414224419313](C:\Users\kobedai\AppData\Roaming\Typora\typora-user-images\image-20230414224419313.png)

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* temp;        
        ListNode* cur = head;  // 前后双指针
        ListNode* pre = NULL;
        while(cur) {
            temp = cur->next;  // 保存cur的下一个节点，因为接下来要改变cur->next
            cur->next = pre;   // 翻转操作
            // 更新pre 和 cur指针
            pre = cur;
            cur = temp;
        }
        return pre;
    }
};
```

**·另解：所有结点入栈再出栈**<u>***（翻转）***</u>

## 两两交换链表中的节点

### 24. 两两交换链表中的节点  Medium

给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

<img src="C:\Users\kobedai\AppData\Roaming\Typora\typora-user-images\image-20230417164418394.png" alt="image-20230417164418394" style="zoom:50%;" />

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        // l,r双指针每次指向要交换的两个结点
        ListNode* l = head, *r;
        // tmp1,tmp2指向交换中会丢失的结点信息
        ListNode* tmp1, *tmp2;
        if(head==NULL || head->next==NULL)  return head;
        r = head->next;     head = r;   
        
        while(r!=NULL && r->next!=NULL){
            tmp2 = r->next;
            if(tmp2==NULL)  break;
            r->next = l;
            tmp1 = r;
            l = tmp2;
            r = l->next;
            tmp1->next->next = r;
        }
        
        if(r == NULL){
            tmp1->next->next = l;
            l->next = NULL;
        } else {
            r->next = l;
            l->next = NULL;
        }
        return head;
    }
};  // 本题也可以使用虚拟头结点，一定程度上可以简化码量
```

## 删除链表的倒数第N个节点

### 19. 删除链表的倒数第 N 个结点  Medium

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。(要求O(n)复杂度)

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        int cnt = n;
        // 快慢双指针
        ListNode *s = head, *f = head;
        // 先让快指针走n步
        while(cnt--)    f = f->next;
        if(f == NULL)   return head->next;
        // 快慢指针同时移动直到快指针到头，这样慢指针刚好走 N-n 步
        while(f->next!=NULL){
            s = s->next;
            f = f->next;
        }
        s->next = s->next->next;
        return head;
    }
};
```

## 链表相交

### 面试题 02.07. 链表相交  Easy

给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 `null` 。

<img src="C:\Users\kobedai\AppData\Roaming\Typora\typora-user-images\image-20230418185620176.png" alt="image-20230418185620176" style="zoom:67%;" />

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* pa= headA, *pb = headB;
        int na = 0, nb = 0;
        // 先统计A/B的长度
        while(pa != NULL){
            pa = pa->next;
            na++;
        }
        while(pb != NULL){
            pb = pb->next;
            nb++;
        }
        // 由于是“从某位置起开始相交”，先让B指针后移 Nb-Na 位，这样才能让链表“对齐”
        pa = headA; pb= headB;
        if(nb >= na){
        	int cnt = nb-na;
            while(cnt){
                pb = pb->next;
                cnt--;
            }
        } else if(nb < na) {
        	int cnt = na-nb;
            while(cnt){
                pa = pa->next;
                cnt--;
            }
        }
        // 双指针同步移动，找到相交起点即可
        while(pa!=NULL && pb!=NULL){
            if(pa == pb)    return pa;
            pa = pa->next;
            pb = pb->next;
        }
        return NULL;
    }
};
```

## 环形链表

### 142.环形链表II  Medium

给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

**不允许修改** 链表。

示例1：<img src="C:\Users\kobedai\AppData\Roaming\Typora\typora-user-images\image-20230418190319963.png" alt="image-20230418190319963" style="zoom:25%;" />  链表中第一个属于环的结点是结点“2”



#### 怎么判断有环

<img src="C:\Users\kobedai\AppData\Roaming\Typora\typora-user-images\image-20230418192121620.png" alt="image-20230418192121620" style="zoom:67%;" />

定义fast，slow指针，fast一次走两步，slow一次走一步。

1.**fast指针一定先进入环中，如果fast指针和slow指针相遇的话，一定是在环中相遇。**

2.**相对于slow来说，fast是一个节点一个节点的靠近slow的**，所以fast一定可以和slow重合。



#### 有环了怎么判断入口

**从头结点出发一个指针，从相遇节点 也出发一个指针，这两个指针每次只走一个节点， 那么当这两个指针相遇的时候就是 环形入口的节点**

#### 代码

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while(fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            // 快慢指针相遇，此时从head 和 相遇点，同时查找直至相遇
            if (slow == fast) {
                ListNode* index1 = fast;
                ListNode* index2 = head;
                while (index1 != index2) {
                    index1 = index1->next;
                    index2 = index2->next;
                }
                return index2; // 返回环的入口
            }
        }
        return NULL;
    }
};
```

# 哈希表

## 哈希表理论基础

哈希表中关键码就是数组的索引下标，然后通过下标直接访问数组中的元素。

那么哈希表能解决什么问题呢，**一般哈希表都是用来快速判断一个元素是否出现集合里。**

例如要查询一个名字是否在这所学校里。

要枚举的话时间复杂度是O(n)，但如果使用哈希表的话， 只需要O(1)就可以做到。

**常见的三种哈希结构**

当我们想使用哈希法来解决问题的时候，我们一般会选择如下三种数据结构。

- 数组
- set （集合）
- map(映射)

这里数组就没啥可说的了，我们来看一下set。

在C++中，set 和 map 分别提供以下三种数据结构，其底层实现以及优劣如下表所示：

| 集合               | 底层实现 | 是否有序 | 数值是否可以重复 | 能否更改数值 | 查询效率 | 增删效率 |
| ------------------ | -------- | -------- | ---------------- | ------------ | -------- | -------- |
| std::set           | 红黑树   | 有序     | 否               | 否           | O(log n) | O(log n) |
| std::multiset      | 红黑树   | 有序     | 是               | 否           | O(logn)  | O(logn)  |
| std::unordered_set | 哈希表   | 无序     | 否               | 否           | O(1)     | O(1)     |

std::unordered_set底层实现为哈希表，std::set 和std::multiset 的底层实现是红黑树，红黑树是一种平衡二叉搜索树，所以key值是有序的，但key不可以修改，改动key值会导致整棵树的错乱，所以只能删除和增加。

| 映射               | 底层实现 | 是否有序 | 数值是否可以重复 | 能否更改数值 | 查询效率 | 增删效率 |
| ------------------ | -------- | -------- | ---------------- | ------------ | -------- | -------- |
| std::map           | 红黑树   | key有序  | key不可重复      | key不可修改  | O(logn)  | O(logn)  |
| std::multimap      | 红黑树   | key有序  | key可重复        | key不可修改  | O(log n) | O(log n) |
| std::unordered_map | 哈希表   | key无序  | key不可重复      | key不可修改  | O(1)     | O(1)     |

std::unordered_map 底层实现为哈希表，std::map 和std::multimap 的底层实现是红黑树。同理，std::map 和std::multimap 的key也是有序的（这个问题也经常作为面试题，考察对语言容器底层的理解）。

当我们要使用集合来解决哈希问题的时候，优先使用unordered_set，因为它的查询和增删效率是最优的，如果需要集合是有序的，那么就用set，如果要求不仅有序还要有重复数据的话，那么就用multiset。

那么再来看一下map ，在map 是一个key value 的数据结构，map中，对key是有限制，对value没有限制的，因为key的存储方式使用红黑树实现的。

其他语言例如：java里的HashMap ，TreeMap 都是一样的原理。可以灵活贯通。

虽然std::set、std::multiset 的底层实现是红黑树，不是哈希表，std::set、std::multiset 使用红黑树来索引和存储，不过给我们的使用方式，还是哈希法的使用方式，即key和value。所以使用这些数据结构来解决映射问题的方法，我们依然称之为哈希法。 map也是一样的道理。



## 有效的字母异位词

### 242. 有效的字母异位词  Easy

给定两个字符串 `*s*` 和 `*t*` ，编写一个函数来判断 `*t*` 是否是 `*s*` 的字母异位词。

**注意：**若 `*s*` 和 `*t*` 中每个字符出现的次数都相同，则称 `*s*` 和 `*t*` 互为字母异位词。

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        map<char,int> hash1, hash2;
        for(int i=0; i<s.length(); i++) {    // 统计频度
            hash1[s[i]] ++;
        }
        for(int i=0; i<t.length(); i++) {
            hash2[t[i]] ++;
        }
        if(hash1.size() != hash2.size())    return false;   // 特判，s/t字符种类数不同
        for(auto t:hash1) {
            if(t.second != hash2[t.first])  return false;
        }
        return true;
    }
};
```

### 49. 字母异位词分组  Medium

给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。

**字母异位词** 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。

```cpp
class Solution {
    public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        for (auto str: strs) {
            string key = str;
            sort(key.begin(), key.end());   // 核心：排序完之后异位词都会变成同一个串，就可以将这个串作为键使用
            mp[key].push_back(str);         // mp[key]：存放所有等价于key的词的字符串
        }
        vector<vector<string>> ans;
        for (auto it = mp.begin(); it != mp.end(); it++) {
            ans.push_back(it->second);
        }
        return ans;
    }
};
```

### *438. 找到字符串中所有字母异位词  Medium

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

**异位词** 指由相同字母重排列形成的字符串（包括相同的字符串）。



**滑动窗口**

```cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        int sLen = s.size(), pLen = p.size();
        if (sLen < pLen) {
            return vector<int>();
        }

        vector<int> ans;
        vector<int> sCount(26);     // 动态维护每个字母出现的频次 
        vector<int> pCount(26);
        for (int i = 0; i < pLen; ++i) {
            ++sCount[s[i] - 'a'];   // 只初始化到了pLen长度，后面的都是0，故不影响同长度子串的比较
            ++pCount[p[i] - 'a'];
        }
        if (sCount == pCount) {
            ans.push_back(0);
        }

        for (int i = 0; i < sLen - pLen; ++i) {
            --sCount[s[i] - 'a'];
            ++sCount[s[i+pLen] - 'a'];
            if (sCount == pCount) {  // vector的“==”已经重载，可以直接使用
                ans.push_back(i + 1);
            }
        }
        return ans;
    }
};
```

## 两个数组的交集

### 349. 两个数组的交集  Easy

给定两个数组 `nums1` 和 `nums2` ，返回 *它们的交集* 。输出结果中的每个元素一定是 **唯一** 的。我们可以 **不考虑输出结果的顺序** 。

```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        vector<int> ans;
        unordered_set<int> bag, repeated;
        for(auto t: nums1) {
            bag.insert(t);
        }
        for(auto t: nums2) {  // ans存放nums1中有的数字，repeated存放已经插入ans的数字
            if(bag.find(t)!=bag.end() && repeated.find(t)==repeated.end()) {
                ans.push_back(t);
                repeated.insert(t);
            }
        }
        return ans;
    }
};
```

更简洁的代码：

```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> bag(nums1.begin(), nums1.end()); // 注意这种初始化方式
        unordered_set<int> tmp;
        for(auto t: nums2) { 
            if(bag.find(t) != bag.end()) {
                tmp.insert(t);     // 直接用set存，自动去重
            }
        }
        return vector<int> (tmp.begin(), tmp.end());   
    }
};
```

### 350. 两个数组的交集 II  Medium

给你两个整数数组 `nums1` 和 `nums2` ，请你以数组形式返回两数组的交集。返回结果中每个元素出现的次数，应与元素在两个数组中都出现的次数一致（如果出现次数不一致，则考虑取较小值）。可以不考虑输出结果的顺序。

```cpp
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        // 排序+双指针
        sort(nums1.begin(), nums1.end());
        sort(nums2.begin(), nums2.end());
        int length1 = nums1.size(), length2 = nums2.size();
        vector<int> intersection;
        int index1 = 0, index2 = 0;
        while (index1 < length1 && index2 < length2) {
            if (nums1[index1] < nums2[index2]) {
                index1++;
            } else if (nums1[index1] > nums2[index2]) {
                index2++;
            } else {
                intersection.push_back(nums1[index1]);
                index1++;
                index2++;
            }
        }
        return intersection;
    }
};
```

## 快乐数 

### 202.快乐数  Easy

编写一个算法来判断一个数 `n` 是不是快乐数。

**「快乐数」** 定义为：

- 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
- 然后重复这个过程直到这个数变为 1，也可能是 **无限循环** 但始终变不到 1。
- 如果这个过程 **结果为** 1，那么这个数就是快乐数。

如果 `n` 是 *快乐数* 就返回 `true` ；不是，则返回 `false` 。

**分析：**如果n不是快乐数，则在变幻中一定会出现循环，从而陷入死循环中。

<img src="C:\Users\kobedai\AppData\Roaming\Typora\typora-user-images\image-20230503152825308.png" alt="image-20230503152825308" style="zoom:67%;" />

```cpp
class Solution {
public:
    // 取数值各个位上的单数之和
    int getSum(int n) {
        int sum = 0;
        while (n) {
            sum += (n % 10) * (n % 10);
            n /= 10;
        }
        return sum;
    }
    bool isHappy(int n) {
        unordered_set<int> set;
        while(1) {
            int sum = getSum(n);
            if (sum == 1) {
                return true;
            }
            // 如果这个sum曾经出现过，说明已经陷入了无限循环了，立刻return false
            if (set.find(sum) != set.end()) {
                return false;
            } else {
                set.insert(sum);
            }
            n = sum;
        }
    }
};
```

## 两数之和

### 1.两数之和  Easy

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

```cpp
class Solution {
// 查找逻辑是值，返回的是下标，自然在两者间建立哈希 
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map <int,int> map;  // <值,下标>键值对
        vector<int> ans;
        for(int i = 0; i < nums.size(); i++) {
            // 遍历当前元素，并在map中寻找是否有匹配的key
            auto it = map.find(target - nums[i]); 
            if(it != map.end()) {
                ans.push_back(it->second);
                ans.push_back(i);
            }
            // 如果没找到匹配对，就把访问过的元素和下标加入到map中
            map.insert({nums[i], i}); 
        }
        return ans;
    }
};
```

## 四数相加

### 454.四数相加II  Medium

给你四个整数数组 `nums1`、`nums2`、`nums3` 和 `nums4` ，数组长度都是 `n` ，请你计算有多少个元组 `(i, j, k, l)` 能满足：

- `0 <= i, j, k, l < n`
- `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`

**解题步骤：**

1. 首先定义 一个unordered_map，key放a和b两数之和，value 放a和b两数之和出现的次数。
2. 遍历大A和大B数组，统计两个数组元素之和，和出现的次数，放到map中。
3. 定义int变量count，用来统计 a+b+c+d = 0 出现的次数。
4. 在遍历大C和大D数组，找到如果 0-(c+d) 在map中出现过的话，就用count把map中key对应的value也就是出现次数统计出来。
5. 最后返回统计值 count 就可以了

```cpp
class Solution {
public:
    int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {
        unordered_map<int, int> umap; // key:a+b的数值，value:a+b数值出现的次数
        // 遍历大A和大B数组，统计两个数组元素之和，和出现的次数，放到map中
        for (int a : A) {
            for (int b : B) {
                umap[a + b]++;
            }
        }
        int count = 0; // 统计a+b+c+d = 0 出现的次数
        // 在遍历大C和大D数组，找到如果 0-(c+d) 在map中出现过的话，就把map中key对应的value也就是出现次数统计出来。
        for (int c : C) {
            for (int d : D) {
                if (umap.find(0 - (c + d)) != umap.end()) {
                    count += umap[0 - (c + d)];
                }
            }
        }
        return count;
    }
};
```

## 赎金信

### 383.赎金信  Easy

给你两个字符串：`ransomNote` 和 `magazine` ，判断 `ransomNote` 能不能由 `magazine` 里面的字符构成。

如果可以，返回 `true` ；否则返回 `false` 。

`magazine` 中的每个字符只能在 `ransomNote` 中使用一次。

```cpp
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        unordered_map<char,int> hash;
        for(int i=0; i<magazine.length(); i++) {
            hash[magazine[i]] ++;
        }
        for(int i=0; i<ransomNote.length(); i++) {
            if(hash.find(ransomNote[i])==hash.end() || hash[ransomNote[i]]==0)
                return false;
            else
                hash[ransomNote[i]] --;
        }
        return true;
    }
};
```

## 三数之和

### 15.三数之和  Medium

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请你返回所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        // 找出[a,b,c]使a+b+c=0
        // a = nums[i], b = nums[j], c = -(a + b)
        for (int i = 0; i < nums.size(); i++) {
            // 排序之后如果第一个元素已经大于零，那么不可能凑成三元组
            if (nums[i] > 0) {
                break;
            }
            if (i > 0 && nums[i] == nums[i - 1]) { //三元组元素a去重
                continue;
            }
            unordered_set<int> set;
            for (int j = i + 1; j < nums.size(); j++) {
                if (j > i + 2 && nums[j] == nums[j-1] && nums[j-1] == nums[j-2]) { // 三元组元素b去重
                    continue;
                }
                int c = 0 - (nums[i] + nums[j]);
                if (set.find(c) != set.end()) {
                    result.push_back({nums[i], nums[j], c}); // 注意vector<vector<>>的插入方式
                    set.erase(c); // 三元组元素c去重
                } else {
                    set.insert(nums[j]);
                }
            }
        }
        return result;
    }
};
```

## 四数之和

### 18.四数之和  Medium

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：

- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` **互不相同**
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

你可以按 **任意顺序** 返回答案 。

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        for (int k = 0; k < nums.size(); k++) {
            // 剪枝处理
            if (nums[k] > target && nums[k] >= 0) {
            	break; // 这里使用break，统一通过最后的return返回
            }
            // 对nums[k]去重
            if (k > 0 && nums[k] == nums[k - 1]) {
                continue;
            }
            for (int i = k + 1; i < nums.size(); i++) {
                // 2级剪枝处理
                if (nums[k] + nums[i] > target && nums[k] + nums[i] >= 0) {
                    break;
                }
                // 对nums[i]去重
                if (i > k + 1 && nums[i] == nums[i - 1]) {
                    continue;
                }
                int left = i + 1;
                int right = nums.size() - 1;
                while (right > left) {
                    if ((long) nums[k] + nums[i] + nums[left] + nums[right] > target) {
                        right--;
                    } else if ((long) nums[k] + nums[i] + nums[left] + nums[right]  < target) {
                        left++;
                    } else {
                        result.push_back(vector<int>{nums[k], nums[i], nums[left], nums[right]});
                        // 对nums[left]和nums[right]去重
                        while (right > left && nums[right] == nums[right - 1]) right--;
                        while (right > left && nums[left] == nums[left + 1]) left++;
                        // 找到答案时，双指针同时收缩
                        right--;
                        left++;
                    }
                }
            }
        }
        return result;
    }
};
```

# 栈和队列

## 用栈实现队列

### 232.用栈实现队列  Easy

```cpp
class MyQueue {  // 双栈实现
public:
    stack<int> stack1; // OUT
    stack<int> stack2; // IN
    
    MyQueue() {
    }
    
    void push(int x) {
        stack2.push(x);
    }
    
    int pop() {  // pop要复杂一些，若OUT栈不空，直接输出栈顶，否则将IN栈元素push进OUT栈（反序成为队列）
        int ans;
        if(!stack1.empty()) {
            ans = stack1.top();
            stack1.pop();
        } else {
            while(!stack2.empty()) {
                int tmp = stack2.top();
                stack1.push(tmp);
                stack2.pop();
            }
            ans = stack1.top();
            stack1.pop();
        }
        return ans;
    }
    
    int peek() {
        int ans;
        if(!stack1.empty())     ans = stack1.top();
        else{
            while(!stack2.empty()) {
                int tmp = stack2.top();
                stack1.push(tmp);
                stack2.pop();
            }
            ans = stack1.top();
        }
        return ans;
    }
    
    bool empty() {
        if(stack1.empty() && stack2.empty())    return true;
        else                                    return false;
    }
};
```

## 用队列实现栈

### 225.用队列实现栈  Easy

```cpp
class MyStack {
public:
    queue<int> q1, q2;

    MyStack() {
    }
    
    void push(int x) {
        q2.push(x);
    }
    
    // 模拟方式；两个队列来回倒，每次pop把原本队列的队尾去掉（栈顶）
    int pop() {
        int ans;
        if(q1.empty()) {
            ans = q2.back();
            while(q2.size()>1) {
                int tmp = q2.front();
                q1.push(tmp);
                q2.pop();
            }
            q2.pop();
        } else {
            ans = q1.back();
            while(q1.size()>1) {
                int tmp = q1.front();
                q2.push(tmp);
                q1.pop();
            }
            q1.pop();
        }
        return ans;
    }
    
    int top() {
        int ans;
        if(q1.empty())  ans = q2.back();
        else            ans = q1.back();
        return ans;
    }
    
    bool empty() {
        if(q1.empty() && q2.empty())    return true;
        else                            return false;
    }
};
```

### 优化

实际上一个队列模拟栈即可。

每次“出栈”，实际上就是把队列除队尾外其他所有元素，逐个出队再入队，最后再pop队头的过程。

## 有效括号

### 20.有效括号  Easy

识别括号匹配的字符串。

```cpp
class Solution {
public:
    bool isValid(string s) {
        int cnt = 0;
        stack<char> str;
        while(cnt < s.length()) {
            if(s[cnt]=='(' || s[cnt]=='[' || s[cnt]=='{') {
                str.push(s[cnt]);
            } else if(s[cnt]==')' && !str.empty() && str.top()=='(') {
                str.pop();
            } else if(s[cnt]==']' && !str.empty() && str.top()=='[') {
                str.pop();
            } else if(s[cnt]=='}' && !str.empty() && str.top()=='{') {
                str.pop();
            } else {
                str.push(s[cnt]);
            }
            cnt ++;
        }
        if(str.empty())    return true;
        else               return false;
    }
};
```

### “匹配问题”都是栈的强项

## 删除字符串中的所有相邻重复项

### 1047.删除字符串中的所有相邻重复项  Easy

给出由小写字母组成的字符串 `S`，**重复项删除操作**会选择两个相邻且相同的字母，并删除它们。

在 S 上**反复执行**重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

```cpp
class Solution {
public:
    string removeDuplicates(string s) {
        stack<char> re;
        string ans;
        int cnt = s.length()-1;
        while(cnt >= 0) {
            if(!re.empty() && re.top()==s[cnt]) {
                re.pop();
            } else {
                re.push(s[cnt]);
            }
            cnt --;
        }
        while(!re.empty()) {
            ans += re.top();
            re.pop();
        }
        return ans;
    }
};
```

## 逆波兰表达式

### 150.逆波兰表达式  Medium

给你一个字符串数组 `tokens` ，表示一个根据 [逆波兰表示法](https://baike.baidu.com/item/逆波兰式/128437) 表示的算术表达式。

请你计算该表达式。返回一个表示表达式值的整数。

**注意：**

- 有效的算符为 `'+'`、`'-'`、`'*'` 和 `'/'` 。
- 每个操作数（运算对象）都可以是一个整数或者另一个表达式。
- 两个整数之间的除法总是 **向零截断** 。
- 表达式中不含除零运算。
- 输入是一个根据逆波兰表示法表示的算术表达式。
- 答案及所有中间计算结果可以用 **32 位** 整数表示。

```cpp
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<long long> st; 
        for (int i = 0; i < tokens.size(); i++) {
            if (tokens[i] == "+" || tokens[i] == "-" || tokens[i] == "*" || tokens[i] == "/") {
                long long num1 = st.top();	st.pop();
                long long num2 = st.top();	st.pop();
                if (tokens[i] == "+") st.push(num2 + num1);
                if (tokens[i] == "-") st.push(num2 - num1);
                if (tokens[i] == "*") st.push(num2 * num1);
                if (tokens[i] == "/") st.push(num2 / num1);
            } else {
                st.push(stoll(tokens[i]));   // string->longlong
            }
        }
        int res = st.top();
        return res;
    }
};
```

### *题外话：cpp的字符串转换函数

```cpp
// 针对C风格的字符数组array
strlwr() 小写转换、strupr大写转换
atoi()：字符串转换数字，遇到非数字或结束符停止，成功返回转换数字，不成功返回0
itoa(value,string,num) num为采用的进制
atof()：字符串转化为double类型数据
atol()：字符串转化为long类型数据
atoll()：字符串转化为long long类型数据
strtod():字符串转数字，带两个参数（const char*str,char**ptr）,若ptr指针不为空，则会将转换数字后面其余字符地址保存在该ptr上
// c++ string类函数
tolower() 和 toupper() 返回类型string
stoi()  
stol()
stoll()
stof()
stod()
to_string() // 转字符串
```

## *滑动窗口最大值

### 239.滑动窗口最大值  Hard

给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

返回 *滑动窗口中的最大值* 。

```cpp
class Solution {
private:
    class MyQueue { // 单调队列数据结构
    public:
        deque<int> que; // 使用deque
        // 控制窗口大小，比较当前要弹出的数值(“实际窗口”的左端)是否等于队列出口的数值，如果相等则弹出。
        void pop(int value) {
            if (!que.empty() && value == que.front()) {
                que.pop_front();
            }
        }
        // 如果push的数值大于入口元素的数值，那么就将队列后端的数值弹出，直到push的数值小于等于队列入口元素的数值为止。
        // 保持队列里的数值单调递减。
        void push(int value) {
            while (!que.empty() && value > que.back()) {
                que.pop_back();
            }
            que.push_back(value);
        }
        // 查询当前队列里的最大值
        int front() {
            return que.front();
        }
    };
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        MyQueue que;
        vector<int> result;
        for (int i = 0; i < k; i++) { // 先将前k的元素放进队列
            que.push(nums[i]);
        }
        result.push_back(que.front());
        for (int i = k; i < nums.size(); i++) {
            que.pop(nums[i - k]); // 移除“实际窗口”最前面元素
            que.push(nums[i]);    // 滑动窗口，加入新元素
            result.push_back(que.front());
        }
        return result;
    }
};
```

## 前 K 个高频元素（大根堆）

### 347.前 K 个高频元素  Medium

给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。

```cpp
class Solution {
public:
    class mycomparison {
    public:
        typedef pair<int,int> PII;
        bool operator()(const PII lhs, const PII rhs) {
                return lhs.second < rhs.second;
            }
        };
        vector<int> topKFrequent(vector<int>& nums, int k) {
        // 统计元素出现频率
        unordered_map<int, int> hash;
        for (int i = 0; i < nums.size(); i++) {
            hash[nums[i]]++;
        }
        // 定义一个大顶堆
        priority_queue<pair<int,int>, vector<pair<int,int>>, mycomparison> heap;
        for (auto it: hash) {
            heap.push(it);
        }

        vector<int> res;
        for (int i = 0; i < k; i++) {
            res.push_back(heap.top().first);
            heap.pop();
        }
        return res;
    }
};
```

## 栈和队列总结

### 灵魂四问

1. C++中stack 是容器么？
2. 我们使用的stack是属于哪个版本的STL？
3. 我们使用的STL中stack是如何实现的？
4. stack 提供迭代器来遍历stack空间么？

首先大家要知道 栈和队列是STL（C++标准库）里面的两个数据结构。

来说一说栈，栈先进后出，如图所示：

![栈与队列理论2](https://code-thinking-1253855093.file.myqcloud.com/pics/20210104235434905.png)

栈提供push 和 pop 等等接口，所有元素必须符合先进后出规则，所以栈不提供走访功能，也不提供迭代器(iterator)。 不像是set 或者map 提供迭代器iterator来遍历所有元素。

**栈是以底层容器完成其所有的工作，对外提供统一的接口，底层容器是可插拔的（也就是说我们可以控制使用哪种容器来实现栈的功能）。**

所以**STL中栈往往不被归类为容器，而被归类为container adapter（容器适配器******）****。

那么问题来了，STL 中栈是用什么容器实现的？

从下图中可以看出，栈的内部结构，栈的底层实现可以是vector，deque，list 都是可以的， 主要就是数组和链表的底层实现。

![栈与队列理论3](https://code-thinking-1253855093.file.myqcloud.com/pics/20210104235459376.png)

**我们常用的SGI STL，如果没有指定底层实现的话，默认是以deque为缺省情况下栈的底层结构。**

deque是一个双向队列，只要封住一段，只开通另一端就可以实现栈的逻辑了。

**SGI STL中 队列底层实现缺省情况下一样使用deque实现的。**

我们也可以指定vector为栈的底层实现，初始化语句如下：

```cpp
std::stack<int, std::vector<int> > third;  // 使用vector为底层容器的栈
```

刚刚讲过栈的特性，对应的队列的情况是一样的。

队列中先进先出的数据结构，同样不允许有遍历行为，不提供迭代器, **SGI STL中队列一样是以deque为缺省情况下的底部结构。**

也可以指定list 为起底层实现，初始化queue的语句如下：

```cpp
std::queue<int, std::list<int>> third; // 定义以list为底层容器的队列
```

所以STL 队列也不被归类为容器，而被归类为container adapter（ 容器适配器）。

### 一道面试题：栈里面的元素在内存中是连续分布的么？

这个问题有两个陷阱：

- 陷阱1：栈是容器适配器，底层容器使用不同的容器，导致栈内数据在内存中是不是连续分布。
- 陷阱2：缺省情况下，默认底层容器是deque，那么deque的在内存中的数据分布是什么样的呢？ 答案是：不连续的，下文也会提到deque。   

# 二叉树

## 二叉树的递归遍历

### 144. 二叉树的前序遍历  Easy

```cpp
class Solution {
public:
    void Traversal(TreeNode* node,vector<int>& res) {
        if(node==NULL)    return;
        res.push_back(node->val);
        Traversal(node->left,res);
        Traversal(node->right,res);
    }

    vector<int> preorderTraversal(TreeNode* root) { 
        vector<int>res;   
        Traversal(root,res);
        return res;
    }
};
```

## 二叉树的迭代遍历

### 144.二叉树的前序遍历  Easy

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if (root == NULL) return result;
        st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();                       // 中
            st.pop();
            result.push_back(node->val);
            if (node->right) st.push(node->right);           // 先push右（空节点不入栈）
            if (node->left)  st.push(node->left);            // 再push左（空节点不入栈）
        }
        return result;
    }
};
```

### 94.二叉树的中序遍历  Easy

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        TreeNode* cur = root;
        while (cur != NULL || !st.empty()) {
            if (cur != NULL) { // 指针来访问节点，访问到最底层
                st.push(cur); // 将访问的节点放进栈
                cur = cur->left;                // 左
            } else {
                cur = st.top(); // 从栈里弹出的数据，就是要处理的数据（放进result数组里的数据）
                st.pop();
                result.push_back(cur->val);     // 中
                cur = cur->right;               // 右
            }
        }
        return result;
    }
};
```

### 145.二叉树的后序遍历  Easy

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if (root == NULL) return result;
        st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();
            st.pop();
            result.push_back(node->val);
            if (node->left) st.push(node->left); // 相对于前序遍历，这更改一下入栈顺序 （空节点不入栈）
            if (node->right) st.push(node->right); // 空节点不入栈
        }
        reverse(result.begin(), result.end()); // 将结果反转之后就是左右中的顺序了
        return result;
    }
};
```

## 二叉树的统一迭代法

### 标记法中序遍历

![中序遍历迭代（统一写法）](https://code-thinking.cdn.bcebos.com/gifs/%E4%B8%AD%E5%BA%8F%E9%81%8D%E5%8E%86%E8%BF%AD%E4%BB%A3%EF%BC%88%E7%BB%9F%E4%B8%80%E5%86%99%E6%B3%95%EF%BC%89.gif)

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        if (root != NULL) st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();
            if (node != NULL) {
                st.pop(); // 将该节点弹出，避免重复操作，下面再将右中左节点添加到栈中
                if (node->right) st.push(node->right);  // 添加右节点（空节点不入栈）

                st.push(node);                          // 添加中节点
                st.push(NULL); // 中节点访问过，但是还没有处理，加入空节点做为标记。

                if (node->left) st.push(node->left);    // 添加左节点（空节点不入栈）
            } else { // 只有遇到空节点的时候，才将下一个节点放进结果集
                st.pop();           // 将空节点弹出
                node = st.top();    // 重新取出栈中元素
                st.pop();
                result.push_back(node->val); // 加入到结果集
            }
        }
        return result;
    }
};
```

## 二叉树的层序遍历

### 102. 二叉树的层序遍历  Medium

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        queue<TreeNode*> bfs;
        if(root)    bfs.push(root);
        while(!bfs.empty()) {
            vector<int> tier;
            int size = bfs.size();    // 一次处理一层，先记录当前层的有几个
            for(int i=0; i<size; i++) {
                TreeNode* node = bfs.front();
                bfs.pop();
                tier.push_back(node->val);
                if(node->left)  bfs.push(node->left);
                if(node->right) bfs.push(node->right);
            }
            ans.push_back(tier);
        }
        return ans;
    }
};
```

### 107. 二叉树的层序遍历II  Medium

给你二叉树的根节点 `root` ，返回其节点值 **自底向上的层序遍历** 。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

**核心：每次处理一层（size变量）**

```cpp
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> ans;
        stack<vector<int>>  tmp;
        queue<TreeNode*> bfs;
        if(root)    bfs.push(root);
        while(!bfs.empty()) {
            vector<int> tier;
            int size = bfs.size();   
            for(int i=0; i<size; i++) {
                TreeNode* node = bfs.front();
                bfs.pop();
                if(node->left)  bfs.push(node->left);
                if(node->right) bfs.push(node->right);
                tier.push_back(node->val);
            }
            tmp.push(tier);
        }
        while(!tmp.empty()) {         // 加一步入栈把层序反序即可
            ans.push_back(tmp.top());
            tmp.pop();
        }
        return ans;
    }
};
```

#### *Tip (reverse函数)

**可以使用reverse(ans.begin(), ans.end())**



### 199. 二叉树的右视图  Medium

给定一个二叉树的 **根节点** `root`，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

**示例 1:**

![img](https://assets.leetcode.com/uploads/2021/02/14/tree.jpg)

```
输入: [1,2,3,null,5,null,4]
输出: [1,3,4]
```

```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> res;
        queue<TreeNode*> bfs;
        if(root)    bfs.push(root);
        while(!bfs.empty()) {
            int size = bfs.size();    // 一次处理一层，先记录当前层的有几个
            for(int i=0; i<size; i++) {
                TreeNode* node = bfs.front();
                bfs.pop();
                if(node->left)  bfs.push(node->left);
                if(node->right) bfs.push(node->right);
                if(i == size-1) res.push_back(node->val);
            }
        }
        return res;
    }
};
```



### 637. 二叉树的层平均值  Easy

给定一个非空二叉树的根节点 `root` , 以数组的形式返回每一层节点的平均值。与实际答案相差 `10-5` 以内的答案可以被接受。

```cpp
class Solution {
public:
    vector<double> averageOfLevels(TreeNode* root) {
        vector<double> ans;
        queue<TreeNode*> bfs;
        if(root)    bfs.push(root);
        while(!bfs.empty()) {
            double sum = 0;
            int size = bfs.size();    // 一次处理一层，先记录当前层的有几个
            for(int i=0; i<size; i++) {
                TreeNode* node = bfs.front();
                bfs.pop();
                sum += node->val;
                if(node->left)  bfs.push(node->left);
                if(node->right) bfs.push(node->right);
            }
            ans.push_back(sum/size);
        }
        return ans;
    }
};
```



### 429.N 叉树的层序遍历  Medium

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        vector<vector<int>> ans;
        queue<Node*> qt;
        if(root)   qt.push(root);

        while(!qt.empty()) {
            int size = qt.size();
            vector<int> tier;
            for(int i=0; i<size; i++) {
                Node* node = qt.front();
                qt.pop();
                tier.push_back(node->val);
                for(auto it: node->children) {  // 改一下子树遍历即可
                    qt.push(it);
                }
            }
            ans.push_back(tier);
        }
        return ans;
    }
};
```



### 515.在每个树行中找最大值  Medium

```cpp
class Solution {
public:
    vector<int> largestValues(TreeNode* root) {
        vector<int> ans;
        queue<TreeNode*> bfs;
        if(root)    bfs.push(root);
        while(!bfs.empty()) {
            vector<int> tier;
            int size = bfs.size();   
            for(int i=0; i<size; i++) {
                TreeNode* node = bfs.front();
                bfs.pop();
                tier.push_back(node->val);
                if(node->left)  bfs.push(node->left);
                if(node->right) bfs.push(node->right);
            }
            sort(tier.begin(), tier.end());   // 亦可每次用 maxValue = max(maxValue, node->val)
            ans.push_back(tier[tier.size()-1]);
        }
        return ans;
    }
};
```



### 116. 填充每个节点的下一个右侧节点指针  Medium

给定一个 **完美二叉树** ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL`。

初始状态下，所有 next 指针都被设置为 `NULL`。

```cpp
class Solution {
public:
    Node* connect(Node* root) {
        queue<Node*> bfs;
        if(root)    bfs.push(root);
        while(!bfs.empty()) {
            int size = bfs.size();    
            for(int i=0; i<size; i++) {
                Node* node = bfs.front();
                bfs.pop();
                if(i != size-1)     node->next = bfs.front();
                else                node->next = NULL;
                if(node->left)  bfs.push(node->left);
                if(node->right) bfs.push(node->right);
            }
        }
        return root;
    }
};
```



### 117. 填充每个节点的下一个右侧节点指针II  Medium

**What's the difference at all?**



### 104. 二叉树的最大深度  Easy

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root)   return 0;
        else        return max (maxDepth(root->left)+1, maxDepth(root->right)+1);
    }
};
```



### 111. 二叉树的最小深度  Easy

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

```cpp
class Solution {
public:
    int minDepth(TreeNode* root) {
        queue<TreeNode*> bfs;
        if(root)    bfs.push(root);
        int tier = 0;
        while(!bfs.empty()) {   
            int size = bfs.size();
            tier ++;                  // 按层序找到的第一个叶结点显然就是最近结点
            for(int i=0; i<size; i++) {
                TreeNode* node = bfs.front();
                bfs.pop();
                if(node->left)  bfs.push(node->left);
                if(node->right) bfs.push(node->right);
                if(!node->left && !node->right) return tier;
            }
        }
        return 0;
    }
};
```



## 翻转二叉树

### 226. 翻转二叉树  Easy

给你一棵二叉树的根节点 `root` ，翻转这棵二叉树，并返回其根节点。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/03/14/invert1-tree.jpg)

```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/03/14/invert2-tree.jpg)

```
输入：root = [2,1,3]
输出：[2,3,1]
```

**示例 3：**

```
输入：root = []
输出：[]
```



#### 递归法

```cpp
class Solution {
public:
    void recur(TreeNode* root) {
        if(!root)   return;
        else        swap(root->left,root->right);
        invertTree(root->left);
        invertTree(root->right);
    }
    TreeNode* invertTree(TreeNode* root) {
        recur(root);
        return root;
    }
};
```



#### 迭代法

```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        queue<TreeNode*> que;
        if (root != NULL) que.push(root);
        while (!que.empty()) {
            int size = que.size();
            for (int i = 0; i < size; i++) {    
                TreeNode* node = que.front();
                que.pop();       
                swap(node->left, node->right);      // 层序遍历，从上往下交换左右子结点
                if (node->left)  que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }
        return root;
    }
};
```



## 对称二叉树

### *101. 对称二叉树  Easy

给你一个二叉树的根节点 `root` ， 检查它是否轴对称。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/02/19/symtree1.jpg)

```
输入：root = [1,2,2,3,4,4,3]
输出：true
```

```cpp
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (root == NULL) return true;
        queue<TreeNode*> que;
        que.push(root->left); 
        que.push(root->right);  
        
        while (!que.empty()) {  // 接下来成对判断是否对称
            TreeNode* leftNode = que.front();    que.pop();
            TreeNode* rightNode = que.front();   que.pop();
            if (!leftNode && !rightNode) {  // 左、右都空，此时对称
                continue;
            }
            // 左右有一个节点不为空，或都不为空但数值不相同，返回false
            if (!leftNode || !rightNode || (leftNode->val != rightNode->val)) {
                return false;
            }
            que.push(leftNode->left);   // 加入左节点左孩子
            que.push(rightNode->right); // 加入右节点右孩子
            que.push(leftNode->right);  // 加入左节点右孩子
            que.push(rightNode->left);  // 加入右节点左孩子
        }
        return true;
    }
};
```



## 完全二叉树的结点个数

### *222. 完全二叉树的节点个数  Medium

给你一棵 **完全二叉树** 的根节点 `root` ，求出该树的节点个数。



遍历O(n)是显然的

```cpp
class Solution {
public:
    int getNodesNum(TreeNode* cur) {
        if (cur == NULL) return 0;
        else return getNodesNum(cur->left) + getNodesNum(cur->right) + 1;   
    }

    int countNodes(TreeNode* root) {
        return getNodesNum(root);
    }
};
```

设计更快的算法解决此问题——

完全二叉树只有两种情况，情况一：就是**满二叉树**，情况二：最后一层叶子节点没有满。

对于情况一，可以直接用 2^深度 - 1 来计算，注意这里根节点深度为1。

对于情况二，分别递归左孩子，和右孩子，递归到某一深度一定会有左孩子或者右孩子为满二叉树，然后依然可以按照情况1来计算。

```cpp
class Solution {
public:
    int countNodes(TreeNode* root) {
        if (root == nullptr) return 0;
        TreeNode* left = root->left;
        TreeNode* right = root->right;
        int leftDepth = 0, rightDepth = 0; 
        while (left) {                    // 求左枝长度
            left = left->left;
            leftDepth++;
        }
        while (right) {                   // 求右枝长度
            right = right->right;
            rightDepth++;
        }
        if (leftDepth == rightDepth) {    // 如果是满二叉树
            return (2 << leftDepth) - 1; 
        }
        else return countNodes(root->left) + countNodes(root->right) + 1;
        // 事实上还能做一步简化，完全二叉树不是满二叉树，那一定是leftDepth > rightDepth，即右子树是满的
    }
};
```

- 时间复杂度：O(log n × log n)



## 平衡二叉树

### 110. 平衡二叉树  Easy

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

> 一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1 。

```cpp
class Solution {
public:
    int heightOf(TreeNode* root) {
       if(!root)    return 0;
       else         return 1 + max (heightOf(root->left),heightOf(root->right)); 
    }

    void heightJudge(TreeNode* root, bool& flag) {  //必须所有子树也都是平衡二叉树，故用一个引用flag记录结果
        if (!root) {
            return;
        } else if (abs(heightOf(root->left)-heightOf(root->right)) > 1) {
            flag = false;
            return;
        }  
        heightJudge (root->left,flag);
        heightJudge (root->right,flag);
    }

    bool isBalanced(TreeNode* root) {
        bool flag = true;
        heightJudge (root,flag);
        if(flag)    return true;
        else        return false;
    }
};
```



## *二叉树的所有路径

### 257. 二叉树的所有路径  Easy

给你一个二叉树的根节点 `root` ，按 **任意顺序** ，返回所有从根节点到叶子节点的路径。

```cpp
class Solution {
public:
    vector<string> binaryTreePaths(TreeNode* root) {
        stack<TreeNode*> tree; // 按照层序遍历树
        stack<string> path;    // 保存每一个遍历路径
        vector<string> res;    // 保存最终路径集合
        if (root == NULL) return res;
        tree.push(root); 
        path.push(to_string(root->val));
        while (!tree.empty()) {
            TreeNode* node = tree.top();    tree.pop();      // 取出节点 
            string PATH = path.top();       path.pop();      // 取出该节点目前对应的路径
            if (node->left == NULL && node->right == NULL) { // 遇到叶子节点
                res.push_back(PATH);
            }
            if (node->left) { // 左
                tree.push(node->left);
                path.push(PATH + "->" + to_string(node->left->val));
            }
            if (node->right) { // 右
                tree.push(node->right);
                path.push(PATH + "->" + to_string(node->right->val));
            }
        }
        return res;
    }
};
```



## 左叶子之和

### 404. 左叶子之和  Easy

给定二叉树的根节点 `root` ，返回所有左叶子之和。

#### 迭代法

```cpp
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        int ans = 0;
        queue<TreeNode*> st;
        if(root)    st.push(root);
        while(!st.empty()) {
            TreeNode* node = st.front();
            st.pop();
            if(node->left) {
                st.push(node->left);
                if(!node->left->left && !node->left->right)   // 对树做遍历，判定左叶子结点即可
                    ans += node->left->val;
            }
            if(node->right) {
                st.push(node->right);
            }
        }
        return ans;
    }
};
```

#### 递归法

```cpp
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        if (root == NULL) return 0;
        if (root->left == NULL && root->right== NULL) return 0;
        if (root->left && !root->left->left && !root->left->right) { // 左子树就是一个左叶子的情况
            return root->left->val + sumOfLeftLeaves(root->right);
        }
        return sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right);
    }
};
```



## 找树左下角的值

### 513. 找树左下角的值  Medium

给定一个二叉树的 **根节点** `root`，请找出该二叉树的 **最底层 最左边** 节点的值。

```cpp
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        int ans;
        queue<TreeNode*> bfs;
        if(root)    bfs.push(root);
        while(!bfs.empty()) {
            vector<int> tier;
            int size = bfs.size();   
            for(int i=0; i<size; i++) {
                TreeNode* node = bfs.front();
                bfs.pop();
                tier.push_back(node->val);
                if(node->left)  bfs.push(node->left);
                if(node->right) bfs.push(node->right);
            }
            ans = tier[0];   // 层序遍历记录一层的首元素即可
        }
        return ans;
    }
};
```



## 路径总和

### 112. 路径总和  Easy

给你二叉树的根节点 `root` 和一个表示目标和的整数 `targetSum` 。判断该树中是否存在 **根节点到叶子节点** 的路径，这条路径上所有节点值相加等于目标和 `targetSum` 。如果存在，返回 `true` ；否则，返回 `false` 。

```cpp
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {   // 递归解决
        if(!root) {
            return false;
        } else if (!root->left && !root->right) {
            return root->val == targetSum;
        } else {
            return hasPathSum(root->left, targetSum-root->val) || hasPathSum(root->right, targetSum-root->val);
        }
    }
};
```

### 113.路径总和ii  Medium

给你二叉树的根节点 `root` 和一个整数目标和 `targetSum` ，找出所有 **从根节点到叶子节点** 路径总和等于给定目标和的路径。

```cpp
class Solution {
public:
    bool sumJudge(vector<int> num,int sum) {
        int res = 0;
        for(auto it:num) {
            res += it;
        }
        if(res == sum)  return true;
        else            return false;
    }

    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        stack<TreeNode*> tree;      // 按照层序遍历树
        stack<vector<int>> path;    // 保存每一个遍历路径
        vector<vector<int>> res;    // 保存最终路径集合
        if (root == NULL) return res;
        tree.push(root); 
        path.push({root->val});
        while (!tree.empty()) {
            TreeNode* node = tree.top();    tree.pop();      // 取出节点 
            vector<int> PATH = path.top();  path.pop();      // 取出该节点目前对应的路径
            if (node->left == NULL && node->right == NULL) { // 遇到叶子节点
                if(sumJudge(PATH,targetSum))
                    res.push_back(PATH);
            }
            vector<int> PATH2 = PATH;
            if (node->left) { // 左
                tree.push(node->left);
                PATH.push_back(node->left->val);
                path.push(PATH);
            }
            if (node->right) { // 右
                tree.push(node->right);
                PATH2.push_back(node->right->val);
                path.push(PATH2);
            }
        }
        return res;
    }
};
```

