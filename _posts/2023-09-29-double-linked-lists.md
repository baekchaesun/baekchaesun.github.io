
---
layout: post
date: 2023-09-29 14:37:00 +0900
title: "데이터구조 assignment - Double LinkedLists 구현"
---

# Double LinkedLists (C language)
##### 정보통신공학과 202001638 백재선

---
### 1. 노드 및 리스트 정의, 초기화

이중연결리스트는 tail 포인터와 head 포인터도 상호 연결 되어있음.

```
head 포인터의 rlink -> tail 포인터
tail 포인터의 llink -> head 포인터
```

---


```
#include <stdio.h>
#include <stdlib.h>

typedef struct node{
    int data;
    struct node *rlink;
    struct node *llink;
}node;

typedef struct Dlinkedlists{
    node *head;
    unsigned int size;
    node *tail;
}list;

void init_D_lists(list *x){
    x->head = NULL;
    x->tail = NULL;
    x->size = 0;
}

int isEmpty_D_lists(list *x){
    return (x->size == 0);
}
```

---

### 2. 노드 삽입

삽입 함수는 총 다섯 부분으로 나누었음
```
1. 비어있는 리스트에 노드 삽입
2. 리스트 처음(head 포인터)에 노드 삽입
3. 리스트 마지막(tail 포인터)에 노드 삽입
4. 리스트 중간에 노드 삽입 (head 포인터에서 llink 순차적 탐색)
5. 리스트 중간에 노드 삽입 (tail 포인터에서 rlink 순차적 탐색)
```

---

```
void insert_D_lists(list *x, int pos, int data){
    node *new = malloc(sizeof(node));
    new->data = data;

    if (isEmpty_D_lists(x)){
        x->head = new;
        x->tail = new;
    } else if (pos <= 0){             // 맨 앞에다가 넣기
        new->llink = x->head;
        x->head = new;
        x->head->llink->rlink = x->head;

        x->head->rlink = x->tail;     // head, tail 상호연결
        x->tail->llink = x->head;
    } else if (pos > x->size - 1){       // 맨 뒤에다가 넣기
        new->rlink = x->tail;
        x->tail = new;
        x->tail->rlink->llink = x->tail;

        x->tail->llink = x->head;
        x->head->rlink = x->tail;
    } else if (pos <= ((x->size)/2) - 1){   // 중간에 넣기 (head에서 순회 시작)
        node *tmp = x->head;

        for (int i = 0; i < pos - 1; ++i)
            tmp = tmp->llink;

        new->llink = tmp->llink;      // new 연결
        tmp->llink = new;

        new->rlink = tmp;             // 상호연결
        new->llink->rlink = new;
    } else{                           // 중간에 넣기 (tail에서 순회 시작)
        node *tmp = x->tail;

        for (int i = x->size; i > pos; --i)
            tmp = tmp->rlink;

        new->llink = tmp->llink;
        new->rlink = tmp;

        new->llink->rlink = new;
        tmp->llink = new;
        }
    x->size ++;

}
```

---

### 3. 노드 삭제

삭제 함수도 삽입 함수와 동일하게 다섯 부분으로 나눔.
** tmp 노드가 가리키는 데이터 동적 할당 해제 **
```
free(tmp);
```

---

```
int delete_D_lists(list *x, int pos){
    int data;

    if (isEmpty_D_lists(x)) {
        printf("underflow \n");
        return -1;
    }

    if (pos == 0){                         // 처음 거 제거
        node *tmp = x->head;
        data = tmp->data;

        x->head = tmp->llink;
        x->head->rlink = x->tail;
        x->tail->llink = x->head;

        free(tmp);
    } else if (pos > x->size - 1){        //마지막 거 제거
        node *tmp = x->tail;
        data = tmp->data;

        x->tail = tmp->rlink;
        x->tail->llink = x->head;
        x->head->rlink = x->tail;

        free(tmp);
    } else if (pos <= ((x->size)/2) - 1){  // 중간 거 제거 (head 포인터에서 순회 시작)
        node *tmp = x->head;
        data = tmp->data;

        for (int i = 0; i < pos; ++i)
            tmp = tmp->llink;

        tmp->rlink->llink = tmp->llink;
        tmp->llink->rlink = tmp->rlink;

        free(tmp);
    } else {                               // tail 포인터에서 순회 시작
        node *tmp = x->tail;
        data = tmp->data;

        for (int i = x->size - 1; i > pos; --i)
            tmp = tmp->rlink;

        tmp->llink->rlink = tmp->rlink;
        tmp->rlink->llink = tmp->llink;

        free(tmp);
    }
    x->size --;
    
    return data;
}
```

---

### 4. 리스트 출력

노드 삭제 함수와 더불어 애를 많이 먹었다.

```
void print_D_lists(list *x){
    if(isEmpty_D_lists(x))
        printf("NULL");
    else {
        if (x->head != NULL)
            printf("%d", x->head->data);
        if (x->size > 1) {
            for (node *tmp = x->head->llink; tmp != x->head; tmp = tmp->llink) {
                // tmp 노드가 head 포인터로 다시 돌아올 때까지 순회
                printf(" -> %d", tmp->data);
            }
        }
    }
    printf("\n");
}
```

---

### 5. main 함수

노드 삽입 삭제 과정을 일일이 확인하기 위해 계속 리스트를 출력했다.

```
int main(){
    list x;
    init_D_lists(&x);

    insert_D_lists(&x,2,111);   // 111
    print_D_lists(&x);

    insert_D_lists(&x,0,222);   // 222 -> 111
    print_D_lists(&x);

    insert_D_lists(&x,4,333);   // 222 -> 111 -> 333
    print_D_lists(&x);

    insert_D_lists(&x,2,444);
    insert_D_lists(&x,2,555);   // 222 -> 111 -> 555 -> 444 -> 333
    print_D_lists(&x);

    insert_D_lists(&x,2,666);
    insert_D_lists(&x,9,777);   // 2 1 6 5 4 3 7
    insert_D_lists(&x,3,888);
    insert_D_lists(&x,1,999);
    insert_D_lists(&x,8,000);   // 2 9 1 6 8 5 4 3 0 7
    print_D_lists(&x);


    delete_D_lists(&x,6);       // 444 제거
    print_D_lists(&x);
    delete_D_lists(&x,1);       // 999 제거
    print_D_lists(&x);
    delete_D_lists(&x,0);       // 222 제거
    print_D_lists(&x);
    delete_D_lists(&x,7);       // 777 제거
    print_D_lists(&x);
    delete_D_lists(&x,3);       // 555 제거
    print_D_lists(&x);
    delete_D_lists(&x,3);       // 333 제거
    print_D_lists(&x);
    delete_D_lists(&x,10);      // 000 제거
    print_D_lists(&x);
    delete_D_lists(&x,0);       // 111 제거
    print_D_lists(&x);
    delete_D_lists(&x,1);       // 888 제거
    print_D_lists(&x);
    delete_D_lists(&x,0);       // 666 제거
    print_D_lists(&x);

    delete_D_lists(&x,1);       // isempty

    return 0;
}
```

---

### 6. Output
```
C:\Users\qorwo\Desktop\DataStructure\cmake-build-debug\doubleLinkedlists.exe
111
222 -> 111
222 -> 111 -> 333
222 -> 111 -> 555 -> 444 -> 333
222 -> 999 -> 111 -> 666 -> 888 -> 555 -> 444 -> 333 -> 0 -> 777  // 삽입 끝
222 -> 999 -> 111 -> 666 -> 888 -> 555 -> 333 -> 0 -> 777         // 삭제 시작
222 -> 111 -> 666 -> 888 -> 555 -> 333 -> 0 -> 777
111 -> 666 -> 888 -> 555 -> 333 -> 0 -> 777
111 -> 666 -> 888 -> 555 -> 333 -> 0
111 -> 666 -> 888 -> 333 -> 0
111 -> 666 -> 888 -> 0
111 -> 666 -> 888
666 -> 888
666
NULL       // isempty
underflow  // isempty 상태에서 추가 삭제함수 호출

Process finished with exit code 0
```

---

중간 삽입과 삭제 함수를 head 시작, tail 시작 두 부분으로 나누어 하는게 헷갈렸다.
합쳐버릴까 했는데 시간이 더 걸리더라도 나누는 게 더 간지나서 했다.

이후에 시간 남으면 탐색 함수를 따로 만들어서 삽입, 삭제 함수에서 탐색 함수를 호출하는 코드를 구현해보겠다.

---

### 전체 코드

```
#include <stdio.h>
#include <stdlib.h>

typedef struct node{
    int data;
    struct node *rlink;
    struct node *llink;
}node;

typedef struct Dlinkedlists{
    node *head;
    unsigned int size;
    node *tail;
}list;

void init_D_lists(list *x){
    x->head = NULL;
    x->tail = NULL;
    x->size = 0;
}

int isEmpty_D_lists(list *x){
    return (x->size == 0);
}

void insert_D_lists(list *x, int pos, int data){
    node *new = malloc(sizeof(node));
    new->data = data;

    if (isEmpty_D_lists(x)){
        x->head = new;
        x->tail = new;
    } else if (pos <= 0){             // 맨 앞에다가 넣기
        new->llink = x->head;
        x->head = new;
        x->head->llink->rlink = x->head;

        x->head->rlink = x->tail;     // head, tail 상호연결
        x->tail->llink = x->head;
    } else if (pos > x->size - 1){       // 맨 뒤에다가 넣기
        new->rlink = x->tail;
        x->tail = new;
        x->tail->rlink->llink = x->tail;

        x->tail->llink = x->head;
        x->head->rlink = x->tail;
    } else if (pos <= ((x->size)/2) - 1){   // 중간에 넣기 (head에서 순회 시작)
        node *tmp = x->head;

        for (int i = 0; i < pos - 1; ++i)
            tmp = tmp->llink;

        new->llink = tmp->llink;      // new 연결
        tmp->llink = new;

        new->rlink = tmp;             // 상호연결
        new->llink->rlink = new;
    } else{                           // 중간에 넣기 (tail에서 순회 시작)
        node *tmp = x->tail;

        for (int i = x->size; i > pos; --i)
            tmp = tmp->rlink;

        new->llink = tmp->llink;
        new->rlink = tmp;

        new->llink->rlink = new;
        tmp->llink = new;
        }
    x->size ++;

}

int delete_D_lists(list *x, int pos){
    int data;

    if (isEmpty_D_lists(x)) {
        printf("underflow \n");
        return -1;
    }

    if (pos == 0){                         // 처음 거 제거
        node *tmp = x->head;
        data = tmp->data;

        x->head = tmp->llink;
        x->head->rlink = x->tail;
        x->tail->llink = x->head;

        free(tmp);
    } else if (pos > x->size - 1){        //마지막 거 제거
        node *tmp = x->tail;
        data = tmp->data;

        x->tail = tmp->rlink;
        x->tail->llink = x->head;
        x->head->rlink = x->tail;

        free(tmp);
    } else if (pos <= ((x->size)/2) - 1){  // 중간 거 제거 (head 포인터에서 순회 시작)
        node *tmp = x->head;
        data = tmp->data;

        for (int i = 0; i < pos; ++i)
            tmp = tmp->llink;

        tmp->rlink->llink = tmp->llink;
        tmp->llink->rlink = tmp->rlink;

        free(tmp);
    } else {                               // tail 포인터에서 순회 시작
        node *tmp = x->tail;
        data = tmp->data;

        for (int i = x->size - 1; i > pos; --i)
            tmp = tmp->rlink;

        tmp->llink->rlink = tmp->rlink;
        tmp->rlink->llink = tmp->llink;

        free(tmp);
    }
    x->size --;

    return data;
}

 void print_D_lists(list *x){
    if(isEmpty_D_lists(x))
        printf("NULL");
    else {
        if (x->head != NULL)
            printf("%d", x->head->data);
        if (x->size > 1) {
            for (node *tmp = x->head->llink; tmp != x->head; tmp = tmp->llink) {
                // tmp 노드가 head 포인터로 다시 돌아올 때까지 순회
                printf(" -> %d", tmp->data);
            }
        }
    }
    printf("\n");
}

int main(){
    list x;
    init_D_lists(&x);

    insert_D_lists(&x,2,111);   // 111
    print_D_lists(&x);

    insert_D_lists(&x,0,222);   // 222 -> 111
    print_D_lists(&x);

    insert_D_lists(&x,4,333);   // 222 -> 111 -> 333
    print_D_lists(&x);

    insert_D_lists(&x,2,444);
    insert_D_lists(&x,2,555);   // 222 -> 111 -> 555 -> 444 -> 333
    print_D_lists(&x);

    insert_D_lists(&x,2,666);
    insert_D_lists(&x,9,777);   // 2 1 6 5 4 3 7
    insert_D_lists(&x,3,888);
    insert_D_lists(&x,1,999);
    insert_D_lists(&x,8,000);   // 2 9 1 6 8 5 4 3 0 7
    print_D_lists(&x);


    delete_D_lists(&x,6);       // 444 제거
    print_D_lists(&x);
    delete_D_lists(&x,1);       // 999 제거
    print_D_lists(&x);
    delete_D_lists(&x,0);       // 222 제거
    print_D_lists(&x);
    delete_D_lists(&x,7);       // 777 제거
    print_D_lists(&x);
    delete_D_lists(&x,3);       // 555 제거
    print_D_lists(&x);
    delete_D_lists(&x,3);       // 333 제거
    print_D_lists(&x);
    delete_D_lists(&x,10);      // 000 제거
    print_D_lists(&x);
    delete_D_lists(&x,0);       // 111 제거
    print_D_lists(&x);
    delete_D_lists(&x,1);       // 888 제거
    print_D_lists(&x);
    delete_D_lists(&x,0);       // 666 제거
    print_D_lists(&x);

    delete_D_lists(&x,1);       // isempty

    return 0;
}
```