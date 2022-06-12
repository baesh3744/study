# 이진 트리 (Binary Tree)

## 이진 트리

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None

    def __str__(self):
        return str(self.data)

class BinaryTree:
    def __init__(self):
        self.root = None

    # 전위 순회
    def preorder_traversal(self, node):
        print(node, end='')
        if not node.left  == None : self.preorderTraversal(node.left)
        if not node.right == None : self.preorderTraversal(node.right)

    # 중위 순회
    def inorder_traversal(self, node):
        if not node.left  == None : self.preorderTraversal(node.left)
        print(node, end='')
        if not node.right == None : self.preorderTraversal(node.right)

    # 후위 순회
    def postorder_traversal(self, node):
        if not node.left  == None : self.preorderTraversal(node.left)
        if not node.right == None : self.preorderTraversal(node.right)
        print(node, end='')

    def add_node(self, node, left_node, right_node):
        if self.root == None:
            self.root = node
        node.left = left_node
        node.right = right_node

if __name__ == "__main__":
    node = []
    node.append(Node('A'))
    node.append(Node('B'))
    node.append(Node('C'))
    node.append(Node('D'))
    node.append(Node('E'))
    node.append(Node('F'))
    node.append(Node('G'))

    my_tree = BinaryTree()
    for i in range(len(node) // 2):
        my_tree.add_node(node[i], node[2*i+1], node[2*i+2])

    print(       '전위 순회: ', end=''); my_tree.preorder_traversal(my_tree.root)
    print('\n' + '중위 순회: ', end=''); my_tree.inorder_traversal(my_tree.root)
    print('\n' + '후위 순회: ', end=''); my_tree.postorder_traversal(my_tree.root)

# 전위 순회: ABDECFG
# 중위 순회: DBEAFCG
# 후위 순회: DEBFGCA
```

-   이진 트리의 순회는 재귀 호출을 사용하므로, 시스템 혹은 외부 스택을 관리해야 하며 하위 레벨로 내려갈수록 재귀 호출의 깊이가 깊어져 비효율적일 수 있다.

<br>

## 스레드 이진 트리

-   스레드 이진 트리는 순회에서 스택을 이용하지 않고 포인터를 이용하여 순회의 시간을 단축시키는 방식이다.

-   스레드 이진 트리에서는 서브 트리가 존재하지 않는 노드의 링크를 순회 경로에 따라 선행자 또는 후행자로 지정하여 재귀 호출을 사용하지 않고 포인터를 이용하여 순회할 수 있다.

-   스레드 이진 트리는 중위 순회의 편의성을 극대화한다.

```python
# 서브 트리가 존재하지 않는 노드의 오른쪽 링크를 후행자로 선택하여 중위 순회를 구현한다.

class Node:
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None
        self.is_thread_right = None  # 오른쪽 링크가 실제 오른쪽 서브 트리(False)인지 후행자의 링크(True)인지 boolean 형태로 저장한다.

    def __str__(self):
        return str(self.data)

class ThreadBinaryTree:
    def __init__(self):
        self.root = None

    def inorder_traversal(self, node):
        while not node.left == None:
            node = node.left
        print(node, end='')
        while True:
            node = self.find_thread(node)
            print(node, end='')
            if node.right == None:
                break

    def find_thread(self, node):
        pre_node = node
        node = node.right
        if node == None:
            return node
        if pre_node.is_thread_right:
            return node
        while not node.left == None:
            node = node.left
        return node

    def add_node(self, node, left_node, right_node, thread):
        if self.root == None:
            self.root = node
        node.left = left_node
        node.right = right_node
        node.is_tread_right = thread
```

<br>

## 이진 탐색 트리

이진 탐색 트리는 다음과 같은 규칙을 가지고 있다.

-   모든 원소는 서로 다른 키를 가진다.

-   왼쪽 서브 트리에 있는 원소들은 루트의 키보다 작다.

-   오른쪽 서브 트리에 있는 원소들은 루트의 키보다 크다.

-   왼쪽 서브 트리와 오른쪽 서브 트리도 이진 탐색 트리이다.

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None

    def __str__(self):
        return str(self.data)

class BinarySearchTree:
    def __init__(self):
        self.root = None

    # 원소 삽입
    def insert_element(self, data):
        new_node = Node(data)
        if self.root == None:
            self.root = new_node

        node = self.root
        while True:
            pre_node = node
            if node.data > new_node.data:
                node = node.left
                if node == None:
                    node = new_node
                    pre_node.left = node
            elif node.data < new_node.data:
                node = node.right
                if node == None:
                    node = new_node
                    pre_node.right = node
            else:
                return

    # 원소 탐색
    def search_element(self, data):
        node = self.root
        while True:
            if node.data > data:
                node = node.left
            elif node.data < data:
                node = node.right
            elif node.data == data:
                break
            else:
                return Node('탐색 결과 없음')

        return node

    # 전위 순회
    def preorder_traversal(self, node):
        print(node, end='')
        if not node.left  == None : self.preorderTraversal(node.left)
        if not node.right == None : self.preorderTraversal(node.right)

    # 중위 순회
    def inorder_traversal(self, node):
        if not node.left  == None : self.preorderTraversal(node.left)
        print(node, end='')
        if not node.right == None : self.preorderTraversal(node.right)

    # 후위 순회
    def postorder_traversal(self, node):
        if not node.left  == None : self.preorderTraversal(node.left)
        if not node.right == None : self.preorderTraversal(node.right)
        print(node, end='')

    def add_node(self, node, left_node, right_node):
        if self.root == None:
            self.root = node
        node.left = left_node
        node.right = right_node
```

-   이진 탐색 트리의 문제는 편향 트리 (한 쪽으로 치우쳐진 트리) 가 만들어질 수 있다는 것이다. 편향 트리의 탐색은 매우 비효율적이다.

<br>

## Reference

https://blex.me/@baealex/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%9C%BC%EB%A1%9C-%EA%B5%AC%ED%98%84%ED%95%9C-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%ED%8A%B8%EB%A6%AC
