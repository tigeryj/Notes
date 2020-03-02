<!-- TOC -->

- [二分搜索树](#二分搜索树)
    - [思考：如何统计一本书中一个单词出现的频率？](#思考如何统计一本书中一个单词出现的频率)
    - [定义](#定义)
    - [操作](#操作)
        - [创建](#创建)
        - [增加](#增加)
        - [查找](#查找)
        - [删除（重点掌握）](#删除重点掌握)
    - [遍历](#遍历)
    - [思想](#思想)
    - [二分搜索树VS哈希表](#二分搜索树vs哈希表)

<!-- /TOC -->

### 二分搜索树

#### 思考：如何统计一本书中一个单词出现的频率？

#### 定义
对于树中的每个节点
- 其左子树中的每个节点的值都小于该节点的值
- 其右子树中的每个节点的值都大于该节点的值   

![](pics/2020-03-01-17-36-23.png)
#### 操作
##### 创建
创建一个BST数据结构，可以用节点(Node)的结构来存放元素,如下
``` java
public class BST<Key extends Comparable<Key>, Value> {

    private class Node {
        private Key key;
        private Value value;
        private Node left;
        private Node right;

        public Node(Key key, Value value) {
            this.key = key;
            this.value = value;
            left = right = null;
        }

        public Node(Node node) {
            this.key = node.key;
            this.value = node.value;
            this.left = node.left;
            this.right = node.right;
        }
    }

    private Node root;
}
```
下面基于这个数据结构来增加基本的操作方法
##### 增加
增加元素后要维持树的结构还具备二叉搜索树的性质
``` java
    public void insert(Key k, Value v) {
        root = insert(root, k, v);
    }

    //插入
    private Node insert(Node root, Key k, Value v) {

        if (root == null) {
            return new Node(k, v);
        }

        if (k.compareTo(root.key) < 0) {
            root.left = insert(root.left, k, v);
        } else if (k.compareTo(root.key) > 0) {
            root.right = insert(root.right, k, v);
        } else {
            root.value = v;
        }
        return root;
    }
```
##### 查找
``` java
    public Value search(Key key) {
        return search(root, key);
    }
    
    private Value search(Node node, Key key) {
        if (node == null) {
            return null;
        }
        if (key.compareTo(node.key) == 0) {
            return node.value;
        } else if (key.compareTo(node.key) < 0) {
            return search(root.left, key);
        } else {
            return search(root.right, key);
        }
    }
```
##### 删除（重点掌握）
``` java
   public void remove(Key key) {
        root = remove(root, key);
    }

    //删除
    private Node remove(Node node, Key key) {
        if (node == null) {
            return null;
        }

        if (key.compareTo(node.key) < 0) {

            node.left = remove(node.left, key);
            return node;

        } else if (key.compareTo(node.key) > 0) {

            node.right = remove(node.right, key);
            return node;

        } else {
            if (node.left == null) {
                Node right = node.right;
                node.right = null;
                return right;
            }

            if (node.right == null) {
                Node left = node.left;
                node.left = null;
                return left;
            }

            Node leftMax = new Node(maximum(node.left));
            removeMax(node.left);
            leftMax.left = node.left;
            leftMax.right = node.right;

            node.left = node.right = null;
            return leftMax;
        }
    }

    public void removeMax() {
        removeMax(root);
    }

    //删除最大元素
    private Node removeMax(Node node) {

        if (node.right == null) {
            Node left = node.left;
            node.left = null;
            return left;
        }

        node.right = removeMax(node.right);
        return node;
    }
```
#### 遍历
二叉搜索的中序遍历就是有顺序的输出
``` java
    public void inTraversal() {
        inTraversal(root);
    }

    private void inTraversal(Node node) {
        if (node == null) {
            return;
        }
        inTraversal(node.left);
        System.out.print(node.key + "\t");
        inTraversal(node.right);
    }
```
#### 思想
从上面可以看出很多操作都是基于递归的思想，写递归方法时可以分三步
- 确定递归方法的返回值
- 运用递归的思想，递归函数能够正常处理结果
- 确定终止条件   
 
递归写完后再构建用例验证下
#### 二分搜索树VS哈希表
散列表在插入、删除和查找等运算上最快可以达到O(1)的效率，为什么还需要二分查找树呢？
- 散列表是无序的，如果需要输出有序的元素，只需要对二分查找树进行中序遍历即可
- 散列表在插入时遇到哈希冲突、或数组扩容时会不稳定，但平衡二叉查找树可以将时间复杂度稳定在O(lgn)
- 散列表的构造要复杂，包括需要考虑扩容、缩容，散列函数设计等
- 二叉树有可能退化成链表结构，平衡二叉树--红黑树可以解决这个问题。