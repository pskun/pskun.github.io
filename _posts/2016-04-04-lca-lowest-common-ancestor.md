---
layout: post
title: 算法经典题目：二叉树两节点的公共祖先(LCA问题)
description: ""
category: algorithm
avatarimg: "/img/head.png"
tags : [二叉树, LCA]
duoshuo: true
---

## 题目
----------
定义二叉树的节点定义如下:

{% highlight cpp %}

struct TreeNode {
	int val;
	TreeNode *left;
	TreeNode *right;
	TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

{% endhighlight %}

给定二叉树中的两个节点，输出这两个节点的最低公共祖先节点。

## 二叉查找树
----------
如果这棵树是二叉查找树，那么从树根开始：

- 如果当前结点t 大于结点u、v，说明u、v都在t 的左侧，所以它们的共同祖先必定在t 的左子树中，故从t 的左子树中继续查找；
- 如果当前结点t 小于结点u、v，说明u、v都在t 的右侧，所以它们的共同祖先必定在t 的右子树中，故从t 的右子树中继续查找；
- 如果当前结点t 满足 u <t < v，说明u和v分居在t 的两侧，故当前结点t 即为最近公共祖先；
- 而如果u是v的祖先，那么返回u的父结点，同理，如果v是u的祖先，那么返回v的父结点。

## 递归思路
----------
如果给出根节点，最简单的想法是用递归求解，代码也较简单。如下：

{% highlight cpp %}

TreeNode* lcaRecursion(TreeNode* root, TreeNode* p, TreeNode* q) {
	if(!root) return NULL;
	if(root == p || root == q) return root;
	TreeNode* l = lcaRecursion(root->left, p, q);
	TreeNode* r = lcaRecursion(root->right, p, q);
	// p、q分别是root的左右子树 
	if(l && r) return root;
	// p、q在root的左子树中 
	if(!r) return l;
	// p、q在root的右子树中 
	else return r;
}

{% endhighlight %}

在这里，时间复杂度是O(n)。上面的方法还是有所局限的，必须保证两个要查找的节点n1和n2都出现在树中。如果n1不在树中，则会返回n2为LCA，理想答案应该为NULL。要解决这个问题，可以先查找下 n1和n2是否出现在树中，然后加几个判断即可。

## 找公共路径思路
----------
找公共路径的思路也很简单

1. 找从根到p的路径，并存储
2. 找从根到q的路径，并存储
3. 遍历这两条路径，找第一个不同的节点，则第一个不同的节点之前那个节点是最近公共祖先。代码如下：

{% highlight cpp %}

bool findNodePathFromRoot(TreeNode* root, TreeNode* node, vector<TreeNode*> &path) {
	if(!node) return false;
	if(!root) return false;
	path.push_back(root);
	if(root == node) return true;
	if(findPath(root->left, node, path) || findPath(root->right, node, path)) return true;
	path.pop_back();
	return false;
}

TreeNode* lcaFindPath(TreeNode* root, TreeNode* p, TreeNode* q) {
	vector<TreeNode*> p1, p2;
	bool findP = findNodePathFromRoot(root, p, p1);
	bool findQ = findNodePathFromRoot(root, q, p2);
	if(findP && findQ) {
		TreeNode* lca = NULL;
		int n = p1.size() < p2.size() ? p1.size() : p2.size();
		for(int i=0; i<n; i++) {
			if(p1[i] != p2[i]) break;
			else lca = p1[i];
		}
		return lca;
	}
	return NULL;
}

{% endhighlight %}

这个思路需要遍历2次树，比一次遍历的递归稍微复杂，空间复杂度是O(n)，时间复杂度仍是O(n)。

上面的两种解法有一个很大的弊端就是：如需N 次查询，则总体复杂度会扩大N 倍，故这种暴力解法仅适合一次查询，不适合多次查询。

接下来的解法，是把LCA问题看成是询问式的，即给出一系列询问，程序对每一个询问尽快做出反应。故处理这类问题一般有两种解决方法：
- 一种是离线的算法，Trajan算法，相当于一次批处理，一开始就知道了全部查询结果。
- 一种是在线的算法，RMQ算法，每次读入一个查询，处理这个查询，给出答案。

## Trajan算法
----------
Tarjan算法（以发现者Robert Tarjan命名）是一个在图中寻找强连通分量的算法。算法的基本思想为：任选一结点开始进行深度优先搜索dfs（若深度优先搜索结束后仍有未访问的结点，则再从中任选一点再次进行）。搜索过程中已访问的结点不再访问。搜索树的若干子树构成了图的强连通分量。其中，强连通图的结果保存可以使用并查集。

算法用集合表示一类节点，这些节点跟集合外的点的LCA都一样，并把这个LCA设为这个集合的祖先。当搜索到节点x时，创建一个由x本身组成的集合，这个集合的祖先为x自己。然后递归搜索x的所有儿子节点。当一个子节点搜索完毕时，把子节点的集合与x节点的集合合并，并把合并后的集合的祖先设为x。因为这棵子树内的查询已经处理完，x的其他子树节点跟这棵子树节点的LCA都是一样的，都为当前根节点x。所有子树处理完毕之后，处理当前根节点x相关的查询。遍历x的所有查询，如果查询的另一个节点v已经访问过了，那么x和v的LCA即为v所在集合的祖先。

如下图的示例：

![](http://7s1sd9.com1.z0.glb.clouddn.com/lca_example.jpg)

假设遍历完10的孩子,要处理关于10的请求了，取根节点到当前正在遍历的节点的路径为关键路径,即1-3-8-10，集合的祖先便是关键路径上距离集合最近的点。

比如：

- 1，2，5，6为一个集合,祖先为1，集合中点和10的LCA为1
- 3，7为一个集合，祖先为3，集合中点和10的LCA为3
- 8，9，11为一个集合，祖先为8，集合中点和10的LCA为8
- 10，12为一个集合，祖先为10，集合中点和10的LCA为10

得出的结论便是：LCA(u,v)便是根至u的路径上到节点v最近的点。

LCA Tarjan基本框架：

1. 先用随便一种数据结构(链表就行)，把关于某个点的所有询问标在节点上，保证遍历到一个点，能得到所有有关这个节点LCA 查询
2. 建立并查集。注意：这个并查集只可以把叶子节点并到根节点，即getf(x)得到的总是x的祖先
3. 深度优先遍历整棵树，用一个Visited数组标记遍历过的节点，每遍历到一个节点将Visite[i]设成True 处理关于这个节点(不妨设为A)的询问，若另一节点(设为B)的Visited[B]==True，则回应这个询问，这个询问的结果就是getf(B)；否则什么都不做。
4. 当A所有子树都已经遍历过之后，将这个节点用并查集并到他的父节点(其实这一步应该说当叶子节点回溯回来之后将叶子节点并到自己，并DFS另一子树)
5. 当一颗子树遍历完时，这棵子树的内部查询(即LCA在这棵子树内部)都已经处理了

具体的图示过程可以参考：[最近公共祖先LCA问题](http://taop.marchtea.com/04.04.html)

若二叉树有n个节点，批查询量为Q，那么算法的时间复杂度为O(n+Q)。

代码如下：

{% highlight cpp %}

unordered_map<TreeNode*,TreeNode*> parent;        
unordered_map<TreeNode*,bool> visited;
TreeNode* left;
TreeNode* right;
TreeNode* ans;

TreeNode* find(TreeNode* x) {
    return parent[x] = parent[x] == x ? x : find(parent[x]);
}

void union(TreeNode* x, TreeNode* y){
    TreeNode* a = parent[x];
    TreeNode* b = parent[y];
    parent[b] = a;
}

void lcaTrajanFind(TreeNode* u) {
    if(u == NULL)
        return;
    parent[u] = u;
    if(u->left) {
        lcaTrajanFind(u->left);
        union(u, u->left);
    }
    if(u->right){
        lcaTrajanFind(u->right);
        union(u,u->right);
    }
    visited[u] = true;
    if(u==left && visited.find(right)!=visited.end()){
        ans = find(right);
    }
    else if(u == right && visited.find(left)!=visited.end()){
        ans = find(left);
    }
}

TreeNode* lcaTrajan(TreeNode* root, TreeNode* p, TreeNode* q) {
    left = p;
    right = q;
    lcaTrajanFind(root);
    return ans;
}

{% endhighlight %}

## RMQ算法
----------
待续

## 线段树算法
----------
待续

## 参考资料
----------
- [geeksforgeeks.org/lowest-common-ancestor-binary-tree-set-1/](http://www.geeksforgeeks.org/lowest-common-ancestor-binary-tree-set-1/)
- [geeksforgeeks.org//find-lca-in-binary-tree-using-rmq/](http://www.geeksforgeeks.org/find-lca-in-binary-tree-using-rmq/)
- [taop.marchtea.com/04.04.html](http://taop.marchtea.com/04.04.html)
- [comzyh.com/blog/archives/492/](https://comzyh.com/blog/archives/492/)