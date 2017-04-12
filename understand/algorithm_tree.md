## 理解二叉搜索树BST，自平衡二叉树AVL，红黑树

### 二叉搜索树BST

像栈，队列，链表都是顺序数据结构，哈希表和树是非顺序数据结构。树是一种分层数据的抽象模型，存储需要快速查找的数据非常有用。

#### 了解树的术语

* 树的定义： 一个树结构包含一系列存在父子关系的节点，除根节点外，每个节点都有一个父节点。
* 叶节点：没有子节点的都是叶子节点
* 子树：子树由节点和它的后代构成。
* 节点的深度：节点的深度取决于它的祖先节点的数量
* 树的高度：树的高度取决于所有节点深度的最大值
* 键：键是树相关术语中对节点的称号，下面说键就是指节点

如下图：
![](../imgs/tree.png)

#### 二叉树和二叉搜索树(BinarySearchTree)
二叉树是每个节点最多只有两个子节点，而二叉搜索树是特殊的二叉树，要求右侧子节点存储比父节点大的值，左侧子节点存储比父节点小的值，这个特性对快速查找数据非常有用。


#### 用Javascript实现二叉搜索树

二叉搜索树的结构图如下：
![](../imgs/tree2.png)

代码：
```
function BST(){
    var Node = function(key){
        this.key = key;
        this.left = null;  // 指向左侧子节点
        this.right = null; // 指向右侧子节点
    }

    var root = null; // 根节点
}
```

和双向链表一样，每个节点包含2个指针，分别指向左右两侧子节点。

二叉搜索树需要实现下面方法：  
1. insert(key): 向树中插入新键  
2. search(key): 查找一个键，存在返回true,否则false  
3. min(): 返回树中最小的键值  
4. max()：返回树中最大的键值  

下面是实现后的代码：

```
function BST(){
    var Node = function(key){
        this.key = key;
        this.left = null;  // 指向左侧子节点
        this.right = null; // 指向右侧子节点
    }

    var root = null; // 根节点

    this.insert = function(key){
        var newNode = new Node(key);
        if(root === null){
            root = newNode;
        }else{
            this.inserNode(root, newNode);
        }
    }

    this.inserNode = function(node, newNode){
        //新节点的值比父节点值小，左子节点
        if(newNode.key < node.key){
            //是叶子节点，插入
            if(node.left === null){
                node.left = newNode;
            }else{
                //非叶子节点，递归
                this.inserNode(node.left, newNode);
            }
        }else{
            // 右子节点
            if(node.right === null){
                node.right = newNode;
            }else{
                this.inserNode(node.right, newNode);
            }
        }
    }

    this.min = function(){
        var node = root;
        if(node){
            while(node && node.left !== null){
                node = node.left; //一直找到最左侧的叶子节点
            }
            return node.key;
        }
        return null;
    }

    this.max = function(){
        var node = root;
        if(node){
            while(node && node.right !== null){
                node = node.right;  //一直找到最右侧的叶子节点
            }
            return node.key;
        }
        return null;
    }

    this.search = function(key){
        return this.searchNode(root, key);
    }

    this.searchNode = function(node, key){
        if(node === null){
            return false;
        }

        if(key < node.key){
            //递归查找左侧子节点
            return this.searchNode(node.left, key);
        }else if(key > node.key){
            //递归查找右侧子节点
            return this.searchNode(node.right, key);
        }else{
            return true;
        }
    }

    this.toString = function(){
        var current = root;
        //输出根节点
        this.showNode(current);

        var leftNode = current.left;
        var rightNode = current.right;

        while(leftNode.left){
            this.showNode(leftNode);
            leftNode = leftNode.left;
        }

        while(rightNode.right){
            this.showNode(rightNode);
            rightNode = rightNode.right;
        }
    }

    this.showNode = function(node){
        console.log("父节点："+ node.key + "，它的左右字节点："+node.left.key+","+node.right.key);
    }
}
```

我们来测试下：

```
var bst = new BST();
bst.insert(11);
bst.insert(7);
bst.insert(3);
bst.insert(9);
bst.insert(15);
bst.insert(13);
bst.insert(20);

bst.toString();

console.log(bst.min());
console.log(bst.max());
console.log(bst.search(15));
console.log(bst.search(100));
```

输出：
```
父节点：11，它的左右字节点：7,15
父节点：7，它的左右字节点：3,9
父节点：15，它的左右字节点：13,20
3
20
true
false
```

我们已经实现了二叉搜索树的数据结构，但BST存在一个问题： 添加的节点数越多，树的一边高度可能非常深，另一边可能比较浅，不均衡，会影响查找性能，下面会介绍一种叫做`自平衡二叉搜索树AVL`


### 自平衡二叉搜索树AVL


https://segmentfault.com/a/1190000008619190
