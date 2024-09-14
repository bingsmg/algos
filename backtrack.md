# backtrack

[toc]

回溯法的核心是回溯。在搜索到某一节点的时候，如果我们发现目前的节点（及
其子节点）并不是需求目标时，我们回退到原来的节点继续搜索，并且把在目前节点修改的状态
还原。

## 46.全排列

[46. 全排列](https://leetcode.cn/problems/permutations/)

作为回溯最经典的入门题目即全排列，我们以最普通的 dfs 眼光去搜索所有可能性，则需要去设置一个 visited/used 数组来表示当前处理的元素状态。类似一棵树的遍历过程：

![image.png](https://pic.leetcode.cn/1674875632-abWPjF-image.png)

但是如此遍历就需要开辟新的空间，引入时间复杂度，所以更优的一种做法是通过交换当前层的元素，为什么交换当前元素可以实现呢？通过交换，我们可以将任何元素放到当前位置，然后递归处理剩余的元素

![image-20240819145244651](https://imageshark.oss-cn-beijing.aliyuncs.com/202408191452826.png)

```java
class Solution {

    List<List<Integer>> path = new ArrayList<>();
    
    public List<List<Integer>> permute(int[] nums) {
        backtrack(nums, 0);
        return path;
    }

    private void backtrack(int[] nums, int level) {
        if (level == nums.length - 1) {
            List<Integer> cur = new ArrayList<>();
            for (int num : nums) cur.add(num);
            path.add(cur);
            return;
        }
        for (int i = level; i < nums.length; i++) {
            swap(nums, i, level);
            backtrack(nums, level + 1);
            swap(nums, i, level);
        }
    }

    private void swap(int[] nums, int i, int j)  {
        int aux = nums[i];
        nums[i] = nums[j];
        nums[j] = aux;
    }
}
```

## 77.组合
