## 水塘抽样

1. 水塘抽样
   1. 适用场景：**总的样本数量未知，从所有样本中抽取若干个，要求每个样本被抽到的概率相等**。
   2. 具体做法：从前往后处理每个样本，每个样本成为答案的概率为 `1/i`，其中 i 为样本编号（编号从 1 开始），最终可以确保每个样本成为答案的概率均为 `1/n` （其中 nnn 为样本总数）
   3. 证明：假设最终成为答案的样本编号为 k，那么 k 成为答案的充要条件为 在遍历到 k 时被选中」并且「遍历大于 k 的所有元素时，均没有被选择（没有覆盖 k）；即为 $P=k/1 ∗ (k/k+1)∗(k+1/k+2)∗...∗(n-1/n) $  等于 `1/n`



2. leetcode 382 链表随机节点

   1. 题目：给你一个单链表，随机选择链表的一个节点，并返回相应的节点值。每个节点 **被选中的概率一样** 。

   2. 范围：链表中的节点数在范围 `[1, 104]` 内; `-104 <= Node.val <= 104`; 至多调用 `getRandom` 方法 `104` 次

   3. 代码：

      ```java
      ListNode head;
      Random r = new Random();
      public Solution(ListNode head) {
          this.head = head;
      }
      
      public int getRandom() {
          int ans = 0;
          int range = 1; //第一个节点以1的概率被选到
          ListNode tmp = head;
          while(tmp != null){
              if(r.nextInt(range) == 0) ans = tmp.val; //1/k
              tmp = tmp.next;
              range++;
          }
          return ans;
      }
      ```

      

## 洗牌算法

1. 洗牌算法
   1. 适用场景：共有 n 个不同的数，根据每个位置能够选择什么数，共有 n! 种组合。洗牌算法可以等概率返回某一种组合。
   2. 做法：对于下标 x 而言，从 [x,n−1] 中随机出一个位置与 x 进行值交换，当所有位置都进行这样的处理后，便得到了一个公平的洗牌方案。



2. leetcode 384 打乱数组

   1. 题目：给你一个整数数组 `nums` ，设计算法来打乱一个没有重复元素的数组。打乱后，数组的所有排列应该是 **等可能** 的。

   2. 范围：`1 <= nums.length <= 50`  `-106 <= nums[i] <= 106`   `nums` 中的所有元素都是 **唯一的**    最多可以调用 `104` 次 `reset` 和 `shuffle`

   3. 代码：

      ```java
      int[] nums;
      Random r;
      public Solution(int[] nums) {
          this.nums = nums;
          r = new Random();
      }
      
      public int[] reset() {
          return nums;
      }
      
      public int[] shuffle() {
          int[] ans = nums.clone();
          for(int i = 0; i < ans.length; i++){
              swap(ans, i, i+r.nextInt(nums.length-i));
          }
          return ans;
      }
      
      public void swap(int[] nums, int i, int j){
          int tmp = nums[i];
          nums[i] = nums[j];
          nums[j] = tmp;
      }
      ```

      