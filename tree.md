# binary tree

[toc]

树是常见的数据结构，以父子关系组织数据，并定制一些规则后可以以 O(logn) 的时间复杂度处理元素。树相关的题目以二叉树居多，二叉树的数据结构：

```java
public class BinaryTree {
    TreeNode root;
}
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

## 二叉树的遍历

### 102.二叉树的层序遍历

[102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal)

如何去遍历一棵树的所有节点呢？我们每次只能通过 parent 节点访问到它的 child，所以最简单的遍历方式是记录每一层的所有节点，每次处理每一层的所有几点，即层序遍历。那么我们需要注意哪些细节呢？层序遍历需要记录每一层的节点，因为下一层的节点只能通过上一层找到，所以在处理一层的节点元素过程中，需要将这一层的下一层所有节点记录下来，因为我们是从 root 开始逐步过渡到 leaf，所以层序遍历的过程可以使用队列来实现。但是因为队列的元素随着处理每一个节点实时变化，所以我们每次处理一层的时候需要**记录队列大小**。每次只处理这一层大小的队列元素。

Java 中的队列用`Deque<TreeNodee> queue = new ArrayDeque<>()/LinkedList<>();`即可，常用的入队和出队方法`offer`和`poll`。

由于我们是层序处理的，所以最先让根节点这一层进队列，什么时候处理结束？肯定是队列为空，因为我们在处理上一层的过程中，就加入了下一层，所以如果将要处理的下一层为空了，肯定就处理完所有节点了。

```java
public void traverse(TreeNode root) {
    List<Integer> ans = new ArrayList<>();
    if (root == null) return ans;
    Deque<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        int n = queue.size(); // 每一层的元素大小
        for (int i = 0; i < n; i++) {
            TreeNode node = queue.poll();
            ans.add(node.val); // 处理每一个节点
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
    }
}
```

### LC94.二叉树的中序遍历

[94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal)

中序遍历首先要确定概念：左中右。前中后序的遍历都是对于 root 节点的处理时间的确定。

我们以栈的处理方式来解决中序遍历，因为递归的处理方式都是很简单的几行代码，不好探究到具体的细节处理逻辑了。中序遍历，我们要在左子树处理完后处理中，之后处理右子树，但是我们对左右子树的查找只能通过根节点进行，而且需要记住的是，我们只有通过某个节点的 parent 才能找到它而不是它的祖先或者 root。所以我们首先要找到 root 左边的最下面的左子树，其实之前肯困惑我的一个点是，我们往左找，是找到最左边的个 leaf 还是找到 leaf 的 parent，这个很重要，因为按照人的脑回路来思考，肯定是直接找到那个 parent 才能找到 right child，但是，我们很难判断当前遍历到的节点是 leaf 的 parent，因为我们判断 leaf 的条件一直都是 left 和 right 都为 null。

我们知道在寻找左子树最左节点的过程，我们会遍历完所有的左子树的一个个子 root，所以我们会用栈来记录这些节点，并且在处理完子 root 后，我们会拿到 right child，我们还需要找右子树的最左节点，如果在脑子里想这个过程可能会感觉很绕，我们可以从 leaf 往上分析，并且，我们分析的过程记住了，**空树也是一个二叉树，没有 child 的节点也是二叉树**。我们对每一个树的处理逻辑是一样的。

Java 中栈我们用`Deque<TreeNode> stack = new ArrayDeque<>()/LinkedList<>();`来表示，常用入栈和出栈方法`push(addFirst)`和`pop(removeFirst)`以及获取栈顶`peek(getFirst)`。

```java
public void traverse(TreeNode root) {
    List<Integer> ans = new ArrayList<>();
    if (root == null) return ans;
    Deque<TreeNode> stack = new LinkedList<>();
    stack.push(root);
    while (!stack.isEmpty()) {
        TreeNode node = stack.peek();
        while (node.left != null) {
            stack.push(node.left);
            node = node.left;
        }
        node = stack.pop();
        // 当到达最左边那个 leaf，我们仍然认为它是一棵树，我们处理该树
        ans.add(node.val);
        if (node.right != null) stack.push(node.right);
    }
}
```

刚开始写出了上面这样的代码，粗略一看好像没什么问题，但是按照思路走一遍遍历处理逻辑，发现有问题，当左子树处理完后，栈弹出左子树的 parent，然后接着判断 parent 的左子树是不是 null，然后又去处理了左子树，导致死循环了，很明显，逻辑有问题，我们处理过的节点，下次一定不能让它再处理一次了，那怎么解决呢？

出现这个问题的原因是我们每次都会走一个判断`while (node.left != null)`，那应该怎么判断才不会发生这种情况呢？我们用一个全局的指针来标识当前要处理的元素，不要判断该指针的左子树，而是一直判断该指针指向的元素，我们维护该指针永远指向正确的位置。

```java
public void traverse(TreeNode root) {
    List<Integer> ans = new ArrayList<>();
    if (root == null) return ans;
    Deque<TreeNode> stack = new LinkedList<>();
    TreeNode p = root;
    while (!stack.isEmpty() || p != null) {
        while (p != null) {
            stack.push(p);
            p = p.left;
        }
        p = stack.pop();
        ans.add(node.val);
        p = p.right; // 这一步很重要，一定要按照空树也是二叉树的思想来解决，p 置空是为了导致下一次的判断，不要重复判断处理过的节点了，避免上面题解的死循环。
    }
}
```

上面的写法很简洁，但是按照常规的思维去理解，很难一下子就理解到位，因为 p 每次都是处理了 null 节点的，通过指向 null 节点从而跳出了对每次左子树的重复判断。更容易理解的写法是：

```java
public void traverse(TreeNode root) {
    List<Integer> ans = new ArrayList<>();
    if (root == null) return ans;
    Deque<TreeNode> stack = new LinkedList<>();
    TreeNode p = root;
    // 将该逻辑提出来，以免死循环
    while (p != null) {
        stack.push(p);
        p = p.left;
    }

    while (!stack.isEmpty()) {
        p = stack.pop();
        ans.add(p.val); // 记录根节点值，转向右节点处理
        p = p.right;
        while (p != null) {
            stack.push(p);
            p = p.left;
        }
    }
    return ans;
}
```

### LC144.二叉树的前序遍历

[144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal)

前序遍历记住：根左右，我们仍然以非递归的方式来描述该过程。

```java
public void traverse(TreeNode root) {
    List<Integer> ans = new ArrayList<>();
    if (root == null) return ans;
    Deque<TreeNode> stack = new LinkedList<>();
    stack.push(root);
    while (stack.isEmpty()) {
        TreeNode node = stack.pop();
        ans.add(node.val);
        if (node.right != null) node.push(node.right);
        if (node.left != null) node.push(node.left);
    }
}
```

### LC145.二叉树的后序遍历

[145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal)

后序遍历有一种比较取巧的方式，原理是将：根-右-左 的遍历结果反转，就是：左-右-根，又因为栈的入出反逻辑，所以入栈按照先左后右入栈即可。如下：

```java
public void traverse(TreeNode root) {
    List<Integer> ans = new ArrayList<>();
    if (root == null) return ans;
    Deque<TreeNode> stack = new LinkedList<>();
    stack.push(root);
    while (stack.isEmpty()) {
        TreeNode node = stack.pop();
        if (node.left != null) node.push(node.left);
        if (node.right != null) node.push(node.right);
        ans.add(node.val);
    }
    Collections.reverse(ans);
}
```

## 二叉树的构造

### LC105.前序中序序列构造二叉树

[105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

为什么要通过前序和中序或者后序和中序才能构造出二叉树？因为只有中序才能找到 root 节点，根据中序 root 节点的索引 idx，才能得到 left 和 right 的长度，进而划分前序或者后序序列的序列区间。那前序和后序起了什么作用？当然是定位 root 节点啊，根据前序和后序的 preorder_l 或者 postorder_r 能确定 root，根据 root 找到 idx，根据 idx 划分原前中后序的序列区间确定 left 和 right 递归就可构建出二叉树。

```java
public TreeNode buildTree(int[] preorder, int[] inorder) {
    int n = preorder.length;
    if (n == 0) return null;
    Map<Integer, Integer> memo = new HashMap<>();
    for (int i = 0; i < inorder.length; i++) memo.put(inorder[i], i);
    return build(preorder, 0, n - 1, inorder, 0, n - 1);
}
```

当我们知道 root 并且在中序里找到 root 对应的 idx，我们划分序列时，需要根据左右子树的长度来划分，其次递归函数什么时候返回呢？返回什么呢？当划分的序列长度已经超出范围了，应该返回 null 了，即 pr < pl 或者 ir < il。其余情况就得构造 root 节点了，并递归去构造 root.left 和 root.right;

```java
private TreeNode build(int[] preorder, int pl, int pr,
                       int[] inorder, int il, int ir) {
    if (pr < pl || ir < il) return null;
    TreeNode root = new TreeNode(preorder[pl]);
    int idx = memo.get(root.val);
    int left_len = idx - il;
    root.left = build(preorder, pl + 1, pl + left_len, inorder, il, idx - 1);
    root.right = build(preorder,  pl + left_len + 1, pr, inorder, idx + 1, ir);
    return root;
}
```

### LC106.中序后序序列构造二叉树

[106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

原理和前序中序类似，第一步一直都是构建 memo 快速从中序里找到 root，之后根据下标分割，递归构造。

```java
Map<Integer, Integer> memo = new HashMap<>(); // memo 备忘录
public TreeNode buildTree(int[] inorder, int[] postorder) {
    int n = inorder.length;
    if (n == 0) return null;
    for (int i = 0; i < inorder.length; i++) memo.put(inorder[i], i);
    return build(postorder, 0, n - 1, inorder, 0, n - 1);
}
private TreeNode build(int[] postorder, int pl, int pr,
                       int[] inorder, int il, int ir) {
    if (pr < pl || ir < il) return null;
    TreeNode root = new TreeNode(preorder[pr]);
    int idx = memo.get(root.val);
    int left_len = idx - il;
    root.left = build(preorder, pl, pl + left_len - 1, inorder, il, idx - 1);
    root.right = build(preorder,  pl + left_len, pr - 1, inorder, idx + 1, ir);
    return root;
}
```

其实很简单嘛，找对方法是关键，确实发现路径以及文件命名以 - 来区分，然后变量命名以 _ 来区分最舒服。

### LC1008.前序遍历构造二叉搜索树

[1008. 前序遍历构造二叉搜索树](https://leetcode.cn/problems/construct-binary-search-tree-from-preorder-traversal/)

根据前序遍历构造二叉搜索树，我们知道要构造一棵树就必须通过中序+后序或者前序+后序，那只通过前序就构造出树，肯定是要基于树的性质去做的，所以这里得牢牢把握二叉搜索树的性质：

左子树左右节点 < 根节点 < 右子树所有节点。

仔细分析前序遍历数组，我们可以发现每次前序的 pl 为根节点，那么如何分别左右子树呢？可以发现右子树的起点坐标为当前 root 元素的下一个更大元素。求下一个更大更小元素，我们很容易想到数据结构单调栈。所以我们预先通过单调栈 O(n) 处理得到 memo 的查找结构，在得到 memo 后，我们就可以瞬间根据 root 区分左右子树，从而递归构建二叉搜索树。

```java
public TreeNode bstFromPreorder(int[] preorder) {
	int n = preorder.length;
    if (n == 0) return null;
    Map<Integer, Integer> memo = new HashMap<>();
    Deque<Integer> mono_stack = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        while (!mono_stack.isEmpty() && preorder[i] > preorder[mono_stack.peek()]) {
            int idx = mono_stack.pop();
            memo.put(preorder[idx], i);
        }
        mono_stack.push(i);
    }
}
```

```java
private TreeNode build(int[] preorder, int pl, int pr) {
    if (pr < pl) return null;
    TreeNode root = new TreeNode(preorder[pl]);
    int idx = memo.get(root.val); // !!!!!!
    root.left = build(preorder, pl + 1, idx - 1);
    root.right = build(preorder, idx, pr);
    return root;
}
```

刚开始是这么写了，结果发现`memo.get(root.val)`会返回 null，为什么？为什么？为什么？重要的事说三遍，其实是二叉搜索树最大的节点不会进入 memo 里，因为它没有下一个更大的元素了。但我们在这个时候仍然需要去让程序执行，并且将那个节点构造出来。所以我们可以修改为：

```java
int idx = memo.get(root.val, pr + 1); // 为什么这么改？？
```

比如一个数组长度为 5，那么最后没有下一个更大元素的就是索引 4 的元素，最后的 build 就是 build(preorder, 4, 4)，我们找不到就让 idx 为 5，从而让 pl+1 和 idx-1 超出范围，让 idx 和 pr 超出范围。

### LC152.验证BST的后序遍历序列

[LCR 152. 验证二叉搜索树的后序遍历序列](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

一定牢牢把握题目信息，BST，即左子树所有元素小于 root 小于右子树所有元素。刚开始我的思路是根据后序构建树，然后中序比遍历判断有序性，结果发现有问题，好吧，修正了 break 后没问题了：

```java
List<Integer> ans = new ArrayList<>();
public boolean verifyTreeOrder(int[] postorder) {
    int n = postorder.length;
    if (n <= 1) return true;
    TreeNode root = build(postorder, 0, n - 1);
    traverse(root);
    for (int i = 0; i < n - 1; i++) {
        if (ans.get(i + 1) <= ans.get(i)) return false;
    }
    // 还原+中序遍历
    return true;
}

private void traverse(TreeNode root) {
    if (root == null) return;
    traverse(root.left);
    ans.add(root.val);
    traverse(root.right);
}

public TreeNode build(int[] postorder, int pl, int pr) {
    if (pr < pl) return null;
    int idx = pr;
    for (int i = pl; i <= pr; i++) {
        if (postorder[i] > postorder[pr]) {
            idx = i;
            break; // 记得 break，第一次编码这里出错了 ！！！！
        }
    }
    TreeNode root = new TreeNode(postorder[pr]);
    root.left = build(postorder, pl, idx - 1);
    root.right = build(postorder, idx, pr - 1);
    return root;
}
```

但我们有必要这么麻烦吗，先构建-再中序遍历-再判断有序性，其实没必要，我们完全可以通过递归解决，每次根据 root 找到序列分割点，判断分割后的两段是否一段全小于 root_val，另一段全大于 root_val：

```java
valid(postorder, 0, n - 1);
private boolean valid(int[] postorder, int pl, int pr) {
    if (pr <= pl) return true;
    int root_val = postorder[pr];
    int i = pl;
    while (postorder[i] < root_val) i++;
    int idx = i;
    while (postorder[i] > root_val) i++;
    return i == pr && valid(postorder, pl, idx - 1) && valid(postorder, idx, pr - 1);
    
}
```

递归的时间复杂度如何计算？其实就是 **递归次数*递归过程中的最大基础操作的执行次数**，该递归可以看为 n 次，每次递归需要走一个 while，最差时间复杂度得走 n * (n - 1) * (n - 2)....1 次，所以是 nlog(n)。

## 二叉树的路径

### LC257.二叉树的所有路径

路径是指两个节点间的相连方式，本题目要求的是 root 到所有 leaf 的路径，即从上往下的所有分流。我们得遍历永远都只能从 root 开始向下扩展，要记得所有的路径，按照 dfs 的方式来看，就是在到达叶子结点后，我们必须将栈里的所有数据保存为一个副本，代表一条到达 leaf 的路径，当遍历完所有的 leaf 就能得到最终结果。

```java
public List<String> binaryTreePaths(TreeNode root) {
	List<String> ans = new ArrayList<>();
    if (root == null) return ans;
    Deque<TreeNode> stack = new LinkedList<>();
    TreeNode cur = root;
    while (!stack.isEmpty()) {
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }
        cur = stack.pop();
        if (cur.left == null && cur.right == null) {
            // 将 stack 转为 String
        }
        cur = cur.right;
    }
}
```

上面的思路看似很合理，但是错的，因为在记录第一条路径后，因为父节点出栈了，导致剩余的所有路径都会缺失已出栈的节点，那如何解决？我们记录每一个节点对应的临时路径即可，即同时建立两个栈，一个 node_stack，一个 path_stack 来记录短路径，最后发现出栈节点是 leaf，就将对应的 path 添加到结果。

```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> ans = new ArrayList<>();
    if (root == null) return ans;
    Deque<TreeNode> node_stack = new LinkedList<>();
    Deque<String> path_stack = new LinkedList<>();
    node_stack.push(root);
    path_stack.push(String.valueOf(root.val));
    while (!node_stack.isEmpty()) {
        TreeNode node = node_stack.pop();
        String path = path_stack.pop();
        if (node.left == null && node.right == null) {
            ans.add(path);
        }
        if (node.right != null) {
            node_stack.push(node.right);
            path_stack.push(path + "->" + node.right.val);
        }
        if (node.left != null) {
            node_stack.push(node.left);
            path_stack.push(path + "->" + node.left.val);
        }
    }
    return ans;
}
```

迭代的正确思路如上，实际上，我们可以通过递归来实现：

```java
List<String> ans = new ArrayList<>();
Deque<Integer> path = new LinkedList<>();
private void dfs(TreeNode root) {
    path.offerLast(root.val);
    if (root.left == null && root.right == null) {
        ans.add(String.join("->", path));
        return;
    }
    if (root.left != null) {
        dfs(root.left);
        path.pollLast();
    }
    if (root.right != null) {
        dfs(root.right);
        path.pollLast();
    }
}
```

### LC113.路径总和II

[113. 路径总和 II](https://leetcode.cn/problems/path-sum-ii/)

给你`root`和一个整数目标和`targetSum`，找出所有 **从根节点到叶子节点** 路径总和等于给定目标和的路径。那么根据题目描述，直接拿所有路径，并过滤出来符合条件的行不行？肯定可以啊，所以修改二叉树所有路径的代码如下：

```java
int target;
List<List<Integer>> ans = new ArrayList<>();
List<Integer> path = new ArrayList<>();
private void dfs(TreeNode root) {
    path.add(root.val);
    if (root.left == null && root.right == null && check(path)) {
        ans.add(new ArrayList<>(path));
        return;
    }
    if (root.left != null) {
        dfs(root.left);
        path.remove(path.size() - 1);
    }
    if (root.right != null) {
        dfs(root.right);
        path.remove(path.size() - 1);
    }
}
private boolean check(List<Integer> path) {
    int sum = 0;
    for (int v : path) sum += v;
    return sum == target;
}
```

这种实现方式需要每次都走一遍 check 函数，多了一部分时间开销，有没有更好的方式可以减掉这一部分开销呢？

我们直接在递归的过程中将目标和以参数的形式传递下去即可：

```java
private void dfs(TreeNode root, int target) {
    path.add(root.val);
    if (root.left == null && root.right == null) {
        if (target == root.val) ans.add(new ArrayList<>(path));
        return;
    }
    if (root.left != null) {
        dfs(root.left, target - root.val);
        path.remove(path.size() - 1);
    }
    if (root.right != null) {
        dfs(root.right, target - root.val);
        path.remove(path.size() - 1);
    }
}
```

路径问题的写法如上面所示，只让节点执行逻辑，不会判断 null。一般会直接判断节点是不是叶子节点，如果是叶子节点的话处理结果，不会让递归走到 null，因为如果到了 null，那么会导致记录的路径会在 parent 的 left 为 null，right 不为 null 时提前把 parent 移除掉，从而导致结果不正确。

本来是这么想的，后面发现不对，想错了，我们想的是如果这么写：

```java
path.add(val);
// ...
dfs(root.left);
dfs(root.right);
path.removeLast();
```

我们担心的是，这样会不会导致只会 remove 一次，导致处理少了节点，从而影响结果，之前一直对这两种处理方式不是很理解清楚，为什么有时候判断 null 能得到正确答案，有时候不判断 null 能得到正确答案，问题在哪呢？其实在前面 leaf 的处理完后是否 return。

- 如果不 return 就可以保证 leaf 处理完后，就将 leaf 从 path 移除，所以可以保证每个节点处理完后都能在返回后从 path 里移除掉，保证结果正确。
- 如果 return，就会导致处理完后就会导致上层节点的处理结束，导致移除的是下层的节点，从而导致路径多出来一个节点。

那么我们怎么写呢？这里写处理 null 的代码，保证添加了节点后，不论如何，处理完该节点都能从 path 里移除掉该节点。

```java
private void dfs(TreeNode root) {
    if (root == null) return;
    target -= root.val;
    path.add(root.val);
    if (root.left == null && root.right == null && target == 0) {
        ans.add(new ArrayList<>(path));
        // No return!!!
    }
    dfs(root.left);
    dfs(root.right);
    path.remove(path.size() - 1);
}
```



### LC437.路径总和III

[437. 路径总和 III](https://leetcode.cn/problems/path-sum-iii/)

前面两道题我们处理的都是根节点到叶子节点，即 root-...-leaf 的路径逻辑，通过 stack 和递归分别实现了。但是本题目不是只需要考虑 root 而是需要考虑每一个节点的情况。所以主函数就需要递归，而且需要记录一个全局变量来统计整个过程中得到了多少个答案。

```java
public int pathSum(TreeNode root, int targetSum) {
    if (root == null) return 0;
    dfs(root, targetSum);
    pathSum(root.left, targetSum);
    pathSum(root.right, targetSum);
    return count;
}
int count;
private void dfs(TreeNode root, long targetSum) { // 使用 long 防止负负得正超出 case 越界问题
    if (root == null) return;
    targetSum -= root.val;
    if (targetSum == 0) count++; // 任意路径，起点不必 root，终点不必 leaf
    dfs(root.left, targetSum);
    dfs(root.right, targetSum);
}
```



