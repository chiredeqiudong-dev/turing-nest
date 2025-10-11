---
title: Leetcode Hot 100
slug: leetcode-hot-100
date: 2025-09-06
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
- 前缀和需要理解等式 $Sum(nums[i],nums[j-1])=suf[i]-suf[j]$，`suf[i]` 表示 $Sum(nums[i],nums[n-1])$。（这里是后缀）
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

方法一：

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode ans = null;
        for (int i = 0; i < lists.length; ++i) {
            ans = mergeTwoLists(ans, lists[i]);
        }
        return ans;
    }

    public ListNode mergeTwoLists(ListNode a, ListNode b) {
        if (a == null || b == null) {
            return a != null ? a : b;
        }
        ListNode head = new ListNode(0);
        ListNode tail = head, aPtr = a, bPtr = b;
        while (aPtr != null && bPtr != null) {
            if (aPtr.val < bPtr.val) {
                tail.next = aPtr;
                aPtr = aPtr.next;
            } else {
                tail.next = bPtr;
                bPtr = bPtr.next;
            }
            tail = tail.next;
        }
        tail.next = (aPtr != null ? aPtr : bPtr);
        return head.next;
    }
}
```

方法二：

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        return merge(lists, 0, lists.length - 1);
    }

    public ListNode merge(ListNode[] lists, int l, int r) {
        if (l == r) {
            return lists[l];
        }
        if (l > r) {
            return null;
        }
        int mid = (l + r) >> 1;
        return mergeTwoLists(merge(lists, l, mid), merge(lists, mid + 1, r));
    }

    public ListNode mergeTwoLists(ListNode a, ListNode b) {
        if (a == null || b == null) {
            return a != null ? a : b;
        }
        ListNode head = new ListNode(0);
        ListNode tail = head, aPtr = a, bPtr = b;
        while (aPtr != null && bPtr != null) {
            if (aPtr.val < bPtr.val) {
                tail.next = aPtr;
                aPtr = aPtr.next;
            } else {
                tail.next = bPtr;
                bPtr = bPtr.next;
            }
            tail = tail.next;
        }
        tail.next = (aPtr != null ? aPtr : bPtr);
        return head.next;
    }
}
```

方法三：

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

- [题解](https://leetcode.cn/problems/merge-k-sorted-lists/solutions/219756/he-bing-kge-pai-xu-lian-biao-by-leetcode-solutio-2/)
- 1、顺序合并（参考合并两个升序链表）
- 2、分治合并（如下图）
- 3、拼接整个数组后，归并排序整个链表

![](https://cdn.img.turingzy.cn/2025/20251002.webp)

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
- 哈希表 + 双向链表，通过双向链表维持 [LRU (最近最少使用) 缓存](https://baike.baidu.com/item/LRU) 。
### [LFU 缓存](https://leetcode.cn/problems/lfu-cache/)

```java
class LFUCache {
    private static class Node {
        int key, value, freq = 1; // 新书只读了一次
        Node prev, next;

        Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private final int capacity;
    private final Map<Integer, Node> keyToNode = new HashMap<>();
    private final Map<Integer, Node> freqToDummy = new HashMap<>();
    private int minFreq;

    public LFUCache(int capacity) {
        this.capacity = capacity;
    }

    public int get(int key) {
        Node node = getNode(key);
        return node != null ? node.value : -1;
    }

    public void put(int key, int value) {
        Node node = getNode(key);
        if (node != null) { // 有这本书
            node.value = value; // 更新 value
            return;
        }
        if (keyToNode.size() == capacity) { // 书太多了
            Node dummy = freqToDummy.get(minFreq);
            Node backNode = dummy.prev; // 最左边那摞书的最下面的书
            keyToNode.remove(backNode.key);
            remove(backNode); // 移除
            if (dummy.prev == dummy) { // 这摞书是空的
                freqToDummy.remove(minFreq); // 移除空链表
            }
        }
        node = new Node(key, value); // 新书
        keyToNode.put(key, node);
        pushFront(1, node); // 放在「看过 1 次」的最上面
        minFreq = 1;
    }

    private Node getNode(int key) {
        if (!keyToNode.containsKey(key)) { // 没有这本书
            return null;
        }
        Node node = keyToNode.get(key); // 有这本书
        remove(node); // 把这本书抽出来
        Node dummy = freqToDummy.get(node.freq);
        if (dummy.prev == dummy) { // 抽出来后，这摞书是空的
            freqToDummy.remove(node.freq); // 移除空链表
            if (minFreq == node.freq) { // 这摞书是最左边的
                minFreq++;
            }
        }
        node.freq++; // 看书次数 +1
        pushFront(node.freq, node); // 放在右边这摞书的最上面
        return node;
    }

    // 创建一个新的双向链表
    private Node newList() {
        Node dummy = new Node(0, 0); // 哨兵节点
        dummy.prev = dummy;
        dummy.next = dummy;
        return dummy;
    }

    // 在链表头添加一个节点（把一本书放到最上面）
    private void pushFront(int freq, Node x) {
        Node dummy = freqToDummy.computeIfAbsent(freq, k -> newList());
        x.prev = dummy;
        x.next = dummy.next;
        x.prev.next = x;
        x.next.prev = x;
    }

    // 删除一个节点（抽出一本书）
    private void remove(Node x) {
        x.prev.next = x.next;
        x.next.prev = x.prev;
    }
}

```

- [题解](https://leetcode.cn/problems/lfu-cache/solutions/2457716/tu-jie-yi-zhang-tu-miao-dong-lfupythonja-f56h/)
- 哈希表（分别维护 KEY -> Node，Freq -> NodeHead）+ 双向链表， [最不经常使用（LFU）](https://baike.baidu.com/item/%E7%BC%93%E5%AD%98%E7%AE%97%E6%B3%95)。

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
### [从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

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
###  [二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

方法1：

```java
class Solution {
    List<TreeNode> res = new ArrayList<>();
    public void flatten(TreeNode root) {
        dfs(root);
        TreeNode cur = root;
        for(int i = 1; i < res.size(); i++){
            cur.left = null;
            cur.right = res.get(i);
            cur = cur.right;
        }
    }
    public void dfs(TreeNode root){
        if(root == null){
            return;
        }
        res.add(root);
        dfs(root.left);
        dfs(root.right);
    }
}
```

- 先遍历后展开

方法2：

```java
class Solution {
    public void flatten(TreeNode root) {
        TreeNode curr = root;
        while (curr != null) {
            if (curr.left != null) {
                TreeNode next = curr.left;
                TreeNode pre = next;
                while (pre.right != null) {
                    pre = pre.right;
                }
                pre.right = curr.right;
                curr.left = null;
                curr.right = next;
            }
            curr = curr.right;
        }
    }
}
```

- [题解](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/solutions/356853/er-cha-shu-zhan-kai-wei-lian-biao-by-leetcode-solu/?envType=study-plan-v2&envId=top-100-liked)
- 该节点的左子树中最后一个被访问的节点是左子树中的**最右边的节点**，也是该节点的前驱节点，然后将当前节点的右子节点赋给前驱节点的右子节点。
### [从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if(preorder.length == 0 || inorder.length == 0) {
            return null;
        }
        // 根节点
        TreeNode root = new TreeNode(preorder[0]);
        // 叶子节点
        if(preorder.length == 1){
            return root;
        }
        // 切割中序数组
        int index;
        for(index = 0; index < inorder.length; index++){
            if(inorder[index] == preorder[0]) {
                break;
            }
        }
        int[] lInorder = Arrays.copyOfRange(inorder,0,index);
        int[] rInorder = Arrays.copyOfRange(inorder,index + 1,inorder.length);
        // 切割前序数组
        int[] lPreorder = Arrays.copyOfRange(preorder,1,lInorder.length + 1);
        int[] rPreorder = Arrays.copyOfRange(preorder,lInorder.length + 1,inorder.length);
        root.left = buildTree(lPreorder,lInorder);
        root.right = buildTree(rPreorder,rInorder);
        return root;
    }
}
```

- 只有中序同前序/后序的组合才可以确定一颗二叉树
- 关键在于，前序数组的第 1 个元素就是子树的根节点，然后根据根节点确定中序/前序数组的左右子树，最后递归。
- 注意数组复制的边界。
### [路径总和 III](https://leetcode.cn/problems/path-sum-iii/)

方法一：

```java
class Solution {
    public int pathSum(TreeNode root, long targetSum) {
        if(root == null) return 0;
        // 这里相当于先序遍历每个 root 起点，然后统计该起点满足结果的路径数
        int res = dfs(root,targetSum);
        res += pathSum(root.left,targetSum);
        res += pathSum(root.right,targetSum);
        return res;
    }

    // 表示以节点 root 为起点向下且满足路径总和为 val 的路径数目。
    public int dfs(TreeNode root,long targetSum){
        if(root == null) return 0;
        int res = 0;
        int val = root.val;
        // 当前节点值就满足
        if (val == targetSum) {
            res++;
        }
        res += dfs(root.left,targetSum - val);
        res += dfs(root.right,targetSum - val);
        return res;
    }
}
```

- [题解](https://leetcode.cn/problems/path-sum-iii/solutions/1021296/lu-jing-zong-he-iii-by-leetcode-solution-z9td/)
- 暴力解法：先序遍历每一个节点 root，然后求以节点 root 为起点向下且满足路径总和为 targetSum 的路径数目
- `dfs(TreeNode root,long targetSum) = dfs(root.left,targetSum - val) + dfs(root.right,targetSum - val)`

方法二：

```java
class Solution {
    public int pathSum(TreeNode root, int targetSum) {
        Map<Long, Integer> prefix = new HashMap<Long, Integer>();
        prefix.put(0L, 1);
        return dfs(root, prefix, 0, targetSum);
    }
  
    public int dfs(TreeNode root, Map<Long, Integer> prefix, long curr, int targetSum) {
        if (root == null) {
            return 0;
        }
        int ret = 0;
        curr += root.val;
        ret = prefix.getOrDefault(curr - targetSum, 0);
        prefix.put(curr, prefix.getOrDefault(curr, 0) + 1);
        ret += dfs(root.left, prefix, curr, targetSum);
        ret += dfs(root.right, prefix, curr, targetSum);
        prefix.put(curr, prefix.getOrDefault(curr, 0) - 1);
        return ret;
    }
}
```

- [题解](https://leetcode.cn/problems/path-sum-iii/solutions/1021296/lu-jing-zong-he-iii-by-leetcode-solution-z9td/)
- 同[和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/) 题的思路一致，考察前缀和。
### [二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        return dfs(root,p,q);
    }
    public TreeNode dfs(TreeNode root,TreeNode p,TreeNode q){
        if(root == null) return null;
        if(root == p || root == q) return root;
        // 在左子树中找 p 或 q
        TreeNode left = dfs(root.left,p,q);
        // 在右子树中找 p 或 q
        TreeNode right = dfs(root.right,p,q);
        // 左右都找到 → 当前 root 就是最近公共祖先
        if(left != null && right != null) return root;
        // 情况2：只找到一个 → 把那个往上返回
        if(left != null && right == null) return left;
        if(left == null && right != null) return right;
        return null;
    }
}
```

- 画图
- 后序遍历，这样结果是向上返回（回溯）
- 公共祖先的两种情况
### [二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

```java
class Solution {
    int maxSum = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return maxSum;
    }
    public int maxGain(TreeNode node) {
        if (node == null) {
            return 0;
        }
        // 递归计算左右子节点的最大贡献值
        // 只有在最大贡献值大于 0 时，才会选取对应子节点
        int leftGain = Math.max(maxGain(node.left), 0);
        int rightGain = Math.max(maxGain(node.right), 0);
        // 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值
        int priceNewpath = node.val + leftGain + rightGain;
        // 更新答案
        maxSum = Math.max(maxSum, priceNewpath);
        // 返回节点的最大贡献值
        return node.val + Math.max(leftGain, rightGain);
    }
}
```

- [题解](https://leetcode.cn/problems/binary-tree-maximum-path-sum/solutions/297005/er-cha-shu-zhong-de-zui-da-lu-jing-he-by-leetcode-/?envType=study-plan-v2&envId=top-100-liked)
- 后序遍历，统计每个节点的最大贡献值（该节点为起点的一条路径，路径的和最大）
- 节点的最大路径和等于该节点的值加上左右子节点的最大贡献值（大于 0）。
## 图论
### [岛屿数量](https://leetcode.cn/problems/number-of-islands/)

```java
class Solution {
    // 方向数组：右、上、左、下
    int[] dx = {1, 0, -1, 0};
    int[] dy = {0, -1, 0, 1};

    public int numIslands(char[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int islands = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') { // 发现未访问的陆地
                    dfs(grid, i, j);
                    islands++;
                }
            }
        }
        return islands;
    }
  
    private void dfs(char[][] grid, int x, int y) {
        // 标记当前格子为已访问（直接修改网格）
        grid[x][y] = '0';
        // 遍历四个方向
        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            // 检查是否越界或是否为陆地
            if (nx >= 0 && nx < grid.length && ny >= 0 && ny < grid[0].length && grid[nx][ny] == '1') {
                dfs(grid, nx, ny);
            }
        }
    }
}
```

- DFS
- 经典连通量问题
### [腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/)

```java
class Solution {
    int[] dx = new int[]{1, 0, -1, 0}; 
    int[] dy = new int[]{0, -1, 0, 1};

    public int orangesRotting(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        Deque<int[]> queue = new LinkedList<>();
        int freshCount = 0; // 统计新鲜橘子数量
        boolean[][] visited = new boolean[m][n];
        // 统计新鲜橘子和初始腐烂橘子
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    freshCount++;
                } else if (grid[i][j] == 2) {
                    queue.add(new int[]{i, j}); // 将所有腐烂橘子加入队列
                    visited[i][j] = true; // 标记初始腐烂橘子
                }
            }
        }
        int time = 0; // 时间计数
        // 运行 BFS
        while (!queue.isEmpty()) {
            int size = queue.size();
            boolean hasNewRotten = false; // 是否有新的腐烂橘子
            for (int i = 0; i < size; i++) {
                int[] curr = queue.poll();
                int x = curr[0], y = curr[1];
                // 检查四个方向
                for (int j = 0; j < 4; j++) {
                    int nx = x + dx[j];
                    int ny = y + dy[j];
                    // 跳过越界或已访问或非新鲜橘子
                    if (nx < 0 || nx >= m || ny < 0 || ny >= n || visited[nx][ny] || grid[nx][ny] != 1) {
                        continue;
                    }
                    // 新鲜橘子变为腐烂
                    grid[nx][ny] = 2;
                    visited[nx][ny] = true;
                    queue.add(new int[]{nx, ny});
                    freshCount--; // 减少新鲜橘子计数
                    hasNewRotten = true; // 标记有新的腐烂橘子
                }
            }
            // 只有当有新的腐烂橘子时才增加时间（关键）
            if (hasNewRotten) {
                time++;
            }
        }
        // 如果仍有新鲜橘子未腐烂，返回 -1
        return freshCount == 0 ? time : -1;
    }
}
```

- BFS
- 有一个关键点是在每一次腐烂的过程中，需要通过 hasNewRotten 变量来判断是否有新的橘子被腐烂然后才增加时间。
### [课程表](https://leetcode.cn/problems/course-schedule/)

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // 初始化邻接表和入度数组
        Map<Integer, List<Integer>> adjList = new HashMap<>();
        int[] indegree = new int[numCourses];
        // 构建图：记录课程依赖关系并统计入度
        for (int[] pre : prerequisites) {
            int course = pre[1]; // 被依赖的课程
            int prereq = pre[0]; // 前置课程
            adjList.computeIfAbsent(prereq, k -> new ArrayList<>()).add(course);
            indegree[course]++;
        }
        // 将入度为0的课程加入队列
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                queue.offer(i);
            }
        }
        // BFS处理课程
        int completedCourses = 0;
        while (!queue.isEmpty()) {
            int current = queue.poll();
            completedCourses++;
            // 处理依赖当前课程的后续课程
            List<Integer> dependents = adjList.getOrDefault(current, Collections.emptyList());
            for (int next : dependents) {
                indegree[next]--;
                if (indegree[next] == 0) {
                    queue.offer(next);
                }
            }
        }
        // 判断是否所有课程都能完成
        return completedCourses == numCourses;
    }
}
```

- 拓扑排序
- BFS 解决拓扑排序，统计入度节点和邻接边，队列里记录入度为 0 的节点（可处理课程）。
### [实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

```java
class Trie {
    // 先创建 node 节点的数据结构
    public class Node {
        Node[] son = new Node[26];
        boolean end = false;
    }
    public Node root;

    public Trie() {
        // 初始化 root
        root = new Node();
    }

    public void insert(String word) {
        Node cur = root;
        for(char c : word.toCharArray()) {
	        int index = c-'a';
            if(cur.son[index] == null){
                cur.son[index] = new Node();
            }
            cur = cur.son[index];
        }
        // 完整单词
        cur.end = true;
    }
    
    public boolean search(String word) {
        return find(word) == 1;
    }
    
    public boolean startsWith(String prefix) {
        return find(prefix) != 0;
    }
    
    // 搜索前缀树
    public int find(String word) {
        Node cur = root;
        for(char c : word.toCharArray()) {
        	int index = c-'a';
            if(cur.son[index] == null) {
                // 不存在当前字符
                return 0;
            }
            cur = cur.son[index];
        }
        // 完整单词
        if(cur.end == true) return 1;
        // 只是字符前缀匹配
        return 2;
    }
}
```

- [题解](https://leetcode.cn/problems/implement-trie-prefix-tree/solutions/2993894/cong-er-cha-shu-dao-er-shi-liu-cha-shu-p-xsj4/?envType=study-plan-v2&envId=top-100-liked)
- 二十六叉树
## 回溯
### [全排列](https://leetcode.cn/problems/permutations/)

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public boolean[] st;
    public List<List<Integer>> permute(int[] nums) {
        st = new boolean[nums.length];
        dfs(nums,new ArrayList());
        return res;
    }

    public void dfs(int[] nums,List<Integer> path){
        if(path.size() >= nums.length) {
            res.add(new ArrayList(path));
            return;
        }
        for(int i = 0; i < nums.length; i++){
            if(st[i]) continue;
            st[i] = true;
            path.add(nums[i]);
            dfs(nums,path);
            st[i] = false;
            path.remove(path.size() - 1);
        }
    }
}
```

- [回溯算法总结](https://blog.turingzy.cn/posts/backtracking-algorithm/)
### [子集](https://leetcode.cn/problems/subsets/)

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> subsets(int[] nums) {
        res.add(new ArrayList());
        dfs(nums,0,new ArrayList());
        return res;
    }

    public void dfs(int[] nums,int startIndex,List<Integer> node){
        if(node.size() >= nums.length) {
            return;
        }
        for(int i = startIndex; i < nums.length; i++){
            node.add(nums[i]);
            res.add(new ArrayList(node));
            dfs(nums,i + 1,node);
            node.remove(node.size() - 1);
        }
    }
}
```

- [回溯算法总结](https://blog.turingzy.cn/posts/backtracking-algorithm/)
- 注意 startIndex 是 i + 1，也就是下一层遍历从 `num[i+1]` 开始
### [电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

```java
class Solution {
    public List<String> res = new ArrayList<>();
    public String[] phone;
    public List<String> letterCombinations(String digits) {
        int n = digits.length();
        if(n == 0) return res;
        phone = new String[] {"","","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
        dfs(digits,0,"");
        return res;
    }

    public void dfs(String digits,int index,String ans){
        if(ans.length() >= digits.length()){
            res.add(ans);
            return;
        }
        String arr = phone[digits.charAt(index) - '0'];
        for(int i = 0; i < arr.length(); i++) {
            dfs(digits,index + 1,ans + arr.charAt(i));
        }
    }
}
```

- 通过递归来 n 重循环
- 每一层是 `phone[digits.charAt(index) - '0']`，不再是同一数组
###  [组合总和](https://leetcode.cn/problems/combination-sum/)

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public int goal;
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        goal = target;
        dfs(candidates,0,0,new ArrayList());
        return res;
    }

    public void dfs(int[] candidates,int sum,int startIndex,List<Integer> ans){
        if(sum > goal){
            return;
        }else if(sum == goal){
            res.add(new ArrayList(ans));
            return;
        }

        for(int i = startIndex; i < candidates.length; i++) {
            sum += candidates[i];
            if(sum > goal) break;
            ans.add(candidates[i]);
            // 这个 i 是关键,避免重复搜索
            dfs(candidates,sum,i,ans);
            sum -= candidates[i];
            ans.remove(ans.size() - 1);
        }
    }
}
```

- startIndex 不是 i + 1，因为可以重复选自己；为了不递归重复答案，不能重复选*前面已经选过*的，所以每一层从 i 开始遍历。
### [括号生成](https://leetcode.cn/problems/generate-parentheses/)

方法一：

```java
class Solution {
    public List<String> res = new ArrayList<>();
    public int goal;
    public List<String> generateParenthesis(int n) {
        if(n == 1) {
            res.add("()");
            return res;
        }
        goal = n;
        dfs(new char[]{'(',')'},0,0,"");
        return res;
    }

    public void dfs(char[] arr,int l,int r,String ans) {
        if(ans.length() > 0 && ans.charAt(0) == ')') return;
        // 右括号的个数不能超过左括号的个数。比如 ())( 是不合法的
        if(l > goal || r > goal || r > l) return;
        while(l == goal && r != goal) {
            ans += ')';
            r ++;
        }
        if(l == goal && r == goal){            
            res.add(ans);
            return;
        }

        for(int i = 0; i < 2; i++){
            if(arr[i] == '(') l++;
            else r++;
            dfs(arr,l,r,ans + arr[i]);
            if(arr[i] == '(') l--;
            else r--;
        }
    }
}
```

- 关键：`if(r > l) return`，省去了判断是否有效括号对这一步。

方法二：

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        char[] path = new char[2*n];
        List<String> ans = new ArrayList<>();
        int left=0,rigth=0;
        backtrack(left,rigth,n,ans,path);
        return ans;
    }        

        private void backtrack(int left,int rigth,int n,List<String> ans,char[] path){
        if(rigth==n){
            ans.add(new String(path));
        }
        if(left<n){
            path[left+rigth]='(';
            backtrack(left+1,rigth,n,ans,path);
        }
        if(rigth<left){
            path[left+rigth]=')';
            backtrack(left,rigth+1,n,ans,path);
        }
    }
}
```

- [题解](https://leetcode.cn/problems/generate-parentheses/solutions/2071015/hui-su-bu-hui-xie-tao-lu-zai-ci-pythonja-wcdw/?envType=study-plan-v2&envId=top-100-liked)
### [单词搜索](https://leetcode.cn/problems/word-search/)

```java
class Solution {
    public boolean[][] st;
    public int[] dx = new int[] {0,1,0,-1};
    public int[] dy = new int[] {1,0,-1,0};
    public boolean res = false;

    public boolean exist(char[][] board, String word) {
        int m = board.length;
        int n = board[0].length;
        st = new boolean[m][n];
        char[] words = word.toCharArray();
        for(int i = 0; i<m; i++){
            for(int j = 0; j<n; j++){
                if(board[i][j] == words[0]) {
                    st[i][j] = true;
                    dfs(i,j,board,1,words);
                    st[i][j] = false;
                    if(res) {
                        break;
                    }
                }
            }
        }
        return res;
    }

    public void dfs(int x, int y,char[][] board,int index,char[] word) {
        if(res) {
            return;
        }
        if(index >= word.length) {
            res = true;
            return;
        }
        for(int i = 0; i < 4; i++) {
            int x1 = x + dx[i];
            int y1 = y + dy[i];
            if(x1 < 0 || x1 >= board.length || y1 < 0 || y1 >= board[0].length) {
                continue;
            }
            if(st[x1][y1] || board[x1][y1] != word[index]) {
                continue;
            }
            st[x1][y1] = true;
            dfs(x1,y1,board,index + 1,word);
            st[x1][y1] = false;
        }
    }
}
```

- 对于每一个 `word[0]` 都需要进行一次搜索
- 通过 index 控制下一个字母是否符合搜索条件 `board[x1][y1] == word[index]`
### [分割回文串](https://leetcode.cn/problems/palindrome-partitioning/)

```java
class Solution {
    public List<List<String>> res = new ArrayList<>();

    public List<List<String>> partition(String s) {
        dfs(s,0,new ArrayList<>());
        return res;
    }
  
    public void dfs(String s,int startIndex,List<String> path) {
        if(startIndex >= s.length()) {
            res.add(new ArrayList(path));
            return;
        }
        for(int i = startIndex; i < s.length(); i++) {
	        // 插板，得到子集
            String son = s.substring(startIndex,i + 1);
            // 回文子串
            if(isValid(son)){
                path.add(son);
                dfs(s,i + 1,path);
                path.removeLast();
            }
        }
    }

    public boolean isValid(String str) {
        if(str == null || str.isEmpty()) {
            return false;
        }
        String reversed = new StringBuilder(str).reverse().toString();
        return str.equals(reversed);
    }
}
```

- 递归的过程相当于在每个字母之间插板，选取插板得到的回文子串再递归
### [N 皇后](https://leetcode.cn/problems/n-queens/)

```java
class Solution {
    public char[][] chess;
    public List<List<String>> res = new ArrayList<>();
    public boolean[] st;
    
    public List<List<String>> solveNQueens(int n) {
        chess = new char[n][n];
        st = new boolean[n];
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < n; j++) {
                chess[i][j] = '.';
            }
        }
        dfs(0);
        return res;
    }
  
    public void dfs(int row) {
        if(row >= chess.length) {
            List<String> ans = new ArrayList<>();
            for(char[] c : chess) {
                ans.add(new String(c));
            }
            res.add(ans);
            return;
        }
  
        for(int i = 0; i < chess.length; i++){
            if(isValid(row,i)) {
                st[i] = true;
                chess[row][i] = 'Q';
                dfs(row + 1);
                st[i] = false;
                chess[row][i] = '.';
            }
        }
    }

    public boolean isValid(int x,int y){
        if(st[y]) {
            return false;
        }
        for(int i = x-1,j = y-1; i>=0 && j>=0; i--,j--) {
            if(chess[i][j] == 'Q') return false;
        }
        for(int i = x-1, j = y+1; i>=0 && j<chess.length; i--,j++){
            if(chess[i][j] == 'Q') return false;
        }
        return true;
    }
}
```

- 树的高度是棋盘的行，宽度是棋盘的宽
- 通过列状态数组，避免一次循环判断
## 二分查找
### [搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int n = nums.length;
        int l = -1;
        int r = n;
        while(l + 1 < r) {
            int mid = (l + r) >> 1;
            if(nums[mid] <= target) {
                l = mid;
            }else {
                r = mid;
            }
        }
        // 边界条件
        if(l == -1) return 0;
        return nums[l] == target ? l : l + 1;
    }
}
```

- 这个二分模板最好记，哔哩哔哩搜“五点七边”的二分视频
- 注意 l == -1 的边界条件
### [搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length;
        int n = matrix[0].length;
        int i = 0;
        int j = n - 1;
        while(i < m &&  j >= 0) {
            if(matrix[i][j] > target) {
                j --;
                continue;
            }
            if(matrix[i][j] < target) {
                i ++;
                continue;
            }
            return true;
        }
        return false;
    }
}
```

- 二叉搜索树的思路
- 二分的做法，1、将二维变为一维数组，然后二分；2、先对第一列二分得到 `matrix[0][i] <= target`，然后再二分第 i 行。
### [在排序数组中查找元素的第一个和最后一个](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
	    // >= target 和 >= target + 1 两种边界情况
        int start = f(nums,target,true); // 开始位置
        int end = f(nums,target + 1,false); // 结束位置
        return new int[]{start,end};
    }

    public int f(int[] nums, int target,boolean flag) {
        int n = nums.length;
        if(n == 0) return -1;
        int l = -1;
        int r = n;
        while(l + 1 < r) {
            int mid = (l + r) >> 1;
            if(nums[mid] >= target) {
                r = mid;
            }else{
                l = mid;
            }
        }
        if(r != n && flag && nums[r] == target) return r;
        if(l != -1 && !flag && nums[l] == target - 1) return l;
        return -1;
    }
}
```

- 分别二分 target 和 target + 1
### [搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length;
        int k = 0;
        for(int i = 1; i < n; i ++){
            if(nums[i] < nums[i-1]) {
                k = i;
                break;
            }
        }
        int l1 = binaryS(nums,target,-1,k);
        if(l1 != -1 && nums[l1] == target) {
            return l1;
        }
        int l2 = binaryS(nums,target,k-1,n);
        if(l2 != -1 && nums[l2] == target) {
            return l2;
        }
        return -1;
    }

    public int binaryS(int[] nums,int target,int l,int r) {
        while(l + 1 < r) {
            int mid = (l + r) >> 1;
            if(nums[mid] <= target) {
                l = mid;
            }else{
                r = mid;
            }
        }
        return l;
    }
}
```

- 找到切割点 k，然后分别二分左右两个有序数组
### [寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

```java
class Solution {
    public int findMin(int[] nums) {
        int n = nums.length;
        int l = 0;
        int r = n;
        while(l + 1 < r) {
            int mid = (l + r) >> 1;
            if(nums[mid] > nums[n-1]){
                l = mid;
            }else{
                r = mid;
            }
        }
        if(l == -1 || n == 1) {
            return nums[0];
        }
        return nums[l] > nums[l + 1] ? nums[l+1] : nums[l];
    }
}
```

- [题解](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/solutions/1987499/by-endlesscheng-owgd/?envType=study-plan-v2&envId=top-100-liked)
- 还可以直接双指针前后判断二种情况（旋转后不变、旋转后改变）
### [寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length;
        int n = nums2.length;
        int[] nums = new int[m+n];
        int i = 0;
        int j = 0;
        int index = 0;
        while(i < m && j < n) {
            if(nums1[i] > nums2[j]) {
                nums[index++] = nums2[j++];
            }else{
                nums[index++] = nums1[i++];
            }
        }
        while (i < m) {
            nums[index++] = nums1[i++];
        }
        while (j < n) {
            nums[index++] = nums2[j++];
        }
        if(index % 2 == 0) {
            return (nums[(m+n) / 2] + nums[(m+n) / 2 - 1]) * 1.0 / 2;
        }
        return nums[(m+n) / 2] * 1.0;
    }
}
```

- 先归并后判断
## 栈

### [有效的括号](https://leetcode.cn/problems/valid-parentheses/)

```java
class Solution {
    public boolean isValid(String s) {
        int n = s.length();
        if (n % 2 == 1) {
            return false;
        }
        Map<Character, Character> pairs = new HashMap<Character, Character>() {{
            put(')', '(');
            put(']', '[');
            put('}', '{');
        }};
        Deque<Character> stack = new LinkedList<Character>();
        for (int i = 0; i < n; i++) {
            char ch = s.charAt(i);
            if (pairs.containsKey(ch)) {
                if (stack.isEmpty() || stack.peek() != pairs.get(ch)) {
                    return false;
                }
                stack.pop();
            } else {
                stack.push(ch);
            }
        }
        return stack.isEmpty();
    }
}
```

- 利用栈的先进后出特性
### [最小栈](https://leetcode.cn/problems/min-stack/)

方法一：

```java
class MinStack {
    Deque<Integer> xStack;
    Deque<Integer> minStack;

    public MinStack() {
        xStack = new LinkedList<Integer>();
        minStack = new LinkedList<Integer>();
        minStack.push(Integer.MAX_VALUE);
    }
    
    public void push(int x) {
        xStack.push(x);
        minStack.push(Math.min(minStack.peek(), x));
    }
    
    public void pop() {
        xStack.pop();
        minStack.pop();
    }
    
    public int top() {
        return xStack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

- 利用一个辅助栈记录每一个进栈元素对应的当前最小值

方法二：

```java
class MinStack {
    private Deque<Long> stack;
    private long minValue;

    public MinStack() {
        stack = new LinkedList<>();
    }
    
    public void push(int val) {
        if (stack.isEmpty()) {
            minValue = val;
            stack.push(0L);
        } else {
            long diff = (long) val - minValue;
            stack.push(diff);
            if (diff < 0) {
                minValue = val;
            }
        }
    }
    
    public void pop() {
        if (!stack.isEmpty()) {
            long diff = stack.pop();
            if (diff < 0) {
                minValue = minValue - diff;
            }
        }
    }
    
    public int top() {
        long diff = stack.peek();
        if (diff >= 0) {
            return (int) (minValue + diff);
        } else {
            return (int) minValue;
        }
    }
    
    public int getMin() {
        return (int) minValue;
    }
}
```

- 通过 `long diff = (long) val - minValue` 来达到同样效果，注意当栈为空时需要 `stack.push(0L);`，来统一通过 diff 获取栈顶值。
### [字符串解码](https://leetcode.cn/problems/decode-string/)

```java
class Solution {
    public String decodeString(String s) {
        Deque<Character> stack = new LinkedList<>();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c != ']') {
                stack.push(c);
            } else {
                // 1. 提取字母
                StringBuilder strBuilder = new StringBuilder();
                while (!stack.isEmpty() && stack.peek() != '[') {
                    strBuilder.append(stack.pop());
                }
                String str = strBuilder.reverse().toString(); // 反转回正确顺序
                
                // 2. 移除 '['
                if (!stack.isEmpty()) stack.pop();
                
                // 3. 提取数字（可能是多位数）
                StringBuilder numBuilder = new StringBuilder();
                while (!stack.isEmpty() && Character.isDigit(stack.peek())) {
                    numBuilder.append(stack.pop());
                }
                int times = Integer.parseInt(numBuilder.reverse().toString()); // 反转回正确顺序
                
                // 4. 重复字符串并重新压入栈中
                String repeated = str.repeat(times);
                for (int j = 0; j < repeated.length(); j++) {
                    stack.push(repeated.charAt(j));
                }
            }
        }
        
        // 构建最终结果
        StringBuilder res = new StringBuilder();
        while (!stack.isEmpty()) {
            res.append(stack.pop());
        }
        return res.reverse().toString(); // 需要反转，因为栈是后进先出
    }
}
```

- 模拟
- 注意字符出栈后需要反转，其次提取的数字可能十多位数（也需要反转）
### [每日温度](https://leetcode.cn/problems/daily-temperatures/)

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        Deque<Integer> stack = new LinkedList<>();
        int[] res = new int[n];
        for(int i = n-1; i >= 0; i--) {
            if(stack.isEmpty()) {
                res[i] = 0;
            }else{
                while(!stack.isEmpty() && temperatures[stack.peek()] <= temperatures[i]) {
                    stack.pop();
                }
                res[i] = stack.isEmpty() ? 0 : stack.peek() - i;         
            }
            // 保存元素 index,方便计算相差天数
            stack.push(i);
        }
        return res;
    }
}
```

正序遍历方式

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int length = temperatures.length;
        int[] ans = new int[length];
        Deque<Integer> stack = new LinkedList<Integer>();
        for (int i = 0; i < length; i++) {
            int temperature = temperatures[i];
            while (!stack.isEmpty() && temperature > temperatures[stack.peek()]) {
                int prevIndex = stack.pop();
                ans[prevIndex] = i - prevIndex;
            }
            stack.push(i);
        }
        return ans;
    }
}
```

- [单调栈](https://www.bilibili.com/video/BV1my4y1Z7jj?vd_source=7341c7fca3b496e9108bb1fd49c634ef)，单调栈能够让不需要你需要走重复走的路。刚刚走过一遍的路可以造福后面找的人。
- 对于求某元素左边和右边第一个比它大或第一个比它小的元素位置（值）时，都可以使用单调栈，比如说“接雨水”这题。
- 通过保存元素 index 计算天数
### [柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        if(n == 1) return heights[0];
        int[] left = new int[n];
        int[] rigth = new int[n];
        Deque<Integer> stack = new LinkedList<>();
        for(int i = n-1; i >= 0; i--) {
            if(stack.isEmpty()) {
                rigth[i] = n - i - 1;
            }else{
                while(!stack.isEmpty() && heights[i] <= heights[stack.peek()]) {
                    stack.pop();
                }
                rigth[i] = stack.isEmpty() ? n - i - 1 : stack.peek() - i - 1;
            }
            stack.push(i);
        }
        stack.clear();
        for(int i = 0; i < n; i++) {
            if(stack.isEmpty()) {
                left[i] = i;
            }else{
                while(!stack.isEmpty() && heights[i] <= heights[stack.peek()]) {
                    stack.pop();
                }
                left[i] = stack.isEmpty() ? i : i - stack.peek() - 1;
            }
            stack.push(i);
        }
        int max = Integer.MIN_VALUE;
        for(int i = 0; i < n; i++) {
            max = Math.max(max,heights[i] * (1 + left[i] + rigth[i]));
        }

        return max;
    }
}
```

- [题解](https://leetcode.cn/problems/largest-rectangle-in-histogram/solutions/2695467/dan-diao-zhan-fu-ti-dan-pythonjavacgojsr-89s7/?envType=study-plan-v2&envId=top-100-liked)
- 先理解暴力做法：枚举每个`heights[i]`然后求最大宽度，也就是向左/右遍历找最近小于`heights[i]`的位置
- 单调栈做法：通过单调栈减少不必要的比较（只需要找小于`heights[i]`最近的索引）
## 堆

### [数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

快速排序板子：

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        quicksort(nums,0,nums.length - 1);
        return nums[nums.length - k];
    }
    
    public void quicksort(int[] nums,int left,int right) {
        if(left >= right) {
            return;
        }
        // 求基准值
        setPivot(nums,left,right);
        // 分区
        int pivotIndex = partition(nums,left,right);
        quicksort(nums,left,pivotIndex - 1);
        quicksort(nums,pivotIndex + 1,right);
    }

    // 挖坑法分区
    public int partition(int[] nums, int left, int right) {
    int pivot = nums[left];
    int i = left;
    int j = right;
    while (i < j) {
        while (i < j && nums[j] >= pivot) {
            j--;
        }
        if (i < j) {
            nums[i] = nums[j];
            i++;
        }
        while (i < j && nums[i] <= pivot) {
            i++;
        }
        if (i < j) {
            nums[j] = nums[i];
            j--;
        }
    }
    // 循环结束时，i 和 j 相遇，此时 i (或 j) 就是 pivot 最终的位置
    nums[i] = pivot;
    return i;
}

    // 三数取中法取 pivot,然后设置 nums[left] = pivot;
    public void setPivot(int[] nums,int left,int right) {
        int valLeft = nums[left];
        int valRight = nums[right];
        int valMid = nums[left + (right - left) / 2];
        int maxVal = Math.max(valLeft, Math.max(valRight, valMid));
        int minVal = Math.min(valLeft, Math.min(valRight, valMid));
        nums[left] = valLeft + valRight + valMid - maxVal - minVal;
        if(nums[left] == valLeft) return;
        if(nums[left] == valRight) {
            nums[right] = valLeft;
            return;
        }
        nums[left + (right - left) / 2] = valLeft;
    }
}
```

- 时间复杂度：$O(NlogN)$
- 需要知道每次选取的基准值在分区后的位置不会再改变
- 不稳定排序

快速选择算法：

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return quickselect(nums, 0, nums.length - 1, nums.length - k);
    }
    
    public int quickselect(int[] nums,int left,int right,int targetIndex) {
        if(left >= right) {
            return nums[left];
        }
        // 设置 nums[left] = pivot
        setPivot(nums,left,right);
        // 分区
        int pivotIndex = partition(nums,left,right);
        // 选择分区
        if(pivotIndex == targetIndex) {
            return nums[pivotIndex];
        }else if(pivotIndex < targetIndex) {
            return quickselect(nums,pivotIndex + 1,right,targetIndex);
        }else {
            return quickselect(nums,left,pivotIndex - 1,targetIndex);
        }
    }

    // 挖坑法
    public int partition(int[] nums, int left, int right) {
    int pivot = nums[left];
    int i = left;
    int j = right;
    while (i < j) {
        while (i < j && nums[j] >= pivot) {
            j--;
        }
        if (i < j) {
            nums[i] = nums[j];
            i++;
        }
        while (i < j && nums[i] <= pivot) {
            i++;
        }
        if (i < j) {
            nums[j] = nums[i];
            j--;
        }
    }
    // 循环结束时，i 和 j 相遇，此时 i (或 j) 就是 pivot 最终的位置
    nums[i] = pivot;
    return i;
}

    // 三数取中法求 pivot
    public void setPivot(int[] nums,int left,int right) {
        int valLeft = nums[left];
        int valRight = nums[right];
        int valMid = nums[left + (right - left) / 2];
        int maxVal = Math.max(valLeft, Math.max(valRight, valMid));
        int minVal = Math.min(valLeft, Math.min(valRight, valMid));
        nums[left] = valLeft + valRight + valMid - maxVal - minVal;
        if(nums[left] == valLeft) return;
        if(nums[left] == valRight) {
            nums[right] = valLeft;
            return;
        }
        nums[left + (right - left) / 2] = valLeft;
    }
}
```

- 根据基准值的索引和目标索引判断来选择性的分区
- 时间复杂度近似 $O(N)$
- 本题还可以用堆排序，$O(NlogK)$
### [前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

方法一：

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // 统计频率
        Map<Integer,Integer> map = new HashMap<>();
        int n = nums.length;
        for(int i = 0; i < n; i++) {
            map.put(nums[i],map.getOrDefault(nums[i],0) + 1);
        }
        List<int[]> values = new ArrayList<int[]>();
        for(Map.Entry<Integer,Integer> entry : map.entrySet()) {
            int key = entry.getKey();
            int count = entry.getValue();
            values.add(new int[]{key, count});
        }
        // 快选
        int m = values.size();
        quickselect(values,0,m - 1,m - k);
        // 收集
        int[] res = new int[k];
        for(int i = 0; i < k; i++) {
            res[i] = values.get(m - k + i)[0];
        }
        return res;
    }
    
    public void quickselect(List<int[]> nums,int left,int right,int targetIndex) {
        if(left >= right) {
            return;
        }
        // 设置 nums[left] = pivot
        setPivot(nums,left,right);
        // 分区
        int pivotIndex = partition(nums,left,right);
        // 选择分区
        if(pivotIndex == targetIndex) {
            return;
        }else if(pivotIndex < targetIndex) {
            quickselect(nums,pivotIndex + 1,right,targetIndex);
        }else {
            quickselect(nums,left,pivotIndex - 1,targetIndex);
        }
    }

    // 挖坑法
    public int partition(List<int[]> nums, int left, int right) {
        int[] pivot = nums.get(left);
        int i = left;
        int j = right;
        while (i < j) {
            while (i < j && nums.get(j)[1] >= pivot[1]) {
                j--;
            }
            if (i < j) {
                nums.set(i,nums.get(j));
                i++;
            }
            while (i < j && nums.get(i)[1] <= pivot[1]) {
                i++;
            }         
            if (i < j) {
                nums.set(j,nums.get(i));
                j--;
            }
        }
        // 循环结束时，i 和 j 相遇，此时 i (或 j) 就是 pivot 最终的位置
        nums.set(i,pivot); 
        return i;
    }

    // 三数取中法求 pivot，并将其交换到 left 位置
    public void setPivot(List<int[]> nums, int left, int right) {
        if (left >= right) return;
        int mid = left + (right - left) / 2;
        // 确保 nums.get(left).freq <= nums.get(mid).freq <= nums.get(right).freq
        if (nums.get(left)[1] > nums.get(mid)[1]) {
            swap(nums, left, mid);
        }
        if (nums.get(left)[1] > nums.get(right)[1]) {
            swap(nums, left, right);
        }
        if (nums.get(mid)[1] > nums.get(right)[1]) {
            swap(nums, mid, right);
        }
        swap(nums, left, mid);
    }
    // 辅助交换函数
    public void swap(List<int[]> nums, int i, int j) {
        int[] temp = nums.get(i);
        nums.set(i, nums.get(j));
        nums.set(j, temp);
    }
}
```

- 快选，targetIndex 前面的元素一定是前 k 个高频率元素
- 通过排序集合，元素数组可以在排序后知道当前频率对应的 key
- 注意，通过传递集合排序，分区和取 pivot 时需要交换的应该是数组

方法二：

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> occurrences = new HashMap<Integer, Integer>();
        for (int num : nums) {
            occurrences.put(num, occurrences.getOrDefault(num, 0) + 1);
        }

        // int[] 的第一个元素代表数组的值，第二个元素代表了该值出现的次数
        PriorityQueue<int[]> queue = new PriorityQueue<int[]>(new Comparator<int[]>() {
            public int compare(int[] m, int[] n) {
                return m[1] - n[1];
            }
        });
        for (Map.Entry<Integer, Integer> entry : occurrences.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            // 维护前k个高频数组元素
            if (queue.size() == k) {
                if (queue.peek()[1] < count) {
                    queue.poll();
                    queue.offer(new int[]{num, count});
                }
            } else {
                queue.offer(new int[]{num, count});
            }
        }
        int[] ret = new int[k];
        for (int i = 0; i < k; ++i) {
            ret[i] = queue.poll()[0];
        }
        return ret;
    }
}
```

- [题解](https://www.bilibili.com/video/BV1Xg41167Lz?vd_source=7341c7fca3b496e9108bb1fd49c634ef)
- 利用小根堆的性质，堆顶一定是当前 k 个元素最小值来判断
### [数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)

```java
class MedianFinder {
    private final PriorityQueue<Integer> left = new PriorityQueue<>((a, b) -> b - a); // 最大堆
    private final PriorityQueue<Integer> right = new PriorityQueue<>(); // 最小堆

    public void addNum(int num) {
        if (left.size() == right.size()) {
            right.offer(num);
            left.offer(right.poll());
        } else {
            left.offer(num);
            right.offer(left.poll());
        }
    }

    public double findMedian() {
        if (left.size() > right.size()) {
            return left.peek();
        }
        return (left.peek() + right.peek()) / 2.0;
    }
}
```

- [题解](https://leetcode.cn/problems/find-median-from-data-stream/solutions/3015873/ru-he-zi-ran-yin-ru-da-xiao-dui-jian-ji-4v22k/?envType=study-plan-v2&envId=top-100-liked)
- 大根堆+小根堆
## 贪心
### [买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        int n = prices.length;
        int max = prices[n - 1];
        for(int i = n - 1; i >= 0; i--) {
            if(prices[i] < max) {
                res = Math.max(res,max - prices[i]);
            }else{
                max = prices[i];
            }
        }
        return res;
    }
}
```

- 当天买入的最佳利润应该等后续股票价格的最大值减去当天的价格，也就是 $max - prices[i]$，所以维护 i 之后的股票价格即可
### [跳跃游戏](https://leetcode.cn/problems/jump-game/)

```java
class Solution {
    public boolean canJump(int[] nums) {
        int n = nums.length;
        int idx = n - 1;
        for(int i = n - 2; i >= 0; i --) {
            if(i + nums[i] >= idx) {
                idx = i;
            }
        }
        return idx == 0;
    }
}
```

- 从后向前遍历，如果当前位置移动的最远距离大于等于目标位置（idx），则能否从 0 到 idx 变为了求从 0 到 i
### [跳跃游戏 II](https://leetcode.cn/problems/jump-game-ii/)

```java
class Solution {
    public int jump(int[] nums) {
        int n = nums.length;
        int t = n - 1;
        int res = 0;
        for(int i = 0; i < n-1; i++) {
            if(i + nums[i] >= t) {
                t = i;
                res ++;
                i = -1;
            }
            if(t == 0) {
                break;
            }
        }
        return res;
    }
}
```

- 每次都从头走，找到可以到达 t 的最近 i，直到 i = 0。
### [划分字母区间](https://leetcode.cn/problems/partition-labels/)

```java
class Solution {
    public List<Integer> partitionLabels(String S) {
        char[] s = S.toCharArray();
        int n = s.length;
        int[] last = new int[26];
        for (int i = 0; i < n; i++) {
            last[s[i] - 'a'] = i; // 每个字母最后出现的下标
        }

        List<Integer> ans = new ArrayList<>();
        int start = 0, end = 0;
        for (int i = 0; i < n; i++) {
            end = Math.max(end, last[s[i] - 'a']); // 更新当前区间右端点的最大值
            if (end == i) { // 当前区间合并完毕
                ans.add(end - start + 1); // 区间长度加入答案
                start = i + 1; // 下一个区间的左端点
            }
        }
        return ans;
    }
}

// 同样的方法，不过这个思路简单

class Solution {
    public List<Integer> partitionLabels(String s) {
         List<Integer> res = new ArrayList<>();
         int n = s.length();
         int next = -1;
         int last = 0;
         for(int i = 0; i < n; i ++) {
            char c = s.charAt(i);
            next = Math.max(next,s.lastIndexOf(c));
            if(i == next) {
                res.add(next - last + 1);
                last = next + 1;
                next = -1;
            }
        }
        return res;
    }
}
```

- [题解](https://leetcode.cn/problems/partition-labels/solutions/2806706/ben-zhi-shi-he-bing-qu-jian-jian-ji-xie-ygsn8/?envType=study-plan-v2&envId=top-100-liked)
- 合并区间
- 第二个方法，next 是我们字符放的最远的位置， last 是划分字符串之前的位置；当 i = next 时，说明后面不再会出现前面的相同字符。第一个相当于优化了 `s.lastIndexOf(c)` 求解。
## 动态规划

### 背包问题

DFS -> 记忆化搜索 -> 递推（将 DFS 1:1 翻译） -> 空间优化。

如何确定递归边界、递归入口等。

关键中在于 $dfs(i,v) = Math.max(dfs(i-1,v),dfs(i-1,v-nums[i]) + value[i])$ 这个转移方程。

完全背包：$dfs(i,v) = Math.max(dfs(i-1,v),dfs(i,v-nums[i]) + value[i])$ 。

### [爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

```java
class Solution {
    public int climbStairs(int n) {
        if (n == 0) {
            return 0; // 或者根据题目定义，如果 n=0 算作 1 种方法（不走）
        }
        if (n == 1) {
            return 1;
        }
        // dp[i] 存储到达第 i 级台阶的方法数
        int[] dp = new int[n + 1];
        // 基本情况
        dp[0] = 1; // 理论上到达第0级台阶只有一种方法（不走），方便计算dp[2]
        dp[1] = 1; // 到达第1级台阶只有一种方法 (1步)
        // 从第2级台阶开始计算
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n];
    }
}
```

- 斐波那契数列
### [杨辉三角](https://leetcode.cn/problems/pascals-triangle/)

```java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> res = new ArrayList<>(numRows);
        res.add(List.of(1));
        for(int i = 1; i < numRows; i++) {
            List<Integer> list = new ArrayList<>(i+1);
            list.add(1);
            for(int j = 1; j < i; j++) {
                // 左上方的数 + 正上方的数
                list.add(res.get(i-1).get(j) + res.get(i-1).get(j-1));
            }
            list.add(1);
            res.add(list);
        }
        return res;
    }
}
```

- 理解等式：$c[i][j]=c[i−1][j−1]+c[i−1][j]$，与 $(a+b)^n$ 求组合数系数一致。
### [打家劫舍](https://leetcode.cn/problems/house-robber/)

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        // dp[i] 表示当天“抢和不抢”的最大利益
        // 转移方程: 当天抢：dp[i] = nums[i] + dp[i+2]; 不抢 dp[i] = dp[i+1];
        int[] dp = new int[n+1];
        dp[n-1] = nums[n-1];
        for(int i = n-2; i >= 0; i--) {
            dp[i] = Math.max(nums[i] + dp[i+2],dp[i+1]);
        }
        return dp[0];
    }
}
```

