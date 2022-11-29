---
title: interview-tree
date: 2020-04-16 10:24:14
mathjax: true
categories:    
- 面试
tags:
- 树
- 数据结构
toc: true
---
# 树

## 二叉树
L、D、R分别表示遍历左子树、访问根节点和遍历右子树
* 先序遍历：DLR
* 中序遍历：LDR
* 后序遍历：LRD

 >确定一个二叉树，必须有中序遍历
## 二叉树的性质
* **性质1**：在二叉树中第i层的结点数最多为$2^{i - 1}（i \geq 1）$
* **性质2**：高度为k的二叉树节点其结点总数最多为$2^{k} - 1（k \geq 1）$
* **性质**3：对任意的非空二叉树T，如果叶节点的个数为$n_{0}$，而其度为2的结点数为$n_{2}$，则：$n{0} = n_{2} + 1$

## 满二叉树
深度为k，且有$2^{k} - 1$个节点称为满二叉树；
* **性质4**：第i层上的结点数为$2^{i - 1}$；

## 完全二叉树
深度为k，有n个节点的二叉树，当且仅当其每一个节点都与深度为k的满二叉树中，序号为1至n的节点对应时，称之为完全二叉树。
* **性质5**：对于具有n个结点的完全二叉树的高度为$log^{n}_{2} + 1$
  
二叉树的构造
```python
class TreeNode:
    def __init__(self, val=None):
        self.val = val
        self.left = None
        self.right = None
        self.parent = None

class Tree:
    def __init__(self, data: , n): #
        if n >= len(data):
            return 
        if data[n] == '#':
            return
        node = TreeNode()
        node.val = data[n]
        
        node.left = Tree(data, n + 1)
        node.right = Tree(data, n + 2)
        return node
```

