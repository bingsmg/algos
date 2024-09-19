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

后序遍历有一种比较取巧的方式，如下：

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