---
title: "抽象数据类型(转)"
subtitle: ""
date: 2021-05-26T10:58:58+08:00
lastmod: 2021-05-26T10:58:58+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: []
categories: ["算法"]

hiddenFromHomePage: false
hiddenFromSearch: false

resources:
- name: "featured-image"
  src: "featured-image.jpg"
- name: featured-image-preview
  src: featured-image-preview.jpg

featuredImage: ""
featuredImagePreview: ""
images: [""]
---



# Abstract Data Types

Abstract Data type (ADT) is a type (or class) for objects whose behaviour is defined by a set of value and a set of operations.

The definition of ADT only mentions what operations are to be performed but not how these operations will be implemented. It does not specify how data will be organized in memory and what algorithms will be used for implementing the operations. It is called “abstract” because it gives an implementation-independent view. The process of providing only the essentials and hiding the details is known as abstraction.

[![img](https://media.geeksforgeeks.org/wp-content/uploads/20190828194629/ADT.jpg)](https://media.geeksforgeeks.org/wp-content/uploads/20190828194629/ADT.jpg)

The user of [data type](https://www.geeksforgeeks.org/data-types-in-c/) does not need to know how that data type is implemented, for example, we have been using Primitive values like int, float, char data types only with the knowledge that these data type can operate and be performed on without any idea of how they are implemented. So a user only needs to know what a data type can do, but not how it will be implemented. Think of ADT as a black box which hides the inner structure and design of the data type. Now we’ll define three ADTs namely [List](https://www.geeksforgeeks.org/linked-list-set-1-introduction/) ADT, [Stack](https://www.geeksforgeeks.org/stack-data-structure-introduction-program/) ADT, [Queue](https://www.geeksforgeeks.org/queue-set-1introduction-and-array-implementation/) ADT.

### List ADT

The data is generally stored in key sequence in a list which has a head structure consisting of count,pointers and address of compare function needed to compare the data in the list.

![img](https://media.geeksforgeeks.org/wp-content/uploads/20190917225727/ListADTStructure.png)

The data node contains the pointer to a data structure and a

self-referential pointer which points to the next node in the list.

```c
//List ADT Type Definitions
typedef struct node
{
void *DataPtr;
struct node *link;
} Node;
typedef struct
{
int count;
Node *pos;
Node *head;
Node *rear;
int (*compare) (void *argument1, void *argument2)
} LIST;

```

The List ADT Functions is given below:

![img](https://media.geeksforgeeks.org/wp-content/uploads/20190917225726/ListADTFunctions.png)

A list contains elements of the same type arranged in sequential order and following operations can be performed on the list.

- get() – Return an element from the list at any given position.
- insert() – Insert an element at any position of the list.
- remove() – Remove the first occurrence of any element from a non-empty list.
- removeAt() – Remove the element at a specified location from a non-empty list.
- replace() – Replace an element at any position by another element.
- size() – Return the number of elements in the list.
- isEmpty() – Return true if the list is empty, otherwise return false.
- isFull() – Return true if the list is full, otherwise return false.

### Stack ADT

- In Stack ADT Implementation instead of data being stored in each node, the pointer to data is stored.

- The program allocates memory for the date and address is passed to the stack ADT.

  ![img](https://media.geeksforgeeks.org/wp-content/uploads/20190917225800/StackADT.jpg)

- The head node and the data nodes are encapsulated in the ADT. The calling function can only see the pointer to the stack.

- The stack head structure also contains a pointer to top and count of number of entries currently in stack.

  ```c
  //Stack ADT Type Definitions
  typedef struct node
  {
  void *DataPtr;
  struct node *link;
  } StackNode;
  typedef struct
  {
  int count;
  StackNode *top;
  } STACK;
  ```

A Stack contains elements of the same type arranged in sequential order. All operations take place at a single end that is top of the stack and following operations can be performed:

- push() – Insert an element at one end of the stack called top.
- pop() – Remove and return the element at the top of the stack, if it is not empty.
- peek() – Return the element at the top of the stack without removing it, if the stack is not empty.
- size() – Return the number of elements in the stack.
- isEmpty() – Return true if the stack is empty, otherwise return false.
- isFull() – Return true if the stack is full, otherwise return false.

### Queue ADT

- The queue abstract data type (ADT) follows the basic design of the stack abstract data type.

  ![img](https://media.geeksforgeeks.org/wp-content/uploads/20190917225729/QueueADT.png)

- Each node contains a void pointer to the data and the link pointer to the next element in the queue. The program’s responsibility is to allocate memory for storing the data.

  ```c
  //Queue ADT Type Definitions
  typedef struct node
  {
  void *DataPtr;
  struct node *next;
  } QueueNode;
  typedef struct
  {
  QueueNode *front;
  QueueNode *rear;
  int count;
  } QUEUE;
  
  ```

  

A Queue contains elements of the same type arranged in sequential order. Operations take place at both ends, insertion is done at the end and deletion is done at the front. Following operations can be performed:

- enqueue() – Insert an element at the end of the queue.
- dequeue() – Remove and return the first element of the queue, if the queue is not empty.
- peek() – Return the element of the queue without removing it, if the queue is not empty.
- size() – Return the number of elements in the queue.
- isEmpty() – Return true if the queue is empty, otherwise return false.
- isFull() – Return true if the queue is full, otherwise return false.

From these definitions, we can clearly see that the definitions do not specify how these ADTs will be represented and how the operations will be carried out. There can be different ways to implement an ADT, for example, the List ADT can be implemented using arrays, or singly linked list or doubly linked list. Similarly, stack ADT and Queue ADT can be implemented using arrays or linked lists.

