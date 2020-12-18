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
- https://leetcode-cn.com/problems/minimum-genetic-mutation/#/description
- https://leetcode-cn.com/problems/generate-parentheses/#/description
- https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row/#/description

## 

- https://leetcode-cn.com/problems/word-ladder/description/
- https://leetcode-cn.com/problems/word-ladder-ii/description/
- https://leetcode-cn.com/problems/number-of-islands/
- https://leetcode-cn.com/problems/minesweeper/description/