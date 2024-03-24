---
layout: post
date: 2023-10-28 16:59:00 +0900
title: "binary seach tree, double pointer"
---

# Binary Search Tree (C language) 
---
### 롤토체스 시즌 9.5 챔피언 검색 트리 구현
```
1. 챔피언 이름을 기준으로 이진 탐색 트리 구성.
2. 챔피언 이름과 함께 체력(hp), 공격력(atk), 초당 피해량(dps)를 출력.
3. 하나의 챔피언 검색 후 탐색 과정과 탐색 횟수, 검색한 챔피언 이름과 정보 출력.
```
---
```
1. strcmp 함수로 key와 루트의 챔피언 이름을 비교.
2. key가 루트 챔피언 이름보다 앞 순서라면 'root->left' 재귀호출
3. 뒤 순서라면 'root->right' 재귀호출
4. 같다면 'return' 중복처리.
```
---
###  inorder_search_and_print 
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
```
1.'add_champions' 함수를 재귀 호출할 때 '&name'이 아니고 'name'으로 호출하는 이유
- 함수 정의를 포인터로 했으므로 주소값이 아닌 name 실제 값을 넘겨줘야함.
2. 이중포인터 너무 어렵당. 처음에 함수 정의를 뭘로 했는지 신경써야겠다.
```

---
끝.