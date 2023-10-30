---
layout: post
date: 2023-10-28 16:59:00 +0900
title: "[C] 롤토체스 시즌 9.5 챔피언 검색트리 구현"
---

# Binary Search Tree (C language) 
##### 정보통신공학과 202001638 백재선
---
### 롤토체스 시즌 9.5 챔피언 검색 트리 구현
```
1. 챔피언 이름을 기준으로 이진 탐색 트리 구성.
2. 챔피언 이름과 함께 체력(hp), 공격력(atk), 초당 피해량(dps)를 출력.
3. 하나의 챔피언 검색 후 탐색 과정과 탐색 횟수, 검색한 챔피언 이름과 정보 출력.
```
---
### 1. 노드 정의 <br>
- 정보는 이름(char), 체력, 공격력, 초당피해량(int) 네 개로 함.
- 이중연결리스트 이므로 자식 노드는 left와 right.

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct champion{
    char name[50];
    int hp, atk, dps;

    struct champion *left;
    struct champion *right;
}champ;
```
---

### 2. add_champions 함수
1. 모든 챔피언의 이름과 정보를 삽입.<br>
2. 삽입 순서는 챔피언의 영어이름 알파벳 순.<br>
3. 'strcpy(str1, str2)' : 문자열 str2를 str1로 복사.<br>
4. 'strcmp(str1, str2)' : 문자열 str1과 str2를 비교.<br>
- 비교후 반환 값 
- 0 -> 문자열이 같다 
- -1 -> str1이 사전적으로 앞에 있다 
- 1 -> 반대

```
1. strcmp 함수로 key와 루트의 챔피언 이름을 비교.
2. key가 루트 챔피언 이름보다 앞 순서라면 'root->left' 재귀호출
3. 뒤 순서라면 'root->right' 재귀호출
4. 같다면 'return' 중복처리.
```

```
void add_chapions(champ **root, const char *name, int hp, int atk, int dps){
    if (*root == NULL){
        *root = (champ *)malloc(sizeof(champ));
        strcpy((*root)->name, name);  // 문자열 복사
        (*root)->hp = hp;
        (*root)->atk = atk;
        (*root)->dps = dps;
        (*root)->left = NULL;
        (*root)->right = NULL;
    } else{
        int x = strcmp(name, (*root)->name); // strcmp(str1,str2)

        if (x < 0)   // str1이 앞순서라면(root 보다 앞 순서라면,)
            add_chapions(&(*root)->left, name, hp, atk, dps);  // 이름을 root 의 left.
        else if (x > 0)
            add_chapions(&(*root)->right, name, hp, atk, dps);
        else
            return; // 같으면 중복처리
    }
}
```
---
### 3. inorder_champ 함수
1. add_champions에서 입력한 전체 챔피언 이름과 정보 출력.<br>
2. 중위 순회로 출력. 알파벳 순서.<br>

```
void inorder_champ(champ *root) {
    if (root == NULL) return;
    inorder_champ(root->left);
    printf("champion : %s\n", root->name);
    printf("hp : %d, atk : %d, dps : %d\n",root->hp, root->atk, root->dps);
    printf("----------------------\n");
    inorder_champ(root->right);
}
```
---
### 4. inorder_search_and_print 함수
1. 원하는 챔피언의 이름과 정보를 출력. <br>
2. 원하는 챔피언을 탐색하기까지의 과정 출력. <br>
3. 탐색 횟수 출력.

```
1. 'count' 변수 정의 : 전체 탐색 횟수
2. 현재 위치 (root)가 'NULL'일 때까지 반복 및 탐색횟수 증가.
3. 원하는 챔피언 이름과 루트의 챔피언 이름 순서 비교
4. 루트 챔피언의 이름 출력 후 (탐색 과정 출력) 왼쪽이나 오른쪽 자식 노드로 이동.
5. 만약 '(2. 현재 위치 (root)가 'NULL'일 때까지 반복)'을 만족하기 전에<br>
원하는 챔피언 이름과 루트 챔피언 이름이 일치하면 그 챔피언 이름과 정보, 탐색 횟수 출력.  
```

```
int inorder_search_and_print(champ *root, const char *name){
    int count = 0; // 탐색 횟수

    while (root != NULL){
        count ++;

        int x = strcmp(name, root->name);
        if (x < 0) {
            printf("%s -> ", root->name);
            root = root->left;
        } else if (x > 0){
            printf("%s -> ", root->name);
            root = root->right;
        } else{
            printf("%s\n\n", root->name);
            printf("search count : %d\n\n", count);
            printf("champion : %s\n", root->name);
            printf("hp : %d, atk : %d, dps : %d\n",root->hp, root->atk, root->dps);
            return count;
        }

        if (count % 5 == 0) printf("\n"); // 5번 탐색마다 줄바꿈
    }

    printf("champion not found\n");
    return -1;
}
```
---
### 5. main 함수
1. 트리 초기화
2. 챔피언 이름 및 정보 삽입
3. 원하는 챔피언 이름 검색

```
int main(){
    champ *root = NULL;

    add_chapions(&root, "Galio", 800, 70, 35);
    add_chapions(&root, "Gangplank",1000 ,80 ,80);
    add_chapions(&root, "Graves",700 ,40 ,55);
    add_chapions(&root, "Nasus",1000 ,55,60);
    
    -------------------중략----------------------
    
    add_chapions(&root, "Twisted pate",550 ,20 ,40);
    add_chapions(&root, "Piora",950 ,45 ,75);
    add_chapions(&root, "Heimerdinger",750 ,30 ,40);

    inorder_champ(&(*root));
    printf("\n");

    inorder_search_and_print(&(*root), "Kale");

    return 0;
}
```
---
### 6. output
```
C:\Users\qorwo\Desktop\DataStructure\cmake-build-debug\lolchess.exe
champion : Aatrox
hp : 900, atk : 70, dps : 80
----------------------
champion : Ahri
hp : 800, atk : 40, dps : 50
----------------------
champion : Ash
hp : 600, atk : 20, dps : 55
----------------------
champion : Azir
hp : 750, atk : 30, dps : 30
----------------------
    
      ...중략...
 
----------------------
champion : Warwick
hp : 800, atk : 50, dps : 40
----------------------
champion : Xanthe
hp : 1000, atk : 50, dps : 60
----------------------

Galio -> Gangplank -> Graves -> Nasus -> Napiri ->
Malzaha -> Irellia -> Jarban IV -> Jaya -> Jayce ->
Jin -> Jinx -> Karma -> Kaisa -> Kale

search count : 15

champion : Kale
hp : 500, atk : 15, dps : 30

Process finished with exit code 0
```

```
1.'add_champions' 함수를 재귀 호출할 때 '&name'이 아니고 'name'으로 호출하는 이유
- 함수 정의를 포인터로 했으므로 주소값이 아닌 name 실제 값을 넘겨줘야함.
2. 이중포인터 너무 어렵당. 처음에 함수 정의를 뭘로 했는지 신경써야겠다.
```

---
### 7. 전체코드
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct champion{
char name[50];
int hp, atk, dps;

    struct champion *left;
    struct champion *right;
}champ;

void add_chapions(champ **root, const char *name, int hp, int atk, int dps){
if (*root == NULL){
*root = (champ *)malloc(sizeof(champ));
strcpy((*root)->name, name);  // 문자열 복사
(*root)->hp = hp;
(*root)->atk = atk;
(*root)->dps = dps;
(*root)->left = NULL;
(*root)->right = NULL;
} else{
int x = strcmp(name, (*root)->name); // strcmp(str1,str2)
// : (0->문자열이 같다) (-1->str1이 사전적으로 앞에 있다) (1->반대)
if (x < 0) // str1이 앞순서라면(root 보다 앞 순서라면,)
add_chapions(&(*root)->left, name, hp, atk, dps);  // 이름을 root 의 left.
else if (x > 0)
add_chapions(&(*root)->right, name, hp, atk, dps);
else
return; // 같으면 중복 처리
}
}

void inorder_champ(champ *root) {
if (root == NULL) return;
inorder_champ(root->left);
printf("champion : %s\n", root->name);
printf("hp : %d, atk : %d, dps : %d\n",root->hp, root->atk, root->dps);
printf("----------------------\n");
inorder_champ(root->right);
}

int inorder_search_and_print(champ *root, const char *name){
int count = 0; // 탐색 횟수

    while (root != NULL){
        count ++;

        int x = strcmp(name, root->name);
        if (x < 0) {
            printf("%s -> ", root->name);
            root = root->left;
        } else if (x > 0){
            printf("%s -> ", root->name);
            root = root->right;
        } else{
            printf("%s\n\n", root->name);
            printf("search count : %d\n\n", count);
            printf("champion : %s\n", root->name);
            printf("hp : %d, atk : %d, dps : %d\n",root->hp, root->atk, root->dps);
            return count;
        }

        if (count % 5 == 0) printf("\n"); // 5번 탐색마다 줄바꿈
    }

    printf("champion not found\n");
    return -1;
}

int main(){
champ *root = NULL;

    add_chapions(&root, "Galio", 800, 70, 35);
    add_chapions(&root, "Gangplank",1000 ,80 ,80);
    add_chapions(&root, "Graves",700 ,40 ,55);
    add_chapions(&root, "Nasus",1000 ,55,60);
    add_chapions(&root, "Napiri",750 ,40 ,60);
    add_chapions(&root, "Notilus",950 ,40 ,80);
    add_chapions(&root, "Nico",800 ,45 ,50);
    add_chapions(&root, "Nilla",950 ,45 ,75);
    add_chapions(&root, "Darius",750 ,50 ,65);
    add_chapions(&root, "Ryze",1111 ,30 ,50);
    add_chapions(&root, "Renekton",650 ,45 ,50);
    add_chapions(&root, "Reksai", 800,45 ,60);
    add_chapions(&root, "Malzaha",500 ,15 ,40);
    add_chapions(&root, "Modekaizer", 1000,50 ,70);
    add_chapions(&root, "Ms fortune",650 ,25 ,50);
    add_chapions(&root, "Millio",500 ,20 ,40);
    add_chapions(&root, "Vi",800 ,50 ,60);
    add_chapions(&root, "Velvez",1100 ,60 ,80);
    add_chapions(&root, "Velkoz",400 ,30 ,40);
    add_chapions(&root, "Bbobbi",700 ,40 ,55);
    add_chapions(&root, "Samira",500 ,15 ,45);
    add_chapions(&root, "Sion",900 ,65 ,70);
    add_chapions(&root, "Sejuani",1100 ,60 ,60);
    add_chapions(&root, "Sett",800 ,50 ,60);
    add_chapions(&root, "Sona",700 ,25 ,40);
    add_chapions(&root, "Soraka",700 ,25 ,55);
    add_chapions(&root, "Shen",1000 ,60 ,60);
    add_chapions(&root, "Swein",800 ,45 ,45);
    add_chapions(&root, "Silco",750 ,30 ,45);
    add_chapions(&root, "Ahri",800 ,40 ,50);
    add_chapions(&root, "Azir",750 ,30 ,30);
    add_chapions(&root, "Aatrox",900 ,70 ,80);
    add_chapions(&root, "Ash",600 ,20 ,55);
    add_chapions(&root, "Eco",800 ,40 ,50);
    add_chapions(&root, "Oriana",500 ,15 ,35);
    add_chapions(&root, "Warwick",800 ,50 ,40);
    add_chapions(&root, "Irellia",700 ,40 ,50);
    add_chapions(&root, "Illaoi",700 ,40 ,60);
    add_chapions(&root, "Jarban IV",1000 ,60 ,60);
    add_chapions(&root, "Jaya",750 ,25 ,65);
    add_chapions(&root, "Jayce",700 ,25 ,60);
    add_chapions(&root, "Jin",504 ,14 ,54);
    add_chapions(&root, "Jinx",600 ,20 ,50);
    add_chapions(&root, "Chogas",700 ,30 ,65);
    add_chapions(&root, "Karma",700 ,25 ,45);
    add_chapions(&root, "Karsadin",800 ,45 ,50);
    add_chapions(&root, "Cassiopeia",500 ,20 ,40);
    add_chapions(&root, "Kaisa",850 ,35 ,45);
    add_chapions(&root, "Katarina",800 ,40 ,50);
    add_chapions(&root, "Kale",500 ,15 ,30);
    add_chapions(&root, "Queen",700 ,25 ,60);
    add_chapions(&root, "Xanthe",1000 ,50 ,60);
    add_chapions(&root, "Kiana",650 ,35 ,55);
    add_chapions(&root, "Tarik",800 ,50 ,60);
    add_chapions(&root, "Taliya",550 ,20 ,40);
    add_chapions(&root, "Twisted pate",550 ,20 ,40);
    add_chapions(&root, "Piora",950 ,45 ,75);
    add_chapions(&root, "Heimerdinger",750 ,30 ,40);

    inorder_champ(&(*root));
    printf("\n");

    inorder_search_and_print(&(*root), "Kale");

    return 0;
}
```

끝.