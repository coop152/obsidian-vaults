- Binary Tree
    - All nodes have at most 2 children
- General Tree
    - At least 1 node has more than 2 children
- Proper Binary Tree
    - Nodes have either 0 or 2 children
- Complete Binary Tree
    - All levels are full, except the last where all nodes are to the left
- Perfect Binary Tree
    - Tree is proper and all external nodes have same depth
## Tree Abstract Data Type
```python
root(): returns root of tree

isRoot(n): TRUE if n is root node

insert(e): Inserts element e into T

find(e): returns node storing e (or NULL)

remove(n): removes node n from T

parent(n): returns the parent node (or NULL)

children(n): returns the children nodes of n

elements(): returns all elements of T

isInternal(n): TRUE if n is internal

isExternal(n): TRUE if n is external
```

## Representing a (Binary) Tree
![](Pasted%20image%2020230127121136.png)
For a parent node at $i$, its left child is at $2i + 1$ and its right child is at $2i + 2$.
Can only represent trees with a fixed number of children! Also has lots of wasted space.
![](Pasted%20image%2020230127121503.png)
Each node links to its parent and its children (i.e. a doubly linked list). Can represent general trees, and doesn't waste as much space for non-complete trees.

## Traversal
#### Pre-order
```python
def preorder(node):
	output(node.value)
	if node.left is not None: preorder(node.left)
	if node.right is not None: preorder(node.right)
```
#### In-order
```python
def inorder(node):
	if node.left is not None: inorder(node.left)
	output(node.value)
	if node.right is not None: inorder(node.right)
```
#### Post-order
```python
def postorder(node):
	if node.left is not None: postorder(node.left)
	if node.right is not None: postorder(node.right)
	output(node.value)
```

## Complexity
Assuming evenly distributed elements, find insert and delete are all $\Theta(log{n})$. The worst case is $O(n)$, when the height of the tree is roughly equal to the number of elements.

## AVL Trees
An AVL tree is a BST but it can only be unbalanced by a max of one level. If it's too unbalanced it will be automatically balanced using rotations.