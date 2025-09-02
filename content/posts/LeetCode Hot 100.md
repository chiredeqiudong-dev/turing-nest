---
title: Leetcode Hot 100
slug: leetcode-hot-100
date: 2025-09-02
categories: 算法
tags:
  - leetcode
  - hot-100
---
## 哈希
### [两数之和](https://leetcode.cn/problems/two-sum/)

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer,Integer> map = new HashMap<>();
        for(int i = 0; i < nums.length; i ++){
            int a = nums[i];
            if(map.containsKey(target - a)){
                return new int[] {i,map.get(target - a)};
            }
            map.put(a,i);
        }
        return null;
    }
}
```

-  $a + b = c$ 题目类型，因为 $b = c - a$，所以遍历 a，查询 b 即可。
- 使用哈希表以 $O(1)$ 复杂度查询。
### [字母异位词分组](https://leetcode.cn/problems/group-anagrams/)

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        HashMap<String, List<String>> hashMap = new HashMap<>();
        for (String str : strs) {
            // 转为字符数组排序
            char[] array = str.toCharArray();
            Arrays.sort(array);
            // 转为String为key，做判断
            String key = Arrays.toString(array);
            // 判断是否存在该单词
            List<String> ans = hashMap.getOrDefault(key, new ArrayList<>());
            ans.add(str);
            hashMap.put(key,ans);
        }
        // 结果集
        return new ArrayList<List<String>>(hashMap.values());
    }
}
```

- 将字符串排序后作为 key，从而得到字母异位词分组
- `Collection<T> values = hashMap.values()`，`list.addAll(Collection<? extends E> c)`
### [最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        // 去重
        for(int num : nums){
            set.add(num);
        }
        int maxL = 0;
        //x-1 的序列长度 >> x 的序列长度
        for(int x : set){
            if(set.contains(x - 1)){
                continue;
            }else{
                int curL = 1;
                while(set.contains(x + 1)){
                    curL ++;
                    x ++;
                }
                maxL = Math.max(maxL,curL);
            }
        }
        return maxL;
    }
}
```

- [题解](https://leetcode.cn/problems/longest-consecutive-sequence/solutions/3005726/ha-xi-biao-on-zuo-fa-pythonjavacgojsrust-whop/?envType=study-plan-v2&envId=top-100-liked)
- 哈希去重，如果哈希表存在 x - 1，那么以 x - 1 开始的数字序列长度一定大于 x。
## 双指针
### [移动零](https://leetcode.cn/problems/move-zeroes/)

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int head = 0;
        int tail = 0;
        // 将不为0的num[tail]依次插入num[head]
        while (tail < nums.length) {
            if (nums[tail] != 0) {
                int temp = nums[head];
                nums[head] = nums[tail];
                nums[tail] = temp;
                head++;
            }
            tail ++;
        }
    }
}
```

- 参考栈的设计，不为 0 的元素依次入栈
### [盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int area = -1;
        while(left < right) {
            area = Math.max(area,(right - left) * Math.min(height[left],height[right]));
            // 移动短板
            if(height[left] >= height[right]){
                right --;
            }else{
                left ++;
            }
        }
        return area;
    }
}
```

- [题解](https://leetcode.cn/problems/container-with-most-water/solutions/11491/container-with-most-water-shuang-zhi-zhen-fa-yi-do/?envType=study-plan-v2&envId=top-100-liked)
- 若向内**移动短板**，水槽的短板 `min(h[left],h[right])` 可能变大，因此下个水槽的面积**可能增大**。
- 若向内**移动长板**，水槽的短板 `min(h[left],h[right])` 不变或变小，因此下个水槽的面积**一定变小**。
### [三数之和](https://leetcode.cn/problems/3sum/)

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        // a + b + c = sum
        for(int i = 0; i < nums.length - 2; i++) {
            int a = nums[i];
            // 去重
            if(i > 0 && nums[i] == nums[i - 1]) continue;
            // 双指针枚举[i + 1, nums.length]中元素
            int l = i + 1;
            int r = nums.length - 1;
            while(l < r){
                int sum = a + nums[l] + nums[r];
                if(sum > 0){
                    while(l < r && nums[r] == nums[--r]);
                }else if(sum < 0) {
                    while(l < r && nums[l] == nums[++l]);
                }else{
                    List<Integer> ans = new ArrayList<>();
                    ans.add(a);
                    ans.add(nums[l]);
                    ans.add(nums[r]);
                    res.add(ans);
                    // 去重
                    while(l < r && nums[r] == nums[--r]);
                    while(l < r && nums[l] == nums[++l]);
                }
            }
        }
        return res;
    }
}
```

- [题解](https://leetcode.cn/problems/3sum/solutions/11525/3sumpai-xu-shuang-zhi-zhen-yi-dong-by-jyd/?envType=study-plan-v2&envId=top-100-liked)
- 双指针 l , r 交替向中间移动，记录 `nums[i] + nums[l] + nums[r] == 0` 的 l , r 组合。
- 通过排序，$a+b+c=sum$ 中，sum 的值可以通过移动 l , r 指针控制。
- 排序后，`nums[i-1]`得到的`(i-1,l,r)`结果一定包含了`nums[i]`的结果，从而解决重复。
### [接雨水](https://leetcode.cn/problems/trapping-rain-water/)

```java
class Solution {
    public int trap(int[] height) {
        // maxL[i]为左边柱子的最大高度，包括本身
        int[] maxL = new int[height.length]; 
        // maxL[i]为右边柱子的最大高度，包括本身
        int[] maxR = new int[height.length]; 
        maxL[0] = height[0];
        maxR[height.length - 1] = height[height.length - 1];
        for (int i = 1; i < height.length; i++) {
            maxL[i] = Math.max(maxL[i - 1], height[i]);
        }
        for (int i = height.length - 2; i >= 0; i --) {
            maxR[i] = Math.max(maxR[i + 1], height[i]);
        }
        // 接的雨水为当前柱子i Min{maxL[i],maxR[i]} - height[i]
        int cnt = 0;
        for(int i = 1; i < height.length - 1; i++) {
            int res = Math.min(maxL[i - 1],maxR[i + 1]) - height[i];
            cnt += Math.max(res, 0);
        }
        return cnt;
    }
}
```

- [题解](https://leetcode.cn/problems/trapping-rain-water/solutions/692342/jie-yu-shui-by-leetcode-solution-tuvc/)
- 木桶理论，dp 的代码更好理解。
- 对于每一列可以接的雨水取决于左右两边最高的短木板减去当前木板高度（如下图所示）。

![](https://cdn.img.turingzy.cn/2025/Hot-100-0811.webp)
## 滑动窗口
### [无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() <= 0) return 0;
        char[] arr = s.toCharArray();
        Set<Character> set = new HashSet<>();
        int head = 0;
        int tail = 1;
        set.add(arr[0]);
        int res = 1;
        while(tail < arr.length){
            // 伸缩
            while(head < tail && set.contains(arr[tail])) {
                set.remove(arr[head]);
                head ++;
            }
            // 扩张
            set.add(arr[tail]);
            res = Math.max(res,tail - head + 1);
            tail ++;
        }
        return res;
    }
}
```

- 不定长滑动窗口
- 通过哈希表去重
### [找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        if(s.length() < p.length()) return new ArrayList<>();
        int[] base = new int[26];
        for(char c: p.toCharArray()) {
            base[c - 'a'] ++;
        }
        int[] cnt = new int[26];
        for(int i = 0; i < p.length(); i++){
            cnt[s.charAt(i) - 'a'] ++;
        }
        int l = 0;
        int r = p.length() - 1;
        List<Integer> res = new ArrayList<>();
        while(r < s.length()){
            if(Arrays.equals(cnt,base)){
                res.add(l);
            }
            // 防止越界
            if(r == s.length() - 1){
                break;
            }
            cnt[s.charAt(l++) - 'a'] --;
            cnt[s.charAt(++r) - 'a'] ++;
        }
        return res;
    }
}
```

- 定长滑动窗口
- 通过`Arrays.equals()`来比较两个数组的值是否相等（只包含小写字母，比较耗时可忽略）。
- 因为是先定好了窗口（第二个 for 循环）才开始比较，所以需要注意是否索引越界。
## 子串
### [和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

```java
// 解法一: 直接枚举所有子数组
class Solution {
    public int subarraySum(int[] nums, int k) {
        int res = 0;
        for (int i = 0; i < nums.length; i++) {
            int ans = 0;
            for (int j = i; j < nums.length; j++) {
                ans += nums[j];
                if (ans == k) {
                    res ++;
                }
            }
        }
        return res;
    }
}
// 解法二：动态规划
class Solution {
    public int subarraySum(int[] nums, int k) {
        HashMap<Integer, Integer> mp = new HashMap<>();
        // suf: 表示
        int res = 0;
        int suf = 0;
        // 当suf[i] - k = 0 即suf[i] = k时的初始值
        mp.put(0, 1); 
        for (int i = nums.length - 1; i >= 0; i--) {
            suf+=nums[i];
            if (mp.containsKey(suf - k)) {
                res += mp.get(suf - k);
            }
            mp.put(suf, mp.getOrDefault(suf, 0) + 1);
        }
        return res;
    }
}
```

- [题解](https://leetcode.cn/problems/subarray-sum-equals-k/solutions/238572/he-wei-kde-zi-shu-zu-by-leetcode-solution/?envType=study-plan-v2&envId=top-100-liked)
- 两种解法，枚举 | 前缀和
- 前缀和需要理解等式 $Sum(nums[i],nums[j-1])=suf[i]-suf[j]$，`suf[i]`表示 $Sum(nums[i],nums[n-1])$。
### [滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        // 优先队列（大根堆）
        PriorityQueue<int[]> pq = new PriorityQueue<int[]>(new Comparator<int[]>() {
            public int compare(int[] pair1, int[] pair2) {
                // 如果2个数相等，那么index大的在前面
                return pair1[0] != pair2[0] ? pair2[0] - pair1[0] : pair2[1] - pair1[1];
            }
        });
        for (int i = 0; i < k; ++i) {
            pq.offer(new int[]{nums[i], i});
        }
        int[] ans = new int[nums.length - k + 1];
        ans[0] = pq.peek()[0];
        for (int i = k; i < nums.length; ++i) {
            pq.offer(new int[]{nums[i], i});
            // 只有当最大值不在在窗口内则删除，其余的不用管
            while (pq.peek()[1] <= i - k) {
                pq.poll();
            }
            ans[i - k + 1] = pq.peek()[0];
        }
        return ans;
    }
}
```

- [题解](https://leetcode.cn/problems/sliding-window-maximum/solutions/543426/hua-dong-chuang-kou-zui-da-zhi-by-leetco-ki6m/?envType=study-plan-v2&envId=top-100-liked)
- 关键在于如何优化`[i,i+k]`之间的最大值求解过程为 $O(1)$。
- 优先队列，插入和删除的都是 $O(log N)$
- 维护优先队列，只需要移除 index 在`[i,i+k]`窗口外的最大值。
### [最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

```java
class Solution {
    public String minWindow(String s, String t) {
        if(t.length() > s.length()) return "";
        HashMap<Character,Integer> map = new HashMap<>();
        char[] arr1 = t.toCharArray();
        for(char c : arr1){
            map.put(c,map.getOrDefault(c,0) + 1);
        }
        int left = 0;
        int right = 0;
        int need = 0;
        char[] arr2 = s.toCharArray();
        int minL = 0;
        int minR = -1;
        while(right < s.length()){
            if(map.containsKey(arr2[right])){
                int size = map.get(arr2[right]);
                map.put(arr2[right],size - 1);
                if(size == 1){
                    need ++;
                }
            }
            // 收缩
            while(left <= right && need == map.size()){
                if(minR == -1 || right - left < minR - minL){
                    minL = left;
                    minR = right;
                }
                if(map.containsKey(arr2[left])){
                    int size = map.get(arr2[left]);
                    map.put(arr2[left],size + 1);
                    if(size == 0){
                        need --;
                    }
                }
                left ++;
            }
            right ++;
        }
        return s.substring(minL,minR + 1);
    }
}
```

- 哈希表记录 t 字符串中字符个数
- 遍历 s 字符串，当哈希表中对应字符的个数减到 0 时，表示子串（窗口内）已经满足特定字符的个数，所以 $need + 1$。
- 当满足 $need=map.size()$ 时，该子串满足记录/收缩条件；比较字串长度来分别设置 minL、minR 索引。
- 同理，当哈希表中对应字符的个数累加到 0 时，表明子串（窗口内）不再满足特定字符的个数，所以 $need - 1$。
## 普通数组
### [最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        int res = dp[0];
        for(int i = 1; i < nums.length; i++){
            dp[i] = Math.max(dp[i-1] + nums[i],nums[i]);
            res = Math.max(dp[i],res);
        }
        return res;
    }
}
```

- 理解 $dp[i] = Math.max(dp[i-1] + nums[i],nums[i])$ 表达式：从 `nums[0] -> nums[i]` 之间连续子数组和的最优解。
### [合并区间](https://leetcode.cn/problems/merge-intervals/)

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        List<int[]> res = new ArrayList<>();
        Arrays.sort(intervals, (o1,o2) -> o1[0] - o2[0]);
        for(int i = 0; i < intervals.length; i ++){
            // 相交 x1<=x2<=y1<=y2(x1<=x2<=y2<=y1)
            while(i+1 < intervals.length && intervals[i][1] >= intervals[i+1][0]){
                // 合并
                intervals[i+1][0] = intervals[i][0];
                intervals[i+1][1] = Math.max(intervals[i][1],intervals[i+1][1]);
                i++;
            }
            // 相隔 x1<y1<x2<y2
            res.add(intervals[i]);
        }
        return res.toArray(new int[res.size()][2]);
    }
}
```

- 理解区间相隔、相交的表达式。
- 排序后根据 $intervals[i][1] >= intervals[i+1][0]$ 判断是否需要合并。
- 合并和后的结果可能需要继续参与下一步判断，所以需要使用 while 循环。
### [轮转数组](https://leetcode.cn/problems/rotate-array/)

**解法一**

```java
class Solution {
    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k %= n;
        int count = 0; // 移动的次数，每一个元素移动1次 共n次
        int cntIndex = 0; // 初始索引
        int nextIndex = k % n ; // 下一个位置索引
        int move = nums[0]; // 移动的元素值
        while (count < n) {
            int next = nums[nextIndex];
            nums[nextIndex] = move;
            move = next;
            count++;
            // 成环
            if (count < n && cntIndex == nextIndex) {
                cntIndex ++;
                move = nums[cntIndex];
                nextIndex =  (cntIndex + k) % n;
                continue;
            }
            nextIndex = (nextIndex + k) % n;
        }
    }
}
```

- 参考环形链表/反转链表的特点。
- 通过 cntIndex 和 nextIndex 变量来判断何时成环。

**解法二**

```java
class Solution {
    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k %= n;
        // 整体反转 0->n-1
        reverse(0,n - 1,nums);
        // 反转0->k -1
        reverse(0,k-1,nums);
        // 反转n-k -> n - 1
        reverse(k,n - 1,nums);
    }
    public void reverse(int l,int r,int[] nums) {
        while (l < r) {
            int temp = nums[l];
            nums[l ++] = nums[r];
            nums[r --] = temp;
        }
    }
}
```

```java
nums   = "--->-->"; k =3
result = "-->--->"; 
// 解释
reverse "--->-->" we can get "<--<-----" 
reverse "<--"     we can get "--><-----" 
reverse "<-----"  we can get "-->----->" 
```

- 引用自[美服](https://leetcode.com/)翻转做法下面的第一条评论

**解法三**

```java
class Solution {
    public void rotate(int[] nums, int k) {
        if(k == 0) return;
        int n = nums.length;
        int index = n - (k % n);
        int[] copy = new int[n];
        int j = 0;
        for(int i = index; i < n; i++){
            copy[j++] = nums[i];
        }
        for(int i = 0; i < index; i++){
            copy[j++] = nums[i];
        }
        for(int i = 0; i < n; i++){
            nums[i] = copy[i];
        }
        return;
    }
}
```

- 扩容
### [除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/)

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] answer = new int[n];
        int[] suf = new int[n];
        suf[n-1] = 1;
        for(int i = n-2; i >= 0; i--){
            suf[i] = suf[i+1] * nums[i+1];
        }
        int pre = 1;
        for(int i = 0; i < n; i++){
            answer[i] = pre * suf[i];
            pre *= nums[i];
        }
        return answer;
    }
}
```

- 分别求 i 的前/后缀和
### [缺失的第一个正数](https://leetcode.cn/problems/first-missing-positive/)

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            while (nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                int temp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = temp;
            }
        }
        for (int i = 0; i < n; ++i) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return n + 1;
    }
}
```

- [题解](https://leetcode.cn/problems/first-missing-positive/solutions/304743/que-shi-de-di-yi-ge-zheng-shu-by-leetcode-solution/?envType=study-plan-v2&envId=top-100-liked)
- `x=nums[i]`，如果 $x∈[1,N]$，我们就知道 x 应当出现在数组中的 x−1 的位置，因此交换 `nums[i]` 和 `nums[x−1]`
- 如果 $nums[i]=nums[x−1]$，那么就会无限交换下去，因此控制交换条件 $nums[i]!=nums[x−1]$。
## 矩阵
### [矩阵置零](https://leetcode.cn/problems/set-matrix-zeroes/)

**解法一**

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        int[] rowS = new int[row];
        int[] colS = new int[col];
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (matrix[i][j] == 0) {
                    // i行j列需要为0
                    rowS[i] = -1;
                    colS[j] = -1;
                }
            }
        }
        for (int i = 0; i < row; i++) {
            if (rowS[i] == -1) {
                for (int j = 0; j < col; j++) {
                    matrix[i][j] = 0;
                }
            }
        }
        for (int i = 0; i < col; i++) {
            if (colS[i] == -1) {
                for (int j = 0; j < row; j++) {
                    matrix[j][i] = 0;
                }
            }
        }
    }
}
```

- 通过状态数组`rowS[i]`、`colS[j]`，分别表示 matrix 数组的 i 行 j 列是否需要全部置换为 0。

**解法二**

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        // 第一行和第一列的状态
        boolean row0 = false;
        boolean col0 = false;
        for (int i = 0; i < row; i++) {
            if (matrix[i][0] == 0) {
                col0 = true;
                break;
            }
        }
        for (int i = 0; i < col; i++) {
            if (matrix[0][i] == 0) {
                row0 = true;
                break;
            }
        }
        // 用matrix的第一行和第一列记录matrix的状态
        for (int i = 1; i < row; i++) {
            for (int j = 1; j < col; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }
        for (int i = 1; i < row; i++) {
            if (matrix[i][0] == 0) {
                for (int j = 0; j < col; j++) {
                    matrix[i][j] = 0;
                }
            }
        }
        for (int i = 1; i< col; i++) {
            if (matrix[0][i] == 0) {
                for (int j = 0; j < row; j++) {
                    matrix[j][i] = 0;
                }
            }
        }
        if (row0) {
            for (int i = 0; i < col; i++) {
                matrix[0][i] = 0;
            }
        }
        if (col0) {
            for (int i = 0; i < row; i++) {
                matrix[i][0] = 0;
            }
        }
    }
}
```

- [题解](https://leetcode.cn/problems/set-matrix-zeroes/solutions/3638986/zhuang-tai-shu-zu-yuan-shu-zu-ya-suo-kon-py88/?envType=study-plan-v2&envId=top-100-liked)
- 这个常量空间的解决方案，关键在于利用自身数组的第 1 行、第 1 列作为原数组 i 行 j 列存在 0 的状态数组。
### [螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
      int m = matrix.length;
      int n = matrix[0].length;
      List<Integer> res = new ArrayList<>();
      int left = 0;
      int right = n-1;
      int top = 0;
      int down = m-1;
      while(res.size() < m*n){
        for(int i = left; i <= right; i++){
          res.add(matrix[top][i]);
        }
        for(int i = top+1; i <= down; i++){
          res.add(matrix[i][right]);
        }
        if(left < right && top < down){
          for(int i = right - 1; i > left; i--){
            res.add(matrix[down][i]);
          }
          for(int i = down; i > top; i--){
            res.add(matrix[i][left]);
          }
        }
        left ++;
        right --;
        top ++;
        down --;
      }
      return res;
    }
}
```

- [题解](https://leetcode.cn/problems/spiral-matrix/solutions/3639254/an-ceng-bian-li-ju-zhen-tong-guo-fang-xi-s7y6/?envType=study-plan-v2&envId=top-100-liked)
- 向右 $[left,rigth]$、向下 $[top+1,down]$、向左 $[right-1,left)$、向上 $[down,top)$
- 向左和向上遍历为了避免和向右、向下遍历的是同一行同一列，需要满足 $left<right,top<down$
### [旋转图像](https://leetcode.cn/problems/rotate-image/)

```java
 class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // 沿对角线 \ 翻转
        for(int i = 0; i < n; i++){
            for(int j = i + 1; j < n; j++){
                int temp = matrix[j][i];
                matrix[j][i] = matrix[i][j];
                matrix[i][j] = temp;
            }
        }
        // 左右翻转
        for(int i = 0; i < n; i++){
            for(int l=0,r=n-1; l < r; l++,r--){
                int temp = matrix[i][r];
                matrix[i][r] = matrix[i][l];
                matrix[i][l] = temp;
            }
        }
    }
}
```

- [题解](https://leetcode.cn/problems/rotate-image/solutions/3640315/xuan-zhuan-ju-zhen-zhao-gui-lu-kan-rowhe-5e3s/?envType=study-plan-v2&envId=top-100-liked)
- 将旋转通过两次翻转完成（翻转方式有多种，例如先上下再沿 \ 对角线），具体公式见题解。
### [搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        // 模拟搜索树
        int row = matrix.length;
        int col = matrix[0].length;
        int i = 0;
        int j = col - 1;
        while (i < row && i >= 0 && j < col && j >= 0) {
            if (matrix[i][j] == target) {
                return true;
            } else if (matrix[i][j] < target) {
                i++;
            } else {
                j--;
            }
        }
        return false;
    }
}
```

- 根据题目要求，当根节点为 $(0,n-1)$ 时，本数组是一个逻辑二叉搜索树。
- 其他解法：每一行二分（升序排列，二分条件天然）
## 链表
### [相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        // a+b+c = c+b+a
        ListNode nodeA = headA;
        ListNode nodeB = headB;
        while(nodeA != null || nodeB != null){
            if(nodeA == nodeB){
                return nodeA;
            }
            nodeA = nodeA == null ? headB:nodeA.next;
            nodeB = nodeB == null ? headA:nodeB.next;
        }
        return null;
    }
}
```

- 速度一致
- $a+b+c=c+b+a$
### [反转链表](https://leetcode.cn/problems/reverse-linked-list/)

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null || head.next == null){
            return head;
        }
        ListNode cntNode = head;
        ListNode pre = null;
        while(cntNode != null){
            ListNode temp = cntNode.next;
            cntNode.next = pre;
            pre = cntNode;
            cntNode = temp;
        }
        return pre;
    }
}
```

- 链表反转：cntNode -> pre
### [回文链表](https://leetcode.cn/problems/palindrome-linked-list/)

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        if(head.next == null){
            return true;
        }
        // 先反转再判断
        ListNode s = head;
        ListNode f = head;
        while(f != null && f.next != null){
            s = s.next;
            f = f.next.next;
        }
        ListNode pre = null;
        while(s != null){
            ListNode temp = s.next;
            s.next = pre;
            pre = s;
            s = temp;
        }
        while(pre != null && head != null){
            if(pre.val == head.val){
                pre = pre.next;
                head = head.next;
            }else{
                return false;
            }
        }
        return true;
    }
}
```

- 通过快慢指针找到中间节点，$v1:v2=1:2$
- 反转中间节点到尾节点部分，再比较：A->B->C => A->B<-C
### [环形链表](https://leetcode.cn/problems/linked-list-cycle/)

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        Set<ListNode> hashSet = new HashSet<>();
        ListNode cur = head;
        while(cur != null){
            if(hashSet.contains(cur)){
                return true;
            }
            hashSet.add(cur);
            cur = cur.next;
        }
        return false;
    }
}
```

- 哈希表、Floyd Cycle Detection Algorithm 都可以
### [环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if(head == null || head.next == null){
            return null;
        }
        ListNode slow = head;
        ListNode fast = head;
        while(fast != null && fast.next != null){
          slow = slow.next;
          fast = fast.next.next;
          // 有环
          if(slow == fast){
            break;
          }
        }
        // 无环
        if(fast == null || fast.next == null){
            return null;
        }
        // 相交节点
        while(slow != head){
            slow = slow.next;
            head = head.next;
        }
        return slow;
    }
}
```

- 哈希表、Floyd Cycle Detection Algorithm 都可以
### [合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode head = new ListNode();
        ListNode node = head;
        while(list1 != null && list2 != null){
            if(list1.val < list2.val){
                node.next = list1;
                list1 = list1.next;
            }else{
                node.next = list2;
                list2 = list2.next;
            }
            node = node.next;
        }
        if(list1 == null && list2 != null){
            node.next = list2;
        }
        if(list2 == null && list1 != null){
            node.next = list1;
        }
        return head.next;
    }
}
```

- 类似归并排序中的有序数组合并过程。
### [两数相加](https://leetcode.cn/problems/add-two-numbers/)

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode();
        ListNode cnt = head;
        int jw = 0;
        while(l1 != null || l2 != null){
            int val = jw;
            if(l1 != null){
                val += l1.val;
                l1 = l1.next;
            }
            if(l2 != null){
                val += l2.val;
                l2 = l2.next;
            }
            jw = val / 10;
            val = val % 10;
            cnt.next = new ListNode(val);
            cnt = cnt.next;
        }
        if(jw != 0){
            cnt.next = new ListNode(jw);
        }
        return head.next;
    }
}
```

- 注意进位的求解，以及最后一位的进位数需要判断是否 = 1
### [删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode cnt = head;
        for(int i = 0; i < n; i++) {
            cnt = cnt.next;
        }
        ListNode slow = head;
        while(cnt != null && cnt.next != null){
            slow = slow.next;
            cnt = cnt.next;
        }
        if(cnt != null) {
            slow.next = slow.next.next;
        }
        else {
            return head.next;
        }
        return head;
    }
}
```

- 双指针，令 slow 和 cnt 之间的距离为 n。
### [两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null) return null;
        ListNode newHead = new ListNode();
        newHead.next = head;
        ListNode cnt = newHead;
        while(cnt.next != null && cnt.next.next != null){
            ListNode temp = cnt.next;
            cnt.next = cnt.next.next;
            ListNode temp2 = cnt.next.next;
            cnt.next.next = temp;
            cnt = cnt.next.next;
            cnt.next = temp2;
        }
        return newHead.next;
    }
}
```

- 需要设置一个虚拟头节点方便节点交换
- 节点交换过程如下图所示

![](https://cdn.img.turingzy.cn/2025/20250819.webp)
### [K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode dummy = new ListNode();
        dummy.next = head;
        ListNode newHead = dummy;
        // fast 为下一组翻转的 head 节点
        ListNode fast = head;
        ListNode slow = null;
        int cnt = 0;
        while(fast != null) {
            // slow 为当前组翻转的 head 节点
            slow = fast;
            while(fast != null && cnt < k){
                fast = fast.next;
                cnt ++;
            }
            // 满足 k 个节点一组翻转
            if(cnt == k){
                ListNode pre = null;
                ListNode curNode = slow;
                while(cnt > 0){
                    ListNode temp = curNode.next;
                    curNode.next = pre;
                    pre = curNode;
                    curNode = temp;
                    cnt --;
                }
                // 翻转后的 slow 变为 tail 节点,链接下一组的 head 节点
                slow.next = fast;
                // 当前组的 head 节点链接翻转后的 head 节点
                newHead.next = pre;
                // 下一组翻转前的 dummy head node
                newHead = slow;
            }else{
                break;
            }
        }
        return dummy.next;
    }
}
```

- dummy head node 技巧
- 两个关键的节点链接（头和尾），`newHead.next = pre` 以及 `slow.next = fast`。
### [随机链表的复制](https://leetcode.cn/problems/copy-list-with-random-pointer/)

```java
class Solution {
    public Node copyRandomList(Node head) {
        if(head == null) return null;
        Node dummy = new Node(0);
        Node cnt = dummy;
        // origin->copy
        HashMap<Node,Node> map = new HashMap<>();
        while(head != null){
            Node copy = null;
            if(map.containsKey(head)){
                copy = map.get(head);
            }else{
                copy = new Node(head.val);
                map.put(head,copy);
            }
            if(head.random != null){
                if(map.containsKey(head.random)){
                    copy.random = map.get(head.random);
                }else{
                    Node n2 = new Node(head.random.val);
                    map.put(head.random,n2);
                    copy.random = n2;
                }            
            }
            cnt.next = copy;
            cnt = cnt.next;
            head = head.next;
        }
        return dummy.next;
    }
}
```

- 哈希表记录 origin node -> copy node
### [排序链表](https://leetcode.cn/problems/sort-list/)

```java
class Solution {
    public ListNode sortList(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        ListNode mid = midNode(head);
        ListNode newHead = mid.next;
        mid.next = null;
        // 归并
        ListNode h1 = sortList(head);
        ListNode h2 = sortList(newHead);
        ListNode dummy = new ListNode();
        ListNode cnt = dummy;
        while(h1 != null && h2 != null){
            if(h1.val < h2.val){
                cnt.next = h1;
                h1 = h1.next;
            }else{
                cnt.next = h2;
                h2 = h2.next;
            }
            cnt = cnt.next;
        }
        cnt.next = h1 == null ? h2 : h1;
        return dummy.next;
    }
    // 中间节点
    public ListNode midNode(ListNode head) {
        ListNode slow = head;
        ListNode fast = head.next;
        while (fast != null && fast.next != null)  {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```

- 归并排序 + 求链表中间节点的双指针算法
### [合并 K 个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        // 拼接后排序
        int k = lists.length;
        if(k == 0) return null;
        ListNode dummy = new ListNode();
        ListNode cnt = dummy;
        for(ListNode h1 : lists){
            if(h1 == null) continue;
            cnt.next = h1;
            while(cnt.next != null){
                cnt = cnt.next;
            }
        }
        return sortList(dummy.next);
    }
    // 归并排序
    public ListNode sortList(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        ListNode mid = midNode(head);
        ListNode newHead = mid.next;
        mid.next = null;
        // 归并
        ListNode h1 = sortList(head);
        ListNode h2 = sortList(newHead);
        ListNode dummy = new ListNode();
        ListNode cnt = dummy;
        while(h1 != null && h2 != null){
            if(h1.val < h2.val){
                cnt.next = h1;
                h1 = h1.next;
            }else{
                cnt.next = h2;
                h2 = h2.next;
            }
            cnt = cnt.next;
        }
        cnt.next = h1 == null ? h2 : h1;
        return dummy.next;
    }
    // 中间节点
    public ListNode midNode(ListNode head) {
        ListNode slow = head;
        ListNode fast = head.next;
        while (fast != null && fast.next != null)  {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```

- 拼接后归并排序整个链表
### [LRU 缓存](https://leetcode.cn/problems/lru-cache/)

```java
public class LRUCache {
    class DLinkedNode {
        int key;
        int value;
        DLinkedNode prev;
        DLinkedNode next;
        public DLinkedNode() {}
        public DLinkedNode(int _key, int _value) {key = _key; value = _value;}
    }
  
    private Map<Integer, DLinkedNode> cache = new HashMap<Integer, DLinkedNode>();
    private int size;
    private int capacity;
    private DLinkedNode head, tail;
  
    public LRUCache(int capacity) {
        this.size = 0;
        this.capacity = capacity;
        // 使用伪头部和伪尾部节点
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            return -1;
        }
        // 如果 key 存在，先通过哈希表定位，再移到头部
        moveToHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            // 如果 key 不存在，创建一个新的节点
            DLinkedNode newNode = new DLinkedNode(key, value);
            // 添加进哈希表
            cache.put(key, newNode);
            // 添加至双向链表的头部
            addToHead(newNode);
            ++size;
            if (size > capacity) {
                // 如果超出容量，删除双向链表的尾部节点
                DLinkedNode tail = removeTail();
                // 删除哈希表中对应的项
                cache.remove(tail.key);
                --size;
            }
        }
        else {
            // 如果 key 存在，先通过哈希表定位，再修改 value，并移到头部
            node.value = value;
            moveToHead(node);
        }
    }
  
    private void addToHead(DLinkedNode node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(DLinkedNode node) {
        removeNode(node);
        addToHead(node);
    }

    private DLinkedNode removeTail() {
        DLinkedNode res = tail.prev;
        removeNode(res);
        return res;
    }
}
```

- [题解](https://leetcode.cn/problems/lru-cache/solutions/259678/lruhuan-cun-ji-zhi-by-leetcode-solution/?envType=study-plan-v2&envId=top-100-liked)
- 哈希表+双向链表，通过双向链表维持 [LRU (最近最少使用) 缓存](https://baike.baidu.com/item/LRU) 。
## 二叉树

### 二叉树的遍历-7种

```java
// 前序遍历 (Preorder Traversal)
// 递归
public void dfs(TreeNode root) {
    if (root == null) {
        return;
    }
    System.out.print(root.val + " "); // 访问根节点
    dfs(root.left);
    dfs(root.right);
}
// 迭代
import java.util.Stack;
public void main(TreeNode root) {
    if (root == null) {
        return;
    }
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        System.out.print(node.val + " ");
        // 关键点：先入栈右孩子，再入栈左孩子
        if (node.right != null) {
            stack.push(node.right);
        }
        if (node.left != null) {
            stack.push(node.left);
        }
    }
}

// 中序遍历 (Inorder Traversal)
// 递归
public void dfs(TreeNode root) {
    if (root == null) {
        return;
    }
    dfs(root.left);
    System.out.print(root.val + " "); // 访问根节点
    dfs(root.right);
}
// 迭代
import java.util.Stack;
public void main(TreeNode root) {
    Stack<TreeNode> stack = new Stack<>();
    TreeNode curr = root;
    while (curr != null || !stack.isEmpty()) {
        // 一路向左，将所有左子节点入栈
        while (curr != null) {
            stack.push(curr);
            curr = curr.left;
        }
        curr = stack.pop();
        System.out.print(curr.val + " ");
        curr = curr.right;
    }
}

// 后序遍历 (Postorder Traversal)
// 递归
public void dfs(TreeNode root) {
    if (root == null) {
        return;
    }
    dfs(root.left);
    dfs(root.right);
    System.out.print(root.val + " "); // 访问根节点
}
//迭代
import java.util.Stack;
import java.util.LinkedList;
import java.util.Collections;
public void postorderTraversalIterative(TreeNode root) {
    if (root == null) {
        return;
    }
    Stack<TreeNode> stack1 = new Stack<>();
    Stack<TreeNode> stack2 = new Stack<>();
    stack1.push(root);
    // 第一个栈用于以“根右左”的顺序遍历
    while (!stack1.isEmpty()) {
        TreeNode node = stack1.pop();
        stack2.push(node);
        if (node.left != null) {
            stack1.push(node.left);
        }
        if (node.right != null) {
            stack1.push(node.right);
        }
    }
    // 第二个栈以“左右根”的顺序输出
    while (!stack2.isEmpty()) {
        System.out.print(stack2.pop().val + " ");
    }
}

// 层序遍历 (Level Order Traversal) - 迭代
import java.util.Queue;
import java.util.LinkedList;
public void bfs(TreeNode root) {
    if (root == null) {
        return;
    }
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        System.out.print(node.val + " ");
        if (node.left != null) {
            queue.offer(node.left);
        }
        if (node.right != null) {
            queue.offer(node.right);
        }
    }
}
```
### [二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

1、递归

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        dfs(res,root);
        return res;
    }
    // 中序遍历
    public void dfs(List<Integer> ans, TreeNode node){
        if(node == null){
            return;
        }
        dfs(ans,node.left);
        ans.add(node.val);
        dfs(ans,node.right);
    }
}
```

2、迭代

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while(cur != null || !stack.empty()){
            if(cur != null){
                stack.push(cur);
                cur = cur.left;
            }else {
                cur = stack.pop();
                res.add(cur.val);
                cur = cur.right;
            }
        }
        return res;
    }
}
```

- 深度优先搜索 - 中序遍历的迭代方式 - 栈
### [二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

1、迭代

```java
class Solution {
    public int maxDepth(TreeNode root) {
        Deque<TreeNode> queue = new ArrayDeque<>();
        int res = 0;
        if(root != null){
            queue.add(root);
        }else{
            return res;
        }
        while(!queue.isEmpty()){
            res ++;
            int size = queue.size();
            while(size -- > 0){
                TreeNode cur = queue.pop();
                if(cur.left != null) queue.add(cur.left);
                if(cur.right != null) queue.add(cur.right);
            }
        }
        return res;
    }
}
```

2、递归

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if(root == null){
            return 0;
        }else{
            int leftHeight = maxDepth(root.left);
            int rightHeight = maxDepth(root.right);
            return 1 + Math.max(leftHeight,rightHeight);
        }
    }
}
```

- 广度优先搜索 - 层序遍历 - 队列
- **前序求深度与后序求高度**，最大高度等于最大深度
- 求树的最大深度可以理解为是左子树或者右子树里最大深度 +1，这样就把原问题拆解成了两个子问题
### [翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        dfs(root);
        return root;
    }
    // 翻转当前 root 节点的左右子树
    public void dfs(TreeNode root) {
        if(root == null){
            return;
        }
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        dfs(root.left);
        dfs(root.right);
    }
}
```

- `dfs(TreeNode root)`：翻转当前节点的左右子树（理解该方法的这个功能即可，不要人脑压栈）。
### [对称二叉树](https://leetcode.cn/problems/symmetric-tree/)

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root == null) {
            return true;
        }
        return compare(root.left, root.right);
    }
    public boolean compare(TreeNode left, TreeNode right) {
        if (left != null && right == null) {
            return false;
        }
        else if (left == null && right != null) {
            return false;
        }
        else if (left == null && right == null) {
            return true;
        }
        else if (left.val != right.val) {
            return false;
        }
        boolean outside = compare(left.left, right.right); // 左子树左、右子树右
        boolean inside = compare(left.right, right.left); // 左子树右、右子树左
        return outside && inside; // 内侧和外侧值是否相等
    }
}
```

- 递归传递左右子树的根节点，不是之前只传当前子树根节点。
### [二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)

```java
class Solution {
    int ans;
    public int diameterOfBinaryTree(TreeNode root) {
        ans = 1;
        depth(root);
        return ans - 1;
    }
    public int depth(TreeNode node) {
        if (node == null) {
            return 0; // 访问到空节点了，返回0
        }
        int L = depth(node.left); // 左儿子为根的子树的深度
        int R = depth(node.right); // 右儿子为根的子树的深度
        ans = Math.max(ans, L+R+1); // 计算d_node即L+R+1 并更新ans
        return Math.max(L, R) + 1; // 返回该节点为根的子树的深度
    }
}
```

- [题解](https://leetcode.cn/problems/diameter-of-binary-tree/?envType=study-plan-v2&envId=top-100-liked)
- 假设我们知道对于该节点的左儿子向下遍历经过最多的节点数 L （即以左儿子为根的子树的深度） 和其右儿子向下遍历经过最多的节点数 R （即以右儿子为根的子树的深度），那么以该节点为起点的路径经过节点数的最大值即为 $L+R+1$。
### [二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        if(root == null){
            return new ArrayList();
        }
        Deque<TreeNode> queue = new LinkedList();
        queue.add(root);
        List<List<Integer>> res = new ArrayList<>();
        while(!queue.isEmpty()){
            List<Integer> ans = new ArrayList<>();
            int size = queue.size();
            while(size-- > 0){
                TreeNode cur = queue.pop();
                ans.add(cur.val);
                if(cur.left != null) queue.add(cur.left);
                if(cur.right != null) queue.add(cur.right);
            }
            res.add(ans);
        }
        return res;
    }
}
```

- 借助队列的“先进先出”特点
### [将有序数组转换为二叉搜索树](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        if(nums.length == 0) return null;
        return dfs(nums,0,nums.length - 1);
    }
    public TreeNode dfs(int[] nums,int l,int r){
        if(l > r){
            return null;
        }
        int mid = (l + r) >> 1;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = dfs(nums,l,mid-1);
        root.right = dfs(nums,mid+1,r);
        return root;
    }
}
```

- 想要二叉搜索树平衡，需要始终选用数组中间节点。
- 选取的子数组范围采用闭区间 `[l,r]`，递归需注意区间的开闭统一。
### [验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

```java
class Solution {
    TreeNode pre = null;
    public boolean isValidBST(TreeNode root) {
        return dfs(root);
    }
    public boolean dfs(TreeNode root){
        if(root == null){
            return true;
        }
        boolean leftTree = dfs(root.left);
        if(pre != null && pre.val >= root.val) {
            return false;
        }else{
            pre = root;
        }
        boolean rightTree = dfs(root.right);
        return leftTree && rightTree;
    }
}
```

- 二叉搜素树的中序遍历元素是递增的。
- 可以存储在数组后进行判断；优化就是直接遍历二叉树的过程中比较，定义一个全局变量记录前一个节点数值。
### [二叉搜索树中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/)

```java
class Solution {
    int size = 0;
    int res = -1;
    boolean st = false;
    public int kthSmallest(TreeNode root, int k) {
        dfs(root,k);
        return res;        
    }
    public void dfs(TreeNode root,int k){
        if(root == null || st){
            return;
        }
        dfs(root.left,k);
        if(st) return;
        size++;
        if(size == k){
            res = root.val;
            st = true;
            return;
        }
        dfs(root.right,k);
    }
}
```

- 二叉搜素树的中序遍历元素是递增的。
- 通过计数器以及找到第 K 小的数后剪枝。
### [二叉树的右视图](https://leetcode.cn/problems/binary-tree-right-side-view/)

```java
class Solution {
    List<Integer> res = new ArrayList<>();
    public List<Integer> rightSideView(TreeNode root) {
        dfs(root,0);
        return res;
    }
    public void dfs(TreeNode root,int depth){
        if(root == null) return;
        if(res.size() == depth){
            res.add(root.val);
        }
        dfs(root.right,depth+1);
        dfs(root.left,depth+1);
    }
}
```

- 每一层最多只能添加一个节点值
- 利用 `res.size()` 来判断当前的最大深度
- 先右节点再左节点，当某个深度首次到达时，对应的节点就在右视图中

```java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        return dfs(inorder,postorder);
    }
    public TreeNode dfs(int[] inorder,int[] postorder) {
        if(postorder.length == 0 || inorder.length == 0) return null;
        // 后序数组最后一个元素为节点
        int rootValue = postorder[postorder.length - 1];
        TreeNode root = new TreeNode(rootValue);
        // 只有一个叶子节点
        if(postorder.length == 1) return root;
        // 切割中序，根据后序数组节点切割
        int index;
        for(index = 0; index < inorder.length; index ++){
            if(inorder[index] == rootValue) break;
        }
        int[] leftInorder = Arrays.copyOfRange(inorder,0,index);
        int[] rightInorder = Arrays.copyOfRange(inorder,index + 1,inorder.length);
        // 切割后序,根据左中序切割
        int[] leftPostorder = Arrays.copyOfRange(postorder,0,leftInorder.length);
        int[] rightPostorder = Arrays.copyOfRange(postorder,leftInorder.length,postorder.length-1);
        root.left = dfs(leftInorder,leftPostorder);
        root.right = dfs(rightInorder,rightPostorder);
        return root;
    }
}
```

- [题解](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/solutions/50561/tu-jie-gou-zao-er-cha-shu-wei-wan-dai-xu-by-user72/)
- 根据中序+后序的遍历数组，还原二叉树。
- 当后序数组长度为 0，中序数组也一定为 0。同理后序数组长度为 1 时元素值为叶子节点。
- 先根据后序数组的根节点切割中序数组得到左/右子树，再根据中序数组的左子树确定后序数组的切割位置，从而切割后续数组。
- `Arrays.copyOfRange(int[] original, int from, int to)`：复制 original 数组，包前不包后。