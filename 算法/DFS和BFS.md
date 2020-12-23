## DFS和BFS



- [DFS 代码模板（递归写法、非递归写法）](http://shimo.im/docs/ddgwCccJQKxkrcTq)

```java
#递归写法  py
visited = set() 

def dfs(node, visited):
    if node in visited: # terminator
    	# already visited 
    	return 
    
	visited.add(node) 

	# process current node here. 
	...
	for next_node in node.children(): 
		if next_node not in visited: 
			dfs(next_node, visited)
                
#非递归写法

def DFS(self, root): 

	if tree.root is None: 
		return [] 

	visited, stack = [], [root]

	while stack: 
		node = stack.pop() 
		visited.add(node)

		process (node) 
        # 生成相关的节点
		nodes = generate_related_nodes(node) 
		stack.push(nodes) 

	# other processing work 
	...
            
            
//java  递归            
public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> allResults = new ArrayList<>();
        if(root==null){
            return allResults;
        }
     
        travel(root,0,allResults);
        return allResults;
    }


    private void travel(TreeNode root,int level,List<List<Integer>> results){
        if(results.size()==level){
            results.add(new ArrayList<>());
        }
        results.get(level).add(root.val);
        if(root.left!=null){
            travel(root.left,level+1,results);
        }
        if(root.right!=null){
            travel(root.right,level+1,results);
        }
    }
```



- [BFS 代码模板](http://shimo.im/docs/P8TqKHGKt3ytkYYd)

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int x) {
        val = x;
    }
}

public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> allResults = new ArrayList<>();
    if (root == null) {
        return allResults;
    }
    Queue<TreeNode> nodes = new LinkedList<>();
    nodes.add(root);
    while (!nodes.isEmpty()) {
        int size = nodes.size();
        List<Integer> results = new ArrayList<>();
        for (int i = 0; i < size; i++) {
            TreeNode node = nodes.poll();
            results.add(node.val);
            if (node.left != null) {
                nodes.add(node.left);
            }
            if (node.right != null) {
                nodes.add(node.right);
            }
        }
        allResults.add(results);
    }
    return allResults;
}
```

---

## 实战题目

- https://leetcode-cn.com/problems/binary-tree-level-order-traversal/#/description

```java
102. 二叉树的层序遍历
给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。

 

示例：
二叉树：[3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7
返回其层次遍历结果：

[
  [3],
  [9,20],
  [15,7]
]

        
        //
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        //层次遍历用队列
        List<List<Integer>> res = new ArrayList<>();
        if(root == null) return res;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while(!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();
           
            int size = queue.size();
            for(int i=0;i<size;i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if(node.left != null) {
                    queue.add(node.left);
                }

                if(node.right != null) {
                    queue.add(node.right);
                }
            }
            res.add(list);
        }
        return res;
    }
}
```



- https://leetcode-cn.com/problems/minimum-genetic-mutation/#/description

```java
433. 最小基因变化
一条基因序列由一个带有8个字符的字符串表示，其中每个字符都属于 "A", "C", "G", "T"中的任意一个。

假设我们要调查一个基因序列的变化。一次基因变化意味着这个基因序列中的一个字符发生了变化。

例如，基因序列由"AACCGGTT" 变化至 "AACCGGTA" 即发生了一次基因变化。

与此同时，每一次基因变化的结果，都需要是一个合法的基因串，即该结果属于一个基因库。

现在给定3个参数 — start, end, bank，分别代表起始基因序列，目标基因序列及基因库，请找出能够使起始基因序列变化为目标基因序列所需的最少变化次数。如果无法实现目标变化，请返回 -1。

注意:

起始基因序列默认是合法的，但是它并不一定会出现在基因库中。
所有的目标基因序列必须是合法的。
假定起始基因序列与目标基因序列是不一样的。
示例 1:

start: "AACCGGTT"
end:   "AACCGGTA"
bank: ["AACCGGTA"]

返回值: 1
示例 2:

start: "AACCGGTT"
end:   "AAACGGTA"
bank: ["AACCGGTA", "AACCGCTA", "AAACGGTA"]

返回值: 2
示例 3:

start: "AAAAACCC"
end:   "AACCCCCC"
bank: ["AAAACCCC", "AAACCCCC", "AACCCCCC"]

返回值: 3

```

```java
class Solution {
    //回溯  dfs
    int minStepCount = Integer.MAX_VALUE;
    public int minMutation(String start, String end, String[] bank) {
        dfs(new HashSet<String>(),0,start,end,bank);
        return (minStepCount == Integer.MAX_VALUE) ? -1 : minStepCount;
    }

    public void dfs(HashSet<String> step,int stepCount,String current,String end,String[] bank) {
        if(current.equals(end)) {
            minStepCount = Math.min(minStepCount,stepCount);
            return;
        }
        //遍历基因库，找到与当前基因相差一个碱基的就是下一步变化的基因
        //这样步数+1 并进入下一层
        for(String str : bank) {
            int diff = 0;
            for(int i = 0;i<str.length();i++) {
                if(current.charAt(i) != str.charAt(i)) { //相差最多为1
                    if(++diff > 1) {
                        break;
                    }
                }
            }
            
              //剪枝       0ms  否则 2ms
            if(step >= minStep) return;

            if(diff == 1 && !step.contains(str)) {   //进入下一层判断
                step.add(str);
                dfs(step,stepCount+1,str,end,bank);
                //reverse
                step.remove(str);
            }
            
        }
    }
}
```



- https://leetcode-cn.com/problems/generate-parentheses/#/description

```java
22. 括号生成
数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

 

示例：

输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]

```

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        //回溯法 经常和深度优先遍历结合 dfs
        List<String> res  = new ArrayList<>();
        StringBuilder sb = new StringBuilder();

        dfs(res,n,0,0,sb);
        return res;
    }

    public void dfs(List<String> res,int n,int left,int right,StringBuilder sb) {
        if(left == n && right == n) {
            res.add(sb.toString());
            return;
        }

        if(left < right) return;
        if(left < n) {
            dfs(res,n,left+1,right,sb.append('('));
            sb.deleteCharAt(sb.length() -1);
        }

        if(right < left) {
            dfs(res,n,left,right+1,sb.append(')'));
            sb.deleteCharAt(sb.length() -1);
        }
    }
}
```

- https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row/#/description

```java
515. 在每个树行中找最大值
您需要在二叉树的每一行中找到最大的值。

示例：

输入: 

          1
         / \
        3   2
       / \   \  
      5   3   9 

输出: [1, 3, 9]

```

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
    public List<Integer> largestValues(TreeNode root) {
        //BFS
        //就是层次遍历
        List<Integer> res = new ArrayList<>();
        if(root == null) {
            return res;
        }
        Queue<TreeNode> queue  = new LinkedList<>();
        // queue.add(root);   和queue.offer()效果一样
        queue.offer(root);
        while(!queue.isEmpty()) {
            int max = Integer.MIN_VALUE;
            int size = queue.size();
            for(int i = 0;i<size;i++) {
                TreeNode node = queue.poll();
                max = Math.max(node.val,max);
                if(node.left != null) {
                    // queue.add(node.left);
                    queue.offer(node.left);
                }
                if(node.right != null) {
                    // queue.add(node.right);
                    queue.offer(node.right);
                }
            }
            res.add(max);
        }
        return res;
    }
}
```

**深度优先搜索的时间要比广度优先搜索快**

```java

class Solution {
    //DFS  深度优先搜索
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        dfs(root,res,0);
        return res;
    }

    public void dfs(TreeNode root,List<Integer> res,int level) {
        if(root == null) {
            return;
        }

        if(res.size() == level) {
            res.add(root.val);
        } else {
            res.set(level,Math.max(res.get(level),root.val));
        }
        dfs(root.left,res,level+1);
        dfs(root.right,res,level+1);
    }
}
```



## 

- https://leetcode-cn.com/problems/word-ladder/description/

```java
127. 单词接龙
给定两个单词（beginWord 和 endWord）和一个字典，找到从 beginWord 到 endWord 的最短转换序列的长度。转换需遵循如下规则：

每次转换只能改变一个字母。
转换过程中的中间单词必须是字典中的单词。
说明:

如果不存在这样的转换序列，返回 0。
所有单词具有相同的长度。
所有单词只由小写字母组成。
字典中不存在重复的单词。
你可以假设 beginWord 和 endWord 是非空的，且二者不相同。
示例 1:

输入:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

输出: 5

解释: 一个最短转换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog",
     返回它的长度 5。
示例 2:

输入:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

输出: 0

解释: endWord "cog" 不在字典中，所以无法进行转换。
```

```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        //BFS
        //将字典中的单词放到set中，方便查询
        Set<String> dictSet = new HashSet<>(wordList);
        //记录单词是否被访问过
        Set<String> visit = new HashSet<>();
        Queue<String> queue = new LinkedList<>();

        queue.add(beginWord);
        int minlen = 1;
        while(!queue.isEmpty()) {
            int level = queue.size();
            for(int i = 0;i<level;i++) {
                //出队
                String word = queue.poll();
                //遍历每个单词，修改它的每个字符使它成为一个新单词
                //并且查看这个新单词是否在字典中，如果在且没有被访问，就加入队列
                for(int j = 0;j<word.length();j++) {
                    char[] ch = word.toCharArray();
                    for(char c= 'a';c<='z';c++) {
                        if(c == word.charAt(j))
                            continue;
                        ch[j] = c;         //修改字符
                        //新单词
                        String newWord = String.valueOf(ch);
                        //查看字典
                        if(dictSet.contains(newWord) && visit.add(newWord)) {
                            //判断是否是endword
                            if(newWord.equals(endWord)) {
                                return minlen + 1;
                            }
                            queue.add(newWord);
                        }
                    }
                }
            }
            //每向外扩展一圈，长度+1
            minlen++;
        }
        
         return 0;
    }
   
}
```



- https://leetcode-cn.com/problems/word-ladder-ii/description/
- https://leetcode-cn.com/problems/number-of-islands/
- https://leetcode-cn.com/problems/minesweeper/description/