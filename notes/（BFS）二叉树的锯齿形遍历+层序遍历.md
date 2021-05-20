# 锯齿形遍历，加flag标志判断

## 算法

### 实现BFS的几种算法。

- 使用两层嵌套循环。外层循环迭代树的层级，内层循环迭代每层上的节点。
- 也可以使用一层循环实现BFS。将要访问的节点添加到队列中，使用分隔符（例如：空节点）把不同层的节点分隔开。分隔符表示一层结束和新一层开始。

这里采用第二种方法。在此算法的基础上，借助双端队列实现锯齿形顺序。在每一层，使用一个空的双端队列保存该层所有的节点。根据每一层的访问顺序，即从左到右或从右到左，决定从双端队列的哪一端插入节点。

![二叉树锯齿形遍历.PNG](https://github.com/ZEQINLIN-666/AlgorithmNotes/blob/main/image/%E4%BA%8C%E5%8F%89%E6%A0%91%E9%94%AF%E9%BD%BF%E5%BD%A2%E9%81%8D%E5%8E%86.PNG?raw=true)

- 实现从左到右的遍历顺序（FIFO）。将元素添加到队列尾部，保证后添加的节点后被访问。从上图中可以看出，输入序列[1，2，3，4，5]，按照FIFO顺序得到输出序列为[1，2，3，4，5]。
- 实现从右到左的遍历顺序（FILO）。将元素添加到队列头部（addFirst），保证后添加的节点先被访问。输入序列[1，2，3，4，5]，按照FILO顺序得到输出序列为[5，4，3，2，1]。



>注意：一种替代做法是，实现标准的BFS算法，得到每层节点从左到右的遍历顺序。然后按照要求翻转某些层节点的顺序，得到锯齿形的遍历结果。

## 复杂度分析

### 时间复杂度：O（N），其中N是树中节点的数量。

- 每个节点仅访问一次。
- 双端队列的插入操作为常数时间。如果使用数组或list，头部插入需要O（K）的时间，其中K是数组或list的长度。

### 空间复杂度：O（N），其中N是树中节点的数量。

- 除了输出数组，主要的内存开销是双端队列。

- 任何时刻，双端队列中最多只存储两层节点。因此双端队列的大小不超过2·L，其中L是一层的最大节点数。包含最多节点的层可能是完全二叉树的叶节点层，大约有L/2个节点。

  因此最坏情况下，存储的节点数为2 × L/2 =N，即空间复杂度为O（N）



```JAVA
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        if(root == null){
            return res;
        }
        LinkedList<Integer> path = new LinkedList<>();
        LinkedList<TreeNode> nodes = new LinkedList<>();
        //标志是从左到右还是从右到左的遍历顺序，true代表从左到右
        boolean flag = true;
        //根节点加入队列
        nodes.addLast(root);
        //以null节点做为换层的分隔符
        nodes.addLast(null);

        //将当前节点向四周扩散，即存储下一层的所有节点，该层的子节点
        while(nodes.size() > 0){
            //取出队列的第一个元素，并将其从队列中删除
            TreeNode cur = nodes.pollFirst();
            if(cur != null){
                if(flag){
                    path.addLast(cur.val);
                }else{
                    path.addFirst(cur.val);
                }
                if(cur.left != null){
                    nodes.addLast(cur.left);
                }
                if(cur.right != null){
                    nodes.addLast(cur.right);
                }
            }else{
                //到这里说明已经到了某层的分隔符，就可以将该层的子队列放入结果队列中
                res.add(path);
                //新开空队列path，接下一层的队列
                path = new LinkedList<>();
                //如果节点队列中不为空，说明树还没有全遍历完
                if(nodes.size() > 0){
                    nodes.addLast(null);
                }
                //翻转遍历顺序
                flag = !flag;
            }
        }
        return res;
        
    }
}
```

# 层序遍历，删掉flag即可

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
          List<List<Integer>> res = new LinkedList<>();
        if(root == null){
            return res;
        }
        LinkedList<Integer> path = new LinkedList<>();
        LinkedList<TreeNode> nodes = new LinkedList<>();
        //根节点加入队列
        nodes.addLast(root);
        //以null节点做为换层的分隔符
        nodes.addLast(null);

        //将当前节点向四周扩散，即存储下一层的所有节点，该层的子节点
        while(nodes.size() > 0){
            //取出队列的第一个元素，并将其从队列中删除
            TreeNode cur = nodes.pollFirst();
            if(cur != null){
                path.addLast(cur.val);
                if(cur.left != null){
                    nodes.addLast(cur.left);
                }
                if(cur.right != null){
                    nodes.addLast(cur.right);
                }
            }else{
                //到这里说明已经到了某层的分隔符，就可以将该层的子队列放入结果队列中
                res.add(path);
                //新开空队列path，接下一层的队列
                path = new LinkedList<>();
                //如果节点队列中不为空，说明树还没有全遍历完
                if(nodes.size() > 0){
                    nodes.addLast(null);
                }
            }
        }
        return res;
    }
}
```

