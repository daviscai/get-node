## 理解二叉树遍历（先序，中序，后序）

树的遍历一般有3种，按访问父节点的先后顺序分为：
1. 先序遍历，总是先访问父节点->左子节点->右子节点
2. 中序遍历，总是先访问左子节点->父节点->右子节点
3. 后序遍历，总是访问右子节点->左子节点->父节点

简单记住，访问父节点的顺序就是对应哪种遍历方式，先访问就是先序，中间访问就是中序，最后访问就是后序。

### 递归遍历实现

在另一篇介绍二叉树的文章代码基础上添加树的遍历

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

    // 先序遍历
    this.preOrder = function(callback){
        this.preOrderNode(root, callback);
    }

    this.preOrderNode = function(node, callback){
        if(node === null){
            return false;
        }
        callback(node); //先访问父节点
        this.preOrderNode(node.left, callback);  // 访问左子节点
        this.preOrderNode(node.right, callback); // 访问右子节点
    }

    // 中序遍历
    this.midOrder = function(callback){
        this.midOrderNode(root, callback);
    }

    this.midOrderNode = function(node, callback){
        if(node === null){
            return false;
        }
        this.midOrderNode(node.left, callback);  // 先访问左子节点
        callback(node); //访问父节点
        this.midOrderNode(node.right, callback); // 访问右子节点
    }

    // 后序遍历
    this.postOrder = function(callback){
        this.postOrderNode(root, callback);
    }

    this.postOrderNode = function(node, callback){
        if(node === null){
            return false;
        }
        this.postOrderNode(node.left, callback);  // 先访问左子节点
        this.postOrderNode(node.right, callback); // 访问右子节点
        callback(node); //访问父节点
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

测试代码：
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

bst.preOrder(function(node){
    if(node){
        console.log('先序节点：'+node.key);
    }

});

bst.midOrder(function(node){
    if(node){
        console.log('中序节点：'+node.key);
    }
});

bst.postOrder(function(node){
    if(node){
        console.log('后序节点：'+node.key);
    }
});

```

输出：
```
父节点：11，它的左右字节点：7,15
父节点：7，它的左右字节点：3,9
父节点：15，它的左右字节点：13,20

先序节点：11
先序节点：7
先序节点：3
先序节点：9
先序节点：15
先序节点：13
先序节点：20

中序节点：3
中序节点：7
中序节点：9
中序节点：11
中序节点：13
中序节点：15
中序节点：20

后序节点：3
后序节点：9
后序节点：7
后序节点：13
后序节点：20
后序节点：15
后序节点：11
```

从输出的结果看，很明显3种遍历方式的顺序是不一样的，看下图会更加直观明显：



递归方式实现时间复杂度为O(n)，空间复杂度为O(logn), 下面这种方式不采用递归，空间复杂度是O(1)

### Morris遍历实现

>1968年，Knuth提出说能否将该问题的空间复杂度压缩到O(1)，同时原树的结构不能改变。大约十年后，1979年，Morris在《Traversing Binary Trees Simply and Cheaply》这篇论文中用一种Threaded Binary Tree的方法解决了该问题。

在Morris遍历算法中，通过修改叶子结点的左右空指针来指向其前驱或者后继结点来实现的。

##### Morris先序

寻找每个节点的前驱节点，并根据前驱节点右子树是否为空来决定当前节点是否被访问过。

```
var morrisPre = function(head) {
 if(!head) {  
  return
 }
 var cur1 = head,
   cur2 = null
 while(cur1) {
  cur2 = cur1.left  
  if(cur2) {   
   while(cur2.right && cur2.right != cur1) {
    cur2 = cur2.right
   }   
   if(!cur2.right) {
    cur2.right = cur1    
    console.log(cur1.value)
    cur1 = cur1.left    
    continue
   } else {
    cur2.right = null
   }
  } else {   
    console.log(cur1.value)
  }
  cur1 = cur1.right
 }
}
```

##### Morris中序

```
var morrisIn = function(head) {
 if(!head) {  
  return
 }
 var cur1 = head,
   cur2 = null
 while(cur1) {
  cur2 = cur1.left  
  if(cur2) {   
   while(cur2.right && cur2.right !== cur1) {
    cur2 = cur2.right
   }   
   if(!cur2.right) {
    cur2.right = cur1
    cur1 = cur1.left    
    continue
   } else {
    cur2.right = null
   }
  }  
  console.log(cur1.value)
  cur1 = cur1.right
 }
}
```

##### Morris后序

```
var morrisPost = function(head) {
  if(!head) {
    return
  }
  var cur1 = head,
      cur2 = null
  while(cur1) {
    cur2 = cur1.left
    if(cur2) {
      while(cur2.right && cur2.right !== cur1) {
        cur2 = cur2.right
      }
      if(!cur2.right) {
        cur2.right = cur1
        cur1 = cur1.left
        continue
      } else {
        cur2.right = null
        printEdge(cur1.left)
      }
    }
    cur1 = cur1.right
  }
  printEdge(head)
}

var printEdge = function(head) {
  var tail = reverseEdge(head)
  var cur = tail
  while(cur) {
    console.log(cur.value)
    cur = cur.right
  }
  reverseEdge(tail)
}

var reverseEdge = function(head) {
  var pre = null,
      next = null
  while(head) {
    next = head.right
    head.right = pre
    pre = head
    head = next
  }
  return pre
}
```

完整代码：
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

    // 先序遍历
    this.morrisPre = function(callback) {
      head = root;
      if(!head) {
        return
      }
      var cur1 = head,
          cur2 = null
      while(cur1) {
        cur2 = cur1.left
        if(cur2) {
          while(cur2.right && cur2.right != cur1) {
            cur2 = cur2.right
          }
          if(!cur2.right) {
            cur2.right = cur1
            callback(cur1);   // console.log(cur1.value)
            cur1 = cur1.left
            continue
          } else {
            cur2.right = null
          }
        } else {
          callback(cur1); // console.log(cur1.value)
        }
        cur1 = cur1.right
      }
    }

    // 中序遍历
    this.morrisIn = function(callback) {
      head = root;
      if(!head) {
        return
      }
      var cur1 = head,
          cur2 = null
      while(cur1) {
        cur2 = cur1.left
        if(cur2) {
          while(cur2.right && cur2.right !== cur1) {
            cur2 = cur2.right
          }
          if(!cur2.right) {
            cur2.right = cur1
            cur1 = cur1.left
            continue
          } else {
            cur2.right = null
          }
        }
        callback(cur1);  // console.log(cur1.value)
        cur1 = cur1.right
      }
    }

    //后序遍历
    this.morrisPost = function(callback) {
      head = root;
      if(!head) {
        return
      }
      var cur1 = head,
          cur2 = null
      while(cur1) {
        cur2 = cur1.left
        if(cur2) {
          while(cur2.right && cur2.right !== cur1) {
            cur2 = cur2.right
          }
          if(!cur2.right) {
            cur2.right = cur1
            cur1 = cur1.left
            continue
          } else {
            cur2.right = null
            this.printEdge(cur1.left, callback)
          }
        }
        cur1 = cur1.right
      }
      this.printEdge(head, callback)
    }

    this.printEdge = function(head, callback) {
      var tail = this.reverseEdge(head)
      var cur = tail
      while(cur) {
        callback(cur); //console.log(cur.value)
        cur = cur.right
      }
      this.reverseEdge(tail)
    }

    this.reverseEdge = function(head) {
      var pre = null,
          next = null
      while(head) {
        next = head.right
        head.right = pre
        pre = head
        head = next
      }
      return pre;
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

测试代码：
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

bst.morrisPre(function(node){
    if(node){
        console.log('先序节点：'+node.key);
    }

});

bst.morrisIn(function(node){
    if(node){
        console.log('中序节点：'+node.key);
    }
});

bst.morrisPost(function(node){
    if(node){
        console.log('后序节点：'+node.key);
    }
});
```

输出：
```
父节点：11，它的左右字节点：7,15
父节点：7，它的左右字节点：3,9
父节点：15，它的左右字节点：13,20

先序节点：11
先序节点：7
先序节点：3
先序节点：9
先序节点：15
先序节点：13
先序节点：20

中序节点：3
中序节点：7
中序节点：9
中序节点：11
中序节点：13
中序节点：15
中序节点：20

后序节点：3
后序节点：9
后序节点：7
后序节点：13
后序节点：20
后序节点：15
后序节点：11
```

可以看到，输出结果跟递归方式实现一样，空间复杂度小很多。

延伸阅读：  
http://blog.codinghonor.com/2014/11/26/morris-traversal/
https://segmentfault.com/a/1190000004620352
