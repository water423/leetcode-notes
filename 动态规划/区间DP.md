## 区间DP

1. 常见题型与思路
   1. 题型：通常在解析题意之后会出现利用子区间的值去计算父区间的值的情况，则可以使用区间dp求解。

   2. 思路：区间dp中，通常对于父区间会通过枚举分割点来转化成用子区间结果计算父区间结果的问题，常见模版如下：

      ```java
      int[][] dp = new int[n.length][n.length];
      //对应初始化：一般是区间中只有一个元素的情形
      //开始枚举：区间长度->左端点/右端点->分割点->递推公式
      for(int len = 2; len <= n.length; len++){ //至少有一个有效数字
          for(int i = 0; i + len -1 < n.length; i++){ //左边界
              int j = i + len -1; //右边界
              for(int k = i+1; k < j; k++){ //分割点
              }
          }
      }
      return dp[0][n.length-1]; //原始问题对应区间
      ```
   
      
   
2. AcWing 282 石子合并
   1. 题目：设有 N 堆石子排成一排，其编号为 1,2,3,…,N。每堆石子有一定的质量，可以用一个整数来描述，现在要将这 N 堆石子合并成为一堆。每次只能合并相邻的两堆，合并的代价为这两堆石子的质量之和，合并后与这两堆石子相邻的石子将和新堆相邻，合并时由于选择的顺序不同，合并的总代价也不相同。

      例如有 4 堆石子分别为 `1 3 5 2`， 我们可以先合并 1、2 堆，代价为 4，得到 `4 5 2`， 又合并 1、2 堆，代价为 9，得到 `9 2` ，再合并得到 11，总代价为 4+9+11=24；如果第二步是先合并 2、3 堆，则代价为 7，得到 `4 7`，最后一次合并代价为 11，总代价为 4+7+11=22。

      问题是：找出一种合理的方法，使总的代价最小，输出最小代价。

   2. 范围：`1≤N≤300`

   3. 思路：用 $f[i][j]$ 表示将第 i 堆到第 j 堆石子进行合并的总代价的最小值，则集合划分可以按照先合并前面k堆[i,k]，再合并后面的部分 $[k+1,j]$ ，k可以是  $ i,i+1 .... j-1$ （将 k 理解成 i 到 j 之间的一个点，且不能为 i 或者 j ），最后一定是将左边的一堆和右边的一堆进行合并，即N堆石子的总重量之和（可以用前缀和来计算），最终的状态转移方程如下： 
   
      * $f[i][j] = min( f[i][k] + f[k+1][j] ) + (S[j] - S[i-1])$
   
   4. 代码：
   
      ```java
      private static int N = 310;
      private static int n; //n堆石子
      private static int[] S;   //前i堆石子的重量(在f[i][j]的计算中用到)
      
      private static void dp(){
          int[][] f = new int[n+1][n+1];
          //需要初始化f[i][j],否则取min将会得到0
          for (int i = 0 ; i <= n ; i++){  //i的范围
              Arrays.fill(dp[i], 0x3f3f3f3f);
          }
          //当i=j即只有一堆时，f[i][j] = 0 因为不需要移动
          for (int i = 1 ; i <= n ; i++) {  //i的范围
              f[i][i] = 0; //（！！不是f[i][i] = a[i]！！）
          }
          for(int len = 2 ; len <= n ; len++){   //先枚举区间长度,至少为2
              for(int i = 1 ; i + len - 1 <= n ;i++){  
                  int j = i + len - 1;
                  for(int k = i; k <= j-1 ; k++){   
                      //再枚举区间中的分点k,[i,k]包含第k堆，且必须保证左右至少有一堆
                      f[i][j] = Math.min(f[i][j], f[i][k]+f[k+1][j]+S[j]-S[i-1]);
                  }
              }
          }
          //目标是：f[1][n]，将第一堆到第n堆合并的结果
          System.out.println(f[1][n]);
      }
      
      public static void main(String[] args) throws IOException {
          BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
          n = Integer.parseInt(input.readLine());
          //读取n堆石子,同时计算前缀和
          int[] a = new int[n+1];  //下标从1开始（要计算前缀和）
          S = new int[n+1];
          S[0] = 0;  //第0位为0
          String[] param = input.readLine().split(" ");
          for (int i = 1; i <= n ; i++){
              a[i] = Integer.parseInt(param[i-1]);
              S[i] = S[i-1] + a[i];
          }
          dp();
      }
      ```
   
      
   
3. leetcode 241 为运算表达式设置优先级
   1. 题目：给你一个由数字和运算符组成的字符串 `expression` ，按不同优先级组合数字和运算符，计算并返回所有可能组合的结果。你可以 **按任意顺序** 返回答案。生成的测试用例满足其对应输出值符合 32 位整数范围，不同结果的数量不超过 `104` 。

   2. 范围：`1 <= expression.length <= 20`   `expression` 由数字和算符 `'+'`、`'-'` 和 `'*'` 组成。 输入表达式中的所有整数值在范围 `[0, 99]` 

   3. 代码：

      ```java
      int[] numbers;
      char[] ops;
      List<Integer>[][] dp;
      
      public List<Integer> diffWaysToCompute(String expression) {
          //整理表达式，分离操作数和操作符号
          char[] exp = expression.toCharArray();
          numbers = new int[expression.length()/2+1];
          ops = new char[expression.length()/2];
          int a = 0;   int b = 0; 
          for(int i = 0; i < expression.length(); ){
              if(!Character.isDigit(exp[i])){
                  ops[b] = exp[i];i++; b++;
              }else{
                  if(i+1 < expression.length() && 
                     Character.isDigit(exp[i]) && Character.isDigit(exp[i+1])){
                      int num = (exp[i] - '0')*10 + (exp[i+1] - '0');
                      numbers[a] = num;a++;i+=2;
                  }else{
                      numbers[a] = exp[i] - '0';a++;i++;
                  }
              }
          }
          int number_length = a;
          dp = new List[number_length][number_length];
          for(int i = 0; i < number_length; i++){  //初始化
              for(int j = 0; j < number_length; j++){
                  dp[i][j] = new ArrayList<>();
                  if(i == j){dp[i][j].add(numbers[i]);}
              }
          }
          for(int len = 2; len <= number_length; len++){
              for(int l = 0; l + len - 1 < number_length; l++){
                  int r = l + len -1;
                  for(int k = l; k < r; k++){ //遍历操作符
                      for(Integer op1 : dp[l][k]){
                          for(Integer op2 : dp[k+1][r]){
                              char op = ops[k]; 
                              int res = 0;
                              if(op == '-'){res = op1 - op2;}
                              else if(op == '+'){res = op1 + op2;}
                              else{res = op1 * op2;}
                              dp[l][r].add(res);
                          }
                      }
                  }
              }
          }
          return dp[0][number_length-1];
      }
      ```
      
      

4. leetcode 312 戳气球

   1. 题目：有 `n` 个气球，编号为`0` 到 `n - 1`，每个气球上都标有一个数字，这些数字存在数组 `nums` 中。现在要求你戳破所有的气球。戳破第 `i` 个气球，你可以获得 `nums[i - 1] * nums[i] * nums[i + 1]` 枚硬币。 这里的 `i - 1` 和 `i + 1` 代表和 `i` 相邻的两个气球的序号。如果 `i - 1`或 `i + 1` 超出了数组的边界，那么就当它是一个数字为 `1` 的气球。求所能获得硬币的最大数量。

   2. 范围：`n == nums.length`  `1 <= n <= 300` `0 <= nums[i] <= 100`

   3. 思路：$dp[i][j]$ 表示戳破 $(i,j)$ 之间的所有气球所能得到的最大的硬币数，因为每次戳气球与左右两边的硬币数相关，因此 $(i,j)$ 代表开区间来便于处理。 **状态划分是根据其中按照最后戳破哪个气球**，假设是 $index=k$ 的节点则 有如下状态转移方程：

      * $dp[i][j] = max(dp[i][j], dp[i][k]+dp[k][j]+nums[k]*nums[i]*nums[j])$

      **由于 $k$ 是最后一个戳破的，则对于两个子dp而言其相当于是边界，是固定的。**并且需要用两个长度更小的子区间去计算大区间，可以考虑**区间dp**。

   4. 代码：

      ```java
      public int maxCoins(int[] nums) {
        int[] n = new int[nums.length+2];
        n[0]=1; n[n.length-1]=1;
        for(int i = 1; i <= n.length-2; i++){n[i]=nums[i-1];}
        int[][] dp = new int[n.length][n.length];
        for(int len = 2; len <= n.length; len++){ //至少有一个有效数字
            for(int i = 0; i + len -1 < n.length; i++){ //左边界
                int j = i + len -1; //右边界
                for(int k = i+1; k < j; k++){ //枚举最后一个戳破的气球，不能是边界
                    //若区间内只有两个数字则可以使用默认值0
                    dp[i][j] = Math.max(dp[i][j], 
                      dp[i][k]+dp[k][j]+n[k]*n[i]*n[j]);
                }
            }
        }
        return dp[0][n.length-1]; //使用一开始插入的两个边界来进行计算
      }
      ```

      

5. leetcode 375 猜数字大小II

   1. 题目：我们正在玩一个猜数游戏，游戏规则如下：我从 `1` 到 `n` 之间选择一个数字。你来猜我选了哪个数字。如果你猜到正确的数字，就会 **赢得游戏** 。如果你猜错了，那么我会告诉你，我选的数字比你的 **更大或者更小** ，并且你需要继续猜数。每当你猜了数字 `x` 并且猜错了的时候，你需要支付金额为 `x` 的现金。如果你花光了钱，就会 **输掉游戏** 。给你一个特定的数字 `n` ，返回能够 **确保你获胜** 的最小现金数，**不管我选择那个数字** 。
   
   2. 范围：`1 <= n <= 200`

   3. 思路：即对于1<=x<=n，选择x作为第一个猜测的数，并以此为根建立二叉树，相当于求整个二叉树中除了叶子节点之外的路径最大值**（确保获胜）**，并对多个x求最小值来达到**最小现金数**。$dp[i][j]$ 表示区间$ [i,j]$ 对应猜中其中每一个数字的花费最小值，选择其中的数字k作为root，即第一次猜测的数字，若一下子就猜中了则花费是0忽略不计，若没有猜中，则根据目标值可能进入左右区间的任意一个并且需要支付当前数字对应的金额。对应的状态转移方程如下：
   
      * $dp[i][j] = min(dp[i][j], max(dp[i][k-1], dp[k+1][j]+nums[k]))$。
   
      同样是使用区间dp来用小区间计算大区间的值。
   
   4. 代码：
   
      ```java
      public int getMoneyAmount(int n) {
          int[][] dp = new int[n+2][n+2];  //1~n 前面空一格 后面空一格
          for(int i = 0; i <= n; i++){
              Arrays.fill(dp[i], 0x3f3f3f3f);
              for(int j = 0; j <= i; j++){
                  dp[i][j] = 0; //若区间中只有一个数或没有数，不需要支付
              }
          }
          for(int len = 2; len <= n; len++){
              for(int l = 1; l+len-1 <= n; l++ ){
                  int r = l+len-1;
                  for(int k = l; k <= r; k++){ //枚举root即猜测点
                      dp[l][r] = Math.min(dp[l][r], Math.max(dp[l][k-1], dp[k+1][r])+k);
                  }
              }
          }
          return dp[1][n];
      
      }
      ```
   
      
   
   