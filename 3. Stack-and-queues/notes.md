# Lecture: Stacks and Queues

- [Welcome!](#welcome)
- [Stacks](#stacks)
- [Implementing a Stack](#implementing-a-stack)
- [The Node Class](#the-node-class)
- [The Stack Class and Its Constructor](#the-stack-class-and-its-constructor)
- [push](#push)
- [pop](#pop)
- [Queues](#queues)
- [Implementing a Queue](#implementing-a-queue)
- [The Queue Constructor](#the-queue-constructor)
- [enqueue](#enqueue)
- [dequeue](#dequeue)
- [Summing Up](#summing-up)

---

## Welcome!

In previous lessons, we built a linked list and got comfortable with nodes, pointers, and traversal.

This week, we are going to take those same building blocks and shape them into two new data structures: **stacks** and **queues**.

Fundamentally, both structures are about restricting *how* you can add and remove items, and that restriction is exactly what makes them useful.

By the end of this lesson, you will be able to build a stack and a queue from scratch, and you'll know which end of a linked list to use so that every operation stays fast.

---

## Stacks

My favorite analogy for a stack is a can of tennis balls.

Imagine removing all the balls, then adding one back in. This is called **pushing** an item onto the stack. You can then push a second ball on top of the first.

The thing that makes a stack a stack is this: **you can only ever get to the last item you pushed on.**

As soon as you push that second ball on, you can't get to the first one without removing the second one first. Push a third ball on, and now the second one is out of reach too. The only ball you can reach is the one on top.

This behavior is called **LIFO**: **L**ast **I**n, **F**irst **O**ut.

Only once you remove that third ball can you reach the second one, and only once you remove the second can you reach the first.

> **Real-world example:** a web browser. You visit Facebook, then YouTube, then Instagram, then check your email. Behind the scenes, your browser has been building a stack of every site you've visited. When you hit the back button, you are **popping** an item off that stack — it's only by removing the top item that you can hit back again and reach the next one underneath.

---

## Implementing a Stack

A common way to implement a stack is with an `ArrayList`.

An `ArrayList` works better than a plain array here because we don't know in advance how many items we'll be pushing onto the stack, so we want a dynamic data structure that can grow.

Technically, you could use either end of the `ArrayList` for adding and removing items. The only real requirement of a stack is that you always add and remove from the **same** end.

| Operate on this end | push / pop cost |
|---|---|
| The end (last index) | O(1) |
| The beginning (index 0) | O(n) — every remaining item must be re-indexed |

So, if you use an `ArrayList` to implement a stack, always operate on the **end**, never the beginning.

Since stacks are usually drawn vertically, you can picture pushing as adding to the top and popping as removing from the top.

Another common way to implement a stack — and the one we'll use here — is with a **linked list**. Just like with an `ArrayList`, you can add and remove from either end of a linked list, as long as it's always the *same* end.

| Null-terminated end at... | Cost |
|---|---|
| The top | Remove: O(n) · Add: O(n) |
| The bottom | Remove: O(1) · Add: O(1) |

It is better, from a Big O perspective, to keep the null-terminated end at the **bottom**, so that every operation happens at the top in O(1) time.

We already wrote two linked list methods we can reuse here: `removeFirst` and `prepend`. For a stack, we'll rename these to `pop` and `push`.

Where a linked list had `head` and `tail`, a stack (represented vertically) will have `top` and `bottom`. But since a stack never adds or removes anything from the bottom, we don't even need a bottom pointer — we can get by with just `top`.

---

## The Node Class

Before we can build the stack itself, we need a node — just like the one we built for our linked list, with a `value` and a `next` pointer.

```java
class Node {
    int value;
    Node next;

    Node(int value) {
        this.value = value;
    }
}
```

> **Notice:** this node class is identical to the one used for linked lists. `this.value = value` takes the value passed into the constructor and stores it on the node itself.

---

## The Stack Class and Its Constructor

Now let's build the stack class. Where a linked list kept pointers called `head` and `tail`, a stack only needs `top` (no `bottom` pointer needed, as discussed above). Where a linked list kept track of `length`, a stack will call this same idea `height`.

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

> **Notice:** the constructor creates a node from the value passed in, points `top` at that new node, and sets `height` to `1`, since the stack now contains exactly one item.

A few helper methods — `printStack`, `getTop`, and `getHeight` — are useful for peeking at what's going on inside your stack as you test it.

```java
Stack myStack = new Stack(4);
myStack.getTop();     // Node with value 4
myStack.getHeight();  // 1
myStack.printStack(); // [4]
```

That's our stack constructor working as intended.

---

## push

Now let's build `push`, which adds a new item to the top of the stack. This is very similar to prepending an item to a linked list.

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

> **Notice** there are two situations to code for:
> 1. **Empty stack** (`height == 0`, meaning `top` is `null`) — we simply set `top` equal to the new node.
> 2. **Stack already has items** — `newNode.next = top` points the new node's `next` arrow at whatever `top` currently points to, then `top = newNode` moves `top` up to point at the new node. This adds the new node above the old top.
>
> Finally, in both cases, we increment `height` by one to reflect the new item.

```java
Stack myStack = new Stack(2);
myStack.push(1);
myStack.printStack(); // [1, 2]
```

That's our `push` method working correctly.

---

## pop

Now let's build `pop`, which removes and returns the top item of the stack.

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

> **Notice:**
> - We first handle the **empty-stack case**: if `height` is `0` (equivalently, `top` is `null`), we return `null` immediately, since there is nothing to pop.
> - For a **non-empty stack**, we need a variable pointing at the node we're about to return, so `temp = top`.
> - `top = top.next` moves the top pointer down to the node below it.
> - `temp.next = null` breaks the old top node off from the rest of the stack, so it no longer points into it.
> - Finally, we decrement `height` by one and return `temp`, the node we just removed.

```java
// Stack: 1 -> 3 -> 23 -> 7
myStack.pop();
myStack.printStack(); // [3, 23, 7]
```

That's our `pop` method.

---

## Queues

Now let's introduce queues.

A queue works just like a line of people: people can join the back of the line, and when you remove someone, you take the person from the **front** of the line.

This behavior is called **FIFO**: **F**irst **I**n, **F**irst **O**ut — as opposed to a stack's LIFO.

Two pieces of terminology come with queues: adding an item is called **enqueueing** it, and removing an item is called **dequeueing** it.

---

## Implementing a Queue

Just as we did with stacks, let's consider how to implement a queue with an `ArrayList` versus a linked list.

The only rule with a queue is that you must enqueue and dequeue on **opposite** ends.

| Structure | enqueue end | dequeue end | Result |
|---|---|---|---|
| `ArrayList` | either end | the other end | one side O(1), one side O(n) — unavoidable |
| Linked list | tail (`last`) | head (`first`) | **both O(1)** |

So, with a linked-list-based queue, you always want to enqueue on one end and dequeue on the other, so that both operations stay O(1).

Where a linked list had `head` and `tail`, a queue will rename these to `first` and `last`.

---

## The Queue Constructor

A queue is like a line: first in, first out. We'll draw it so the person first in line is on the left, and the person last in line is on the right.

The node class for a queue is identical to the one we used for linked lists and for stacks: a `value` and a `next` pointer.

The queue class itself has three class variables: `first` and `last`, pointing to the first and last items in the queue, and `length`, tracking how many items are in the queue.

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

> **Notice:** the constructor passes the given value to the node constructor to create a new node, sets both `first` and `last` to point at that same new node, and sets `length` to `1`, since the queue now has exactly one node in it.

As with our stack, helper methods like `printQueue`, `getFirst`, `getLast`, and `getLength` let us peek inside the queue as we test it.

```java
Queue myQueue = new Queue(7);
myQueue.getFirst();   // Node with value 7
myQueue.getLast();    // Node with value 7
myQueue.getLength();  // 1
myQueue.printQueue(); // [7]
```

That's our queue constructor.

---

## enqueue

Now let's build `enqueue`, which adds a new item to the **end** of the queue.

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

> **Notice** there are two situations to code for:
> 1. **Empty queue** (`length == 0`, equivalently `first` is `null`) — we set both `first` and `last` equal to the new node, since it is now the only item in the queue.
> 2. **Queue already has items** — `last.next = newNode` points the current last node's `next` arrow at the new node, then `last = newNode` moves the `last` pointer over to the new node. This adds the new item to the end of the queue.
>
> Finally, we increment `length` by one.

```java
Queue myQueue = new Queue(1);
myQueue.enqueue(2);
myQueue.printQueue(); // [1, 2]
```

That's our `enqueue` method.

---

## dequeue

Now let's build `dequeue`, which removes and returns the item at the **front** of the queue.

There are three scenarios to handle: an empty queue, a queue with exactly one item, and a queue with two or more items. The one-item case is the same edge case we saw with `removeFirst` on a linked list.

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

> **Notice:**
> - The return type is `Node`, since `dequeue` hands back a pointer to the node it removed.
> - **Empty queue** (`length == 0`, or `first` is `null`) — return `null` immediately.
> - We create `temp` and set it equal to `first`, since `temp` is the pointer we'll eventually return, and it already points at the correct node no matter which remaining scenario applies.
> - **Exactly one item** (`length == 1`) — set both `first` and `last` to `null`, since removing the only item leaves the queue empty.
> - **Two or more items** — move the `first` pointer over by one with `first = first.next`, then set `temp.next = null` to break the removed node off from the rest of the queue.
> - Finally, in either non-empty case, decrement `length` by one and return `temp`.

```java
// Queue: [2, 1]
myQueue.dequeue().value; // 2
myQueue.dequeue().value; // 1
myQueue.dequeue();       // null
```

That's our working `dequeue` method.

---

## Summing Up

In this lesson, you learned how to restrict a linked list's behavior to build two new, more specialized data structures. Specifically, you learned…

- The tennis-ball-can analogy for a stack, and **LIFO** (Last In, First Out).
- How the back button in a web browser is really just popping a stack.
- Why a linked list beats an `ArrayList` for a stack, and which end to operate on to keep `push` and `pop` at O(1).
- How to build a `Node` class, a `Stack` class and constructor, and the `push` and `pop` methods.
- The line-of-people analogy for a queue, and **FIFO** (First In, First Out).
- Why a queue needs to enqueue and dequeue on opposite ends to stay O(1).
- How to build a `Queue` class and constructor, and the `enqueue` and `dequeue` methods.

See you next time!
