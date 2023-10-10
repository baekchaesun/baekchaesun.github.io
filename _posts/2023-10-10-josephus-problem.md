---
layout: post
date: 2023-10-10 15:00:08 +0900
title: "[C] 백준 1158번: 요세푸스 문제"
---

# Josephus problem (C language)
##### 정보통신공학과 202001638 백재선
---
연결리스트로 큐를 구현하여 문제를 해결할 예정이다.<br>
(0 <= n <= 5000) 조건에서는 배열 메모리 사용이 더 간단하고 효율적이다. <br>
그래도 배운 걸 이용하고 싶으니까 그냥 할 거다
---

문제 해결 방법
```
(입력은 `n = 7`, `k = 3` 으로 설정)
1. 리스트를 front 포인터 노드부터 하나씩 dequeue 한다.
2. dequeue 된 노드를 다시 rear 포인터에 enqueue 한다.
3. front 포인터의 노드가 k(3)번째 노드이면 enqueue 하지 않고 출력할 배열에 저장한다.
4. 리스트가 빌 때까지 1, 2, 3번을 반복한다.
```
---
### 1. 노드 및 리스트 정의, 초기화

LinkedLists 정의
---

```
#include <stdio.h>
#include <stdlib.h>

typedef struct node{
    int data;
    struct node *next;
}node;

typedef struct table_list{
    struct node *front;
    struct node *rear;
    int count;
}table;

void initTable(table *q){
    q->front=q->rear=NULL;
    q->count=0;
}

int isEmptyTable(table *q){
    return (q->count == 0);
}
```
---

### 2. enqueue 함수

연결리스트에 초기 데이터 삽입할 때만 호출됨.<br>
리스트의 rear 포인터에 노드 삽입
---
```
void enqueue_table(table *q, int data){
    node *new = malloc(sizeof(node));
    new->data = data;

    if (isEmptyTable(q)){
        q->front = new;
        q->rear = new;
    } else{
        q->rear->next = new;
        q->rear = new;
    }
    q->count ++;
}
```
---

### 3. dequeue 함수

리스트의 front 포인터에 해당하는 노드를 반환.<br>
제거하지 않는 이유는 다시 enqueue 해야하기 때문이다. (reenqueue 함수)
---
```
node* dequeue_table(table *q){
    if (isEmptyTable(q)) return NULL;
    else{
        node *tmp = q->front;

        q->front = tmp->next;
        tmp->next = NULL;
        q->count --;

        return tmp;
    }
}
```
---

### 4. reenqueue 함수

k에 해당하지 않는 노드 dequeue 후, 다시 enqueue 할 때 호출하는 함수.
---
```
void reinqueue(table *q, node *tmp){   // dequeued node를 다시 reenqueue.
    if (isEmptyTable(q)){
        q->front = tmp;
        q->rear = tmp;
    }else {
        q->rear->next = tmp;
        q->rear = tmp;
    }
    q->count ++;
}
```
---

### 5. output 함수

k에 해당하는 노드를 dequeue 하고(dequeue 함수 X) 그 값을 반환한다.
반환된 값은 이후 main 함수에서 배열에 삽입될 것이다.
---
```
int output(table *q){
    if (isEmptyTable(q)) return -1;
    else {
        node *tmp = q->front;
        int data = tmp->data;

        q->front = tmp->next;
        q->count--;

        free(tmp);
        return data;
    }
}
```
---

### 6. josephus 함수

k에 해당하는지 해당하지 않는지 구분하고, <br>
해당하면 `output()`, <br>
해당하지 않으면 `dequeue()` 및 `reenqueue()` 호출.
---
```
int josephus(table *q, int n, int k) {
    for (int i = 0; i < k - 1; ++i) {
        node *denode = (node *) dequeue_table(q);
        reenqueue(q, denode);
    }
    return output(q);
}
```

### 7. make_table 함수

처음에 입력된 n에 맞춰서 리스트에 1 ~ n 숫자 삽입.
---
```
void make_table(table *q, int n){
    for (int i = 1; i <= n; ++i) {
        enqueue_table(q, i);
    }
}
```
---

### 8. main 함수

```
1. 배열 정의, 리스트에 데이터 입력
2. 요세푸스 함수 호출
3. 출력 및 배열과 리스트 데이터 동적 할당 해제
```
---

```
int main(){
    int n = 7;
    int k = 3;
    int* output = (int*)malloc(n * sizeof(int));  // 출력할 동적배열 정의
    
    table q;
    initTable(&q);
    make_table(&q, n);

    for (int i = 0; i <= n - 1; ++i) {            // n번 반복
        int output_data = josephus(&q, n, k);     // 배열 사이즈가 n이 될 때까지
        output [i] = output_data;                 // (= 리스트가 빌 때까지)
    }

    while (!isEmptyTable(&q)) {                   // 다 옮겼으면 리스트는 데이터 동적 할당 해제
        node *tmp = dequeue_table(&q);
        free(tmp);
    }

    printf("< ");
    for (int j = 0; j <= n - 1; ++j)
        printf("%d ", output[j]);
    printf(">");

    free(output);
    return 0;
}
```
---

### 9. 출력
```
C:\Users\qorwo\Desktop\DataStructure\cmake-build-debug\josephus_problem.exe
< 3 6 2 7 5 1 4 >
Process finished with exit code 0
```
---

1. dequeue 된 값을 어떻게 다시 reenqueue 해야할 지 고민이 많이 됐다.<br>
   1. ->  반환 타입을 `node *`로 해서 해당 노드 포인터를 반환했다.<br>
2. 배열의 i 인덱스에 output데이터를 삽입해야 하는데, josephus 함수에서 하려니 반복문을 어디에, 어떻게 만들어야 할지 몰라서 헤맸다.<br>
3. 배열 자체를 반환할 수가 없는 점<br>
   2, 3 ->  그냥 main 함수에서 해결<br>
4. 이전에는 `int, node*, void` 등 함수의 반환 타입이 막연하고 잘 안 다가왔었는데, 이번에는 각 함수의 반환 값을 신경써야 했고, 덕분에 이해하는 데에 도움이 됐다.

---

### 전체 코드

---
```
#include <stdio.h>
#include <stdlib.h>

typedef struct node{
    int data;
    struct node *next;
}node;

typedef struct table_list{
    struct node *front;
    struct node *rear;
    int count;
}table;

void initTable(table *q){
    q->front=q->rear=NULL;
    q->count=0;
}

int isEmptyTable(table *q){
    return (q->count == 0);
}

// ------------------------------------------ linkedlists 정의.

void enqueue_table(table *q, int data){
    node *new = malloc(sizeof(node));
    new->data = data;

    if (isEmptyTable(q)){
        q->front = new;
        q->rear = new;
    } else{
        q->rear->next = new;
        q->rear = new;
    }
    q->count ++;
}

node* dequeue_table(table *q){
    if (isEmptyTable(q)) return NULL;
    else{
        node *tmp = q->front;

        q->front = tmp->next;
        tmp->next = NULL;
        q->count --;

        return tmp;
    }
}

void reenqueue(table *q, node *tmp){   // dequeued node를 다시 reenqueue.
    if (isEmptyTable(q)){
        q->front = tmp;
        q->rear = tmp;
    }else {
        q->rear->next = tmp;
        q->rear = tmp;
    }
    q->count ++;
}

int output(table *q){
    if (isEmptyTable(q)) return -1;
    else {
        node *tmp = q->front;
        int data = tmp->data;

        q->front = tmp->next;
        q->count--;

        free(tmp);
        return data;
    }
}

int josephus(table *q, int n, int k) {
    for (int i = 0; i < k - 1; ++i) {
        node *denode = (node *) dequeue_table(q);
        reenqueue(q, denode);
    }
    return output(q);
}

void make_table(table *q, int n){
    for (int i = 1; i <= n; ++i) {
        enqueue_table(q, i);
    }
}


int main(){
    int n = 7;
    int k = 3;
    int* output = (int*)malloc(n * sizeof(int));  // 출력할 동적배열 정의
    
    table q;
    initTable(&q);
    make_table(&q, n);

    for (int i = 0; i <= n - 1; ++i) {            // n번 반복
        int output_data = josephus(&q, n, k);     // 배열 사이즈가 n이 될 때까지
        output [i] = output_data;                 // (= 리스트가 빌 때까지)
    }

    while (!isEmptyTable(&q)) {                   // 다 옮겼으면 리스트는 데이터 동적 할당 해제
        node *tmp = dequeue_table(&q);
        free(tmp);
    }

    printf("< ");
    for (int j = 0; j <= n - 1; ++j)
        printf("%d ", output[j]);
    printf(">");

    free(output);
    return 0;
}
```

끝