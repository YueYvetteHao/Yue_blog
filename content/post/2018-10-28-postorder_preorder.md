---
title: "Traversing nodes in a phylogenetic tree"
date: 2018-10-28
categories:
- blog
tags:
- python
- tree traversal
- phylogeny
thumbnailImagePosition: left
thumbnailImage: https://i.imgur.com/CBynhOY.jpg
comments: true
showSocial: true
gallery:
- https://i.imgur.com/dAJS7Fo.jpg
---

Converting internal node numbering from postorder to preorder in a bifurcating tree using [`DendroPy`](https://dendropy.org/).
<!--more-->

In a tree [structure](https://en.wikipedia.org/wiki/Tree_traversal), we can visit each node in different orders: inorder (left subtree → root → right subtree), preorder (root → left subtree → right subtree), postorder (left subtree → right subtree → root), etc. A detailed introduction to tree traversal algorithms can be found 
[here](https://www.geeksforgeeks.org/tree-traversals-inorder-preorder-and-postorder/).<br>

For example, we have a tree with five species: ((A,B),(C,(D,E)));
![tree](https://i.imgur.com/dAJS7Fo.jpg)
In the above tree, if we visit every node (including tips and internal nodes) in preorder, we will start at the root, and always visit left branch before right branch:
```
Preorder  1  2  3  4  5  6  7  8  9   
Nodes     R* X* A  B  Y* C  Z* D  E
```
*: internal nodes<br>

If following postorder, we would go to left subtree, then right subtree, and visit the root in the end:
```
Postorder 1  2  3  4  5  6  7  8  9
Nodes     A  B  X* C  D  E  Z* Y* R*
```
Sometimes in phylogenetics, nodes are indexed slightly differently: we would want to visit the tips of a tree first, and then the internal nodes would be in preorder or postorder, depending on the program.<br>
In the [`ggtree`](https://bioconductor.org/packages/release/bioc/html/ggtree.html) R package, internal nodes in a tree are always in **preorder**. However, in one of our datasets, the internal nodes were indexed in **postorder**. To plot the tree correctly, I will need to convert the internal node numbers:
```
Postorder_internal 1  2  3  4  5  6  7  8  9
Nodes              A  B  C  D  E  X* Z* Y* R*

Preorder_internal  1  2  3  4  5  6  7  8  9
Nodes              A  B  C  D  E  R* X* Y* Z*
```
Here is a python script to obtain both postorder and preorder internal node numbers using the [`DendroPy`](https://dendropy.org/) library:
{{< codeblock "internalnodes_postorder_preorder.py" "python" >}}
#!/usr/bin/python
# Convert internal nodes numbers from postorder to preorder
import dendropy
import sys

tree = dendropy.Tree.get(
    path=sys.argv[1],
    schema='newick')

Postorder = {}
num = int(sys.argv[2]) #number of species
for node in tree.postorder_node_iter():
    node = str(node)
    Arg = str("Taxon" in node)
    # internal nodes only
    if Arg != "True":
      num = num+1
      # unique node identifier as hash key
      Postorder[node[16:27]] = num
        
Preorder = {}
num = int(sys.argv[2])
for node in tree.preorder_node_iter():
    node = str(node)
    Arg = str("Taxon" in node)
    # internal nodes only
    if Arg != "True":
      num = num+1
      # unique node identifier as hash key
      Preorder[node[16:27]] = num

print "Postorder => Preorder"
for key,val in Postorder.items():
    print val, "\t", Preorder[str(key)]
{{< /codeblock >}}
Usage:`python internalnodes_postorder_preorder.py tree.tre 5`<br>
where `tree.tre` contains the tree file in newick format: `((A,B),(C,(D,E)));` and `5` is the number of species(tips) in the tree.<br>
The output of the python script will look like the following:
```
Postorder => Preorder
6 	7
7 	9
8 	8
9 	6
```