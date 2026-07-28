# Code: Stacks and Queues

## The Node Class

**Java**
```java
class Node {
    int value;
    Node next;

    Node(int value) {
        this.value = value;
    }
}
```

**Python**
```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None
```

## The Stack Class and Its Constructor

**Java**
```java
class Stack {
    Node top;
    int height;

    Stack(int value) {
        Node newNode = new Node(value);
        top = newNode;
        height = 1;
    }
}
```

**Python**
```python
class Stack:
    def __init__(self, value):
        new_node = Node(value)
        self.top = new_node
        self.height = 1
```

## push

**Java**
```java
void push(int value) {
    Node newNode = new Node(value);

    if (height == 0) {
        top = newNode;
    } else {
        newNode.next = top;
        top = newNode;
    }

    height++;
}
```

**Python**
```python
def push(self, value):
    new_node = Node(value)

    if self.height == 0:
        self.top = new_node
    else:
        new_node.next = self.top
        self.top = new_node

    self.height += 1
```

## pop

**Java**
```java
Node pop() {
    if (height == 0) {
        return null;
    }

    Node temp = top;
    top = top.next;
    temp.next = null;
    height--;
    return temp;
}
```

**Python**
```python
def pop(self):
    if self.height == 0:
        return None

    temp = self.top
    self.top = self.top.next
    temp.next = None
    self.height -= 1
    return temp
```

## The Queue Constructor

**Java**
```java
class Queue {
    Node first;
    Node last;
    int length;

    Queue(int value) {
        Node newNode = new Node(value);
        first = newNode;
        last = newNode;
        length = 1;
    }
}
```

**Python**
```python
class Queue:
    def __init__(self, value):
        new_node = Node(value)
        self.first = new_node
        self.last = new_node
        self.length = 1
```

## enqueue

**Java**
```java
void enqueue(int value) {
    Node newNode = new Node(value);

    if (length == 0) {
        first = newNode;
        last = newNode;
    } else {
        last.next = newNode;
        last = newNode;
    }

    length++;
}
```

**Python**
```python
def enqueue(self, value):
    new_node = Node(value)

    if self.length == 0:
        self.first = new_node
        self.last = new_node
    else:
        self.last.next = new_node
        self.last = new_node

    self.length += 1
```

## dequeue

**Java**
```java
Node dequeue() {
    if (length == 0) {
        return null;
    }

    Node temp = first;

    if (length == 1) {
        first = null;
        last = null;
    } else {
        first = first.next;
        temp.next = null;
    }

    length--;
    return temp;
}
```

**Python**
```python
def dequeue(self):
    if self.length == 0:
        return None

    temp = self.first

    if self.length == 1:
        self.first = None
        self.last = None
    else:
        self.first = self.first.next
        temp.next = None

    self.length -= 1
    return temp
```
