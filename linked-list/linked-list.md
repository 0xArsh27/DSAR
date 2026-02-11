🔹 1) Node Structure (Base of everything)
class Node {
public:
    int data;
    Node* next;

    Node(int val) {
        data = val;
        next = NULL;
    }
};
# Linked List — Reference (DSA Ready)

## Node structure

```cpp
class Node {
public:
    int data;
    Node* next;

    Node(int val) {
        data = val;
        next = NULL;
    }
};
```

## Head

`head` points to the first node. If the list is empty, `head = NULL`.

## Traversal

```cpp
Node* temp = head;
while (temp != NULL) {
    cout << temp->data << " ";
    temp = temp->next;
}
```

## Three Golden Rules

- Always check for `NULL` before accessing `->next`.
- When re-linking nodes, store the next node first:

```cpp
Node* nextNode = curr->next;
```

- If a function may modify the head, pass by reference:

```cpp
void func(Node* &head);
```

## Core Operations (Interview-ready)

### Insert at head

```cpp
void insertAtHead(Node* &head, int val) {
    Node* newNode = new Node(val);
    newNode->next = head;
    head = newNode;
}
```

### Insert at tail

```cpp
void insertAtTail(Node* &head, int val) {
    Node* newNode = new Node(val);
    if (head == NULL) {
        head = newNode;
        return;
    }
    Node* temp = head;
    while (temp->next != NULL) temp = temp->next;
    temp->next = newNode;
}
```

### Delete node by value

```cpp
void deleteNode(Node* &head, int val) {
    if (head == NULL) return;
    if (head->data == val) {
        Node* del = head;
        head = head->next;
        delete del;
        return;
    }
    Node* temp = head;
    while (temp->next != NULL && temp->next->data != val) {
        temp = temp->next;
    }
    if (temp->next == NULL) return;
    Node* del = temp->next;
    temp->next = temp->next->next;
    delete del;
}
```

## Reverse linked list (must-know)

```cpp
Node* reverse(Node* head) {
    Node* prev = NULL;
    Node* curr = head;
    while (curr != NULL) {
        Node* nextNode = curr->next;
        curr->next = prev;
        prev = curr;
        curr = nextNode;
    }
    return prev;
}
```

## Fast & slow pointers (two-pointer technique)

Used for finding the middle, detecting cycles, and checking palindromes.

```cpp
Node* slow = head;
Node* fast = head;
while (fast != NULL && fast->next != NULL) {
    slow = slow->next;
    fast = fast->next->next;
}
```

## DSA-ready checklist

- Understand pointer reassignments and memory ownership.
- Practice edge cases: empty list, single node, head/tail changes.
- Know time/space complexity for each operation (O(1)/O(n)).
- Solve common problems: reverse, cycle detection, merge, add numbers.
- Write clean, well-tested functions suitable for interviews.

---

_Concise reference for implementation and interviews._