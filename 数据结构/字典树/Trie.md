## Trie树



1. Trie树模版











### 利用Tire完成字典序排序

2. leetcode 386 字典序排数 

   1. 题目：给你一个整数 `n` ，按字典序返回范围 `[1, n]` 内所有整数。你必须设计一个时间复杂度为 `O(n)` 且使用 `O(1)` 额外空间的算法。

   2. 范围：`1 <= n <= 5 * 104`

   3. 代码：

      ```java
      List<Integer> res = new ArrayList<>();
      int n;
      public List<Integer> lexicalOrder(int n) {
          //给定一个集合的数字 将其按照字典序排序
          //则可以尝试构建一棵Trie树 则从root开始向下dfs搜索到的每一个end标签代表的数字即是结果
          //由于本题中是连续的数字，因此可以抽象Trie树 不需要真正的建树
          this.n = n;
          //枚举起点 不能为0
          for(int i = 1; i <= 9; i++){ dfs(i); }
          return res;
      }
      
      //对于curr尝试在其末尾按照字典序添加一位
      public void dfs(int curr){
          if(curr <= n) res.add(curr);
          else return;
          for(int i = 0; i <= 9; i++){
              dfs(curr*10+i);
          }
      }
      ```

      