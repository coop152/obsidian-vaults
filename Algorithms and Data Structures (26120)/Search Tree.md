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
	if node.isInternal():
		preorder(node.left)
		preorder(node.right)
```