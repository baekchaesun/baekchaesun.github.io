---
layout: post
date: 2023-09-29 14:37:00 +0900
title: "Double LinkedLists"
---

# Double LinkedLists (C language)

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
        return NULL;
    }
    if (x->size == 1){
        node *tmp = x->head;
	x->head = x->tail = NULL;
	x->size = 0;

	return tmp;
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
1. 리스트 비었으면 "NULL" 출력
2. 안 비었으면 일단 head->data 출력
3. 이후 tmp 노드를 head->llink 부터 head까지 순회하며 출력
```
---

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

---

1. 중간 삽입과 삭제 함수를 head 시작, tail 시작 두 부분으로 나누어 하는게 헷갈렸다.<br>
2. 출력 함수 작성할 때 순회를 멈출지 고민을 많이 했다. 근데 방법이 저것밖에 생각이 안남...<br>
3. 이후에 시간 남으면 탐색 함수를 따로 만들어서 삽입, 삭제 함수에서 탐색 함수를 호출하는 코드를 구현해보겠다.

---

### 전체 코드


```
1. head, tail 총 두개의 경우를 만들어 순환하게 함.
2. tmp 노드 반환
3. 반환된 tmp 노드 사용하여 삽입 및 삭제
```
--- 

```
int circuit_D_lists(list *x, int pos){
    if (pos <= ((x->size)/2) - 1){
        node *tmp = x->head;

        for (int i = 0; i < pos - 1; ++i)
            tmp = tmp->llink;

        return tmp;
    } else {
        node *tmp = x->tail;

        for (int i = x->size; i > pos; --i)
            tmp = tmp->rlink;

        return tmp;
    }
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
        node *tmp = circuit_D_lists(x, pos);

        new->llink = tmp->llink;      // new 연결
        tmp->llink = new;

        new->rlink = tmp;             // 상호연결
        new->llink->rlink = new;
    } else{                           // 중간에 넣기 (tail에서 순회 시작)
        node *tmp = circuit_D_lists(x, pos);

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
        return NULL;
    }
    if (x->size == 0){
        node *tmp = x->head;
        x->head = x->tail = NULL;
        x->size = 0;
        return tmp;
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
        node *tmp = circuit_D_lists(x, pos);
        data = tmp->data;

        tmp->rlink->llink = tmp->llink;
        tmp->llink->rlink = tmp->rlink;

        free(tmp);
    } else {                               // tail 포인터에서 순회 시작
        node *tmp = circuit_D_lists(x, pos);
        data = tmp->data;

        tmp->llink->rlink = tmp->rlink;
        tmp->rlink->llink = tmp->llink;

        free(tmp);
    }
    x->size --;

    return data;
}

```