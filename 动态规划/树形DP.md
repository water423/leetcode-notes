## 树形DP

1. 常见题型与思路

   1. 题型：树形DP一般会比较明显地给出树的结构（如二叉树等），并且会涉及到父节点与相关子节点无法同时选择/父节点与子节点的状态会相互影响，从而在题目的限制条件下求最大值/最小值等。

   2. 思路：设定目的是**求以某个节点为根节点时在整个子树满足条件时的目标最大值/最小值等**，**从父节点和子节点的相互影响角度出发，判断每个节点一共会出现几个状态以及对于root节点这几个状态之间是如何转移的并影响目标值的，从而推导出状态转移方程（根据方向来进行划分）。**树形DP的模版是在dfs的过程中，用子节点的dfs结果来计算父节点的值，也可以利用父节点来计算当前节点的值，**需要综合考虑递归与回溯的顺序，如单纯利用子节点来计算当前节点的值则应该采用最常见的后序遍历；对于某些情况会同时影响父节点和子节点可以考虑利用状态划分来避免双重影响**。

      

2. leetcode 337 打家劫舍III **（树形DP）**

   1. 题目：小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 `root` 。除了 `root` 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果 **两个直接相连的房子在同一天晚上被打劫** ，房屋将自动报警。给定二叉树的 `root` 。返回 ***在不触动警报的情况下** ，小偷能够盗取的最高金额* 。

   2. 范围： 树的节点数在 `[1, 104]` 范围内；`0 <= Node.val <= 104`

   3. 思路：以子树为粒度进行分析，其根为root，dp[0]表示root不被偷时整个子树可以被偷的最大金额；dp[1]表示root被偷时整个子树可以被偷的最大金额，这两个值需要使用其子树的对应返回值进行计算，因此**树形DP存在于dfs对每个子树的过程中**。

      * $dp[0] = max(leftdp[0],leftdp[1]) + max(rightdp[0], rightdp[1])$
      * $dp[1] = leftdp[0] + leftdp[0] + value[root]$

   4. 代码：

      ```java
      public int rob(TreeNode root) {
          int[] res = dfs(root);
          return Math.max(res[0], res[1]);
      }
      
      public int[] dfs(TreeNode root){
          if(root == null){return new int[2];}  //递归返回条件
          //此处需要定义两个状态（抢劫root/不抢劫root）
          //而不是只返回可以抢夺的最大金额的原因是：
          //无法仅仅通过左右子树的最大金额和隐含的if条件语句推出结果
          int not_rob = 0;
          int do_rob = root.val;
          int[] l_res = dfs(root.left);
          int[] r_res = dfs(root.right);
          not_rob += Math.max(l_res[0], l_res[1]) + Math.max(r_res[0], r_res[1]);
          do_rob += l_res[0] + r_res[0];
          return new int[]{not_rob, do_rob};
      }
      ```



3. Leetcode 968 监控二叉树

   1. 题目：给定一个二叉树，我们在树的节点上安装摄像头。节点上的每个摄影头都可以监视**其父对象、自身及其直接子对象。**计算监控树的所有节点所需的最小摄像头数量。

   2. 范围：给定树的节点数的范围是 `[1, 1000]`；每个节点的值都是 0。

      1. 思路：根据题意，目标即为**求当以root为子树的根节点时整个子树均被监控需要的摄像头最小值**。对于每个节点，其状态可以分为三类：（1）当前节点自身装摄像头；（2）当前节点不装摄像头但是处于监控状态；（3）当前节点不装摄像头且未被监控。然而，由于目标是子树中所有节点均处理监控状态，因此（3）删除。其中，由于每个节点可以被父节点和子节点同时影响，因此，状态（2）可以细分成 **当前节点不装摄像头但是父节点装了摄像头、 当前节点不装摄像头但是其中一个子节点装了摄像头** 从而满足其处于监控状态。根据以上的状态定义和目标来推断状态转移（由子节点的状态及对应值来推断父节点）：

         * （1）若当前节点已经安装了摄像头，则子节点处于（1）安装了摄像头 （2）[1]未安装摄像头但父节点安装了摄像头；每个子节点对于这两种状态取最小值后相加，还需要加上当前节点安装的这一个摄像头。
         * （2）[1] 若当前节点不装摄像头但是父节点装了摄像头，则任意一个子节点应该处于 （1）安装摄像头 或 （2）[2]当前节点不装摄像头但是其中一个子节点装了摄像头 状态；每个子节点对于这两种状态取最小值后相加。
         * （2）[2]若当前节点不装摄像头但是其中一个子节点装了摄像头，则两个子节点应该为 [1] 一个处于（1）安装摄像头，另 一个处于（2）[2]未安装摄像头但子节点安装摄像头的状态，对于这两种情况将所有子节点结果求和后取最小值；**[2]两个都安装了摄像头。（由于无法判断每个子树在各个状态下的总体情况，因此不能认为两个都装的结果一定是大于一个装一个不装的，具体取决于树的结构）**

         则综合以上信息，将三个状态对应到当前节点的状态数组dp[3]中，可以写出如下的状态转移方程：

         * $dp[0] = min(dp_(left)[0], dp_(left)[1]) + min(dp_(right)[0], dp_(right)[1]) +1$​
         * $dp[1] = min(dp_(left)[0], dp_(left)[2] ) + min( min(dp_(right)[0], dp_(right)[2] ) $​
         * $dp[2] = min(dp_(left)[0]+dp_(right)[2] , dp_(left)[2]+dp_(right)[0])$​

         在递推公式的基础之上，进一步明确初始化条件，由于树形DP是由子节点到父节点的推导过程，即对于空节点(null)的情况，根据状态定义，空节点无法安装摄像头，**dp[0]=inf表示不合法（实际代码中为$Integer.MAX_VALUE/2$，防止加法溢出）**，并且空节点不需要被监控，因此dp[1]=dp[2]=0；则对于叶子节点。**将dp[0]定义为无穷大的主要目的是对于dp[2]若一个节点其没有子节点则dp[2]状态应该也是不合法的**。

         最后，整个方法的入口为 $dp(root)[3] = dfs(root)$，而root没有父亲节点，因此最后的返回值应该是 $min(dp[0], dp[2])$

   3. 代码：

      ```java
      public int minCameraCover(TreeNode root) {
          int[] res = dfs(root);
          return Math.min(res[0], res[2]);
      }
      
      public int[] dfs(TreeNode root){
          if(root == null){
              return new int[]{Integer.MAX_VALUE/2, 0, 0};
          }
          int[] l = dfs(root.left);
          int[] r = dfs(root.right);
          int self = Math.min(l[0], l[1]) + Math.min(r[0], r[1]) +1;
          int by_parent = Math.min(l[0], l[2]) + Math.min(r[0], r[2]);
          int by_children = Math.min(Math.min(l[0]+r[2], l[2]+r[0]), l[0]+r[0]);
          return new int[]{self, by_parent, by_children};
      }
      ```

   4. 进阶：

      * 如果将这道题设定为 在节点 $x$ 安装摄像头需要花费 $cost[x]$ ，求监控所有树的最小花费：本题求的是最小个数，其实就相当于是所有摄像头的安装费用相等且都是1，则若改成 $cost[x]$ ，则对应只需要将 $dp[0]$ 的状态转移方程中的 +1 改成 + $cost[x]$ 即可
      * 如果每个节点不止有两个子节点，而是有多个，应该如何化简：\



4. Leetcode 124 二叉树中的最大路径和

   1. 题目：二叉树中的 **路径** 被定义为一条节点序列，序列中每对相邻节点之间都存在一条边。同一个节点在一条路径序列中 **至多出现一次** 。该路径 **至少包含一个** 节点，且不一定经过根节点。**路径和** 是路径中各节点值的总和。给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。

   2. 范围：树中节点数目范围是 `[1, 3 * 104]`  `-1000 <= Node.val <= 1000`

   3. 思路：此题相较于树形dp，更类似于dfs，重点在于区分在递归与回溯的过程中不断判断最长的路径是否出现在当前子树中，并且区分最终结果与每层递归返回值之间的区别。

   4. 代码：注意如何在递归过程中处理root

      ```java
      int result = Integer.MIN_VALUE;
      
      public int maxPathSum(TreeNode root) {
          dfs(root);
          return result;
      }
      
      //返回当前子树中以root为起点的最大路径和
      //排除 a -> root -> b的情形，是以root为起点的路径
      public int dfs(TreeNode root){
          if(root == null){return Integer.MIN_VALUE;}
          //子树中包含root的路径一共有三种
          //1. root - left/right - ... 仅涉及一颗子树
          //2. .. - left - root - right - ... 涉及两颗子树
          //只有当子树对应的最大路径和为正数时才选择，否则不选择
          int left_max = Math.max(dfs(root.left), 0);
          int right_max = Math.max(dfs(root.right), 0);
          int child_max = Math.max(left_max, right_max);
          //情形1:以root为起点的线性最大值即为左右子树的最大值+root本身
          //需要注意的是：由于本题中含有负数，并且路径中至少包含一个节点即为root
          //此即为返回值，并且也需要判断是否能成为最值
          int tmp_max = root.val + child_max;
          result = Math.max(result, tmp_max);
          //情况3:判断是不是可能是最值，不返回
          result = Math.max(result, left_max + right_max + root.val);
          return tmp_max;
      }
      ```

   5. 相似题目：leetcode 543 二叉树的直径：同样是返回二叉树中以root为根的子树的最大深度，并在dfs过程中考虑以root为中转点的直径是否会是最大值。

   

5. leetcode 310 最小高度树

   1. 题目：树是一个无向图，其中任何两个顶点只通过一条路径连接。 换句话说，一个任何没有简单环路的连通图都是一棵树。给你一棵包含 `n` 个节点的树，标记为 `0` 到 `n - 1` 。给定数字 `n` 和一个有 `n - 1` 条无向边的 `edges` 列表（每一个边都是一对标签），其中 `edges[i] = [ai, bi]` 表示树中节点 `ai` 和 `bi`之间存在一条无向边。

      可选择树中任何一个节点作为根。当选择节点 `x` 作为根节点时，设结果树的高度为 `h` 。在所有可能的树中，具有最小高度的树（即，`min(h)`）被称为 **最小高度树** 。请你找到所有的 **最小高度树** 并按 **任意顺序** 返回它们的根节点标签列表。树的 **高度** 是指根节点和叶子节点之间最长向下路径上边的数量。

   2. 范围：`1 <= n <= 2 * 104` `edges.length == n - 1` 

      ​             `0 <= ai, bi < n`    `ai != bi`    所有 `(ai, bi)` 互不相同

      ​             给定的输入 **保证** 是一棵树，并且 **不会有重复的边**

   3. 思路：暴力做法是对于每一个节点尝试以其作为根节点用dfs/bfs（**用bfs的原因是bfs适用于求最短距离**）的方式求最小高度并记录，最终筛选得到高度最小的节点，但是暴力做法存在很多重复的计算，因此考虑如何在一次遍历的过程中就得到所有的结果，考虑利用树形dp。**树形dp的常见思考方式是根据方向进行划分（用子节点计算当前节点的值或用父节点来计算当前节点的值），并且在同时考虑递归与回溯的顺序。**在递归过程中某次 $dfs$ 必然是选择一个节点 $root$ 来作为入口，返回值以是当前节点 $root$ 为树根时的**最大高度**（本题要求的是所有最大高度的最小值），同时记录在$dp[root]$ 中。此 $root$不为整次遍历的起点时，必然会存在一个 $rootParent$ 节点，**在 $dfs(rootParent)$ 的过程中递归到了 $dfs(root)$ ，则除了此节点外，与 $root$ 相邻的其他节点均可以视作 $root$ 的 $child$ ，即为 $dfs(root)$ 的过程中 $dfs(child_x)$**。则$dp[root]$受到$dp[rootParent]$和$dp[child_x]$的影响，其中，**即使$rootParent$是$root$在递归上的父亲节点，但在将$root$视作树根并求最大高度的时候$rootParent$也是其$child$**，只是由于递归与回溯顺序需要额外考虑。则可以划分为以下两种状态，对这两种状态求最大值即为 $root$ 对应的最大高度值：

      * （1）最大高度值在其某个子树中取到 $dp[root]-1 = max(dp[child_x])$
      * （2）最大高度值在父节点中取到，此时又分为两种情况，
        * 一种是**父节点的最大高度值在父亲节点的父节点中取到**
        * 一种是**父节点的最大高度值在父节点的子节点中取到**，此时由于在以$rootParent$为树根进行动态规划求最小高度时也会将$dfs(root)$的返回值视作其$min(dp[child_x])$的部分，因此**在再次利用$rootParent$的最大高度值推断$root$的最大高度值时需要额外考虑$rootParent$的最大高度值是否就是在子树为$root$时取到，若是此处取到则$dfs(rootParent)$​的返回值应该为其子树最大高度的次大值。**
   
      此外，需要明确的是，**对于情况（1）的更新顺序是由下往上，用子节点计算父节点，对于情况（2）其实是计算当前节点的父亲节点的最大高度值，更新顺序是自上而下的**。因此肯定在一次dfs的过程中实现，因此可以首先预处理出在以0号节点为根节点时，每个节点向下的最大高度值和次大高度值及对应的节点，在dfs求情况（2），最终得到答案。
   
   4. 代码：
   
      ```java
      int idx = 0;  //下一条被插入边的id
      int[] e;  //当前边对应的终点节点
      int[] ne; //当前边对应的下一条边
      int[] h;  //头节点对应的边的idx
      int[][] subtree_height; //[0]最大高度[1]最大高度对应节点[2]次大高度
      int[] parent_height;  //表示最大向上高度
      
      public List<Integer> findMinHeightTrees(int n, int[][] edges) {
          //构图
          h = new int[n]; //N个节点
          ne = new int[2*n]; 
          e = new int[2*n];
          Arrays.fill(h, -1);
          for(int i = 0; i < n-1; i++){ //无向图
              add(edges[i][0], edges[i][1]);
              add(edges[i][1], edges[i][0]);
          }
          //dp+dfs
          subtree_height = new int[n][3];
          parent_height = new int[n];
          dfs_down(0, -1);
          dfs_up(0, -1);
          //获取结果
          List<Integer> ans = new ArrayList<>();
          int min = n;
          //获得最大列表
          for (int i = 0; i < n; i++) {
              int cur = Math.max(subtree_height[i][0], parent_height[i]);
              if (cur < min) {
                  min = cur;
                  ans.clear();
                  ans.add(i);
              } else if (cur == min) {
                  ans.add(i);
              }
          }
          return ans;
      }
      
      //数组的链式结构存储图 点a->点b的边是idx
      public void add(int a, int b){
          e[idx] = b;
          ne[idx] = h[a];
          h[a] = idx++;
      }
      
      //向下遍历，自下而上更新，记录每个节点为root时的最大/次大子树高度即对应的节点
      public int dfs_down(int root, int root_parent){
          int max = 0;
          int max_node = -1;
          int second_max = 0;
          for(int edge = h[root]; edge != -1; edge = ne[edge]){
              if(e[edge] == root_parent){continue;} //防止向上走
              int child_height = dfs_down(e[edge], root);
              if(child_height >= max){
                  second_max = max;    //max变成second
                  max = child_height;  //新的max
                  max_node = e[edge];  //若有相同的也可以随便更新哪一个
              }else if(child_height > second_max){ //比second大
                  second_max = child_height;
              }
          }
          subtree_height[root] = new int[]{max+1, max_node, second_max+1};
      
          return max+1; //算上自己的高度
      }
      
      //向下递归，自上而下更新，记录每个节点的父节点对应的那一棵子树的最大高度值
      public void dfs_up(int root, int root_parent){
          for(int edge = h[root]; edge != -1; edge = ne[edge]){
              if(e[edge] == root_parent){continue;}
              //对于child而言保证root的每一个出边都被遍历到
              //可能1:当前子节点的最大parent高度由parent的子节点转移
              //判断父亲节点的子树的最大值是否是当前节点
              if(subtree_height[root][1] == e[edge]){ 
                  //则当前子节点向上的最大高度需要取root的次大值，不能取到自身
                  parent_height[e[edge]] = Math.max(parent_height[e[edge]], subtree_height[root][2]+1);
              }else{
                  //则当前节点向上的最大高度可以是最大值
                  parent_height[e[edge]] = Math.max(parent_height[e[edge]], subtree_height[root][0]+1);
              }
              //可能2:当前子节点的最大parent高度由parent的parent转移
              parent_height[e[edge]] = Math.max(parent_height[e[edge]], parent_height[root]+1);
              //继续递归整棵树
              dfs_up(e[edge], root);
          }
      }
      ```
   
      5. 进阶：本题也可以利用bfs（拓扑排序）快速解决；或使用算法分析得到**图中相距离最远的两个点之间路径的中点即为解（？）**。
   
      ```java
      
      public List<Integer> findMinHeightTrees(int n, int[][] edges) {
          List<Integer> ans = new ArrayList<Integer>();
          if (n == 1) { ans.add(0); return ans;}
          int[] degree = new int[n];
          List<Integer>[] adj = new List[n];
          for (int i = 0; i < n; i++) {
              adj[i] = new ArrayList<Integer>();
          }
          for (int[] edge : edges) {
              adj[edge[0]].add(edge[1]); degree[edge[0]]++;
              adj[edge[1]].add(edge[0]); degree[edge[1]]++;
          }
          Queue<Integer> queue = new ArrayDeque<Integer>();
          for (int i = 0; i < n; i++) {
              if (degree[i] == 1) {queue.offer(i);}
          }
          int remainNodes = n;
          while (remainNodes > 2) {
              int sz = queue.size();
              remainNodes -= sz;
              for (int i = 0; i < sz; i++) {
                  int curr = queue.poll();
                  for (int v : adj[curr]) {
                      degree[v]--;
                      if (degree[v] == 1) {queue.offer(v);}
                  }
              }
          }
          while (!queue.isEmpty()) {ans.add(queue.poll());}
          return ans;
      }
      
      ```
   
      
   
      
   





