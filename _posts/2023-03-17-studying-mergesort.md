---
layout: post
date: 2023-03-17 17:26:15 +0900
title: "mergesort 배우고나서 해보기"
---

# 합병정렬 (Merge Sort)
1. 배열의 중간 값을 구한다.
2. 중간 값 전, 후로 재귀.
3. 정렬 후에 둘 병합.

~~모르겠다~~ 일단 해본다.

```
public static void mergesort(int [] a, int left, int right) {
    
    if (a.length == 1)
        return;
    
    int mid = a.length / 2;
    int i = 0;
    int j = mid + 1 ;
    int k = 0;
    
    int [] b = {a.length};
    
    while (i < mid + 1 && j < right + 1) {
        if (a[i] < a[j])
            b[k++] = a[i++];
            
        else if (a[i] > a[j])
            b[k++] = a[j++];
    }
    
    while (i > mid)
        b[k++] = a[j++];
    while (j > right)
        b[k++] = a[i++];
        
}
    
public static void main(String[] args) {
    int [] a = {5, 9, 8, 98, 21, 32, 2};
    mergesort(a, 0, a.length -1);  
    soutsystem.out.println(Arrays.toString(a));
}  
```

`int [] b = {a.length};`로 배열 `b`를 생성하려고 했다.
하지만 이 코드에서는 `b`배열이 초기화 되지 않아서 결과 저장이 안된다.  
&rarr; `int [] b = new int [a.length];`로 배열을 초기화했다.

뭔지도 모르고 만들었더니 `merge`는 작성하지도 않았다.

---

- `merge` : 두 배열을 하나로 합치는 메소드.
- `mergesort` : 하나의 배열을 분할, 정렬, 합치는 메소드.

&rarr; **mergesort는 merge를 호출하는 과정이 포함됨.**

---

`mergesort`메소드로 분할 후에, `merge`메소드를 호출해보자.

일단, `merge` 메소드 작성. 뭐였지? *합치는거*
```
public static void merge(int [] a, int left, int mid, int right) {
    
    int i = 0;
    int j = mid + 1;   // 2개로 분할 후 뒤에 있는 배열의 첫 index
    
    int b [] = new int [a.length]; // 아까 고친거
    int k = 0;         // 배열 b의 index
    
    while (i < mid + 1 && j < right + 1) {
    // i = mid (앞 배열의 마지막까지) && j = right (뒤 배열의 마지막까지)
    
        if (a[i] < a[j])               // a의 i번째, j번째 index에 있는 값 비교
                b[k++] = a[i++];       // 더 작은 숫자 (a[i])를 b[k]에 대입하고   
        else if (a[i] > a[j])          // a와 b의 index 한칸 이동
                b[k++] = a[j++];
    }

    while (i > mid)                    // 비교군 중 한쪽 배열이 끝났을 때
        b[k++] = a[j++];               // 남은 (안 끝난) 배열 싹 다 b에 대입
    while (j > right)
        b[k++] = a[i++];  
}
```

다음은, `mergesort`메소드 작성. *나누고, 정렬하고, `merge` 불러서 합치고.*
```
public static void mergesort(int [] a, int left, int right) {

    if (a.length == 1)      // 혹시 배열이 쪼갤 수 없으면 그대로 반환
        return;
        
    int mid = a.length / 2;
    
    mergesort(a, left, mid);    // mid 기준으로 분할 (앞 배열)
    mergesort(a, mid + 1, right);  // 뒤 배열
                              
    merge(a, left, mid, right);    // 병합
}
```

항상 생각했던 건데, `main`메소드에서 어떻게 호출해야될지 모르겠다.
1. `a`배열 정의
2. `mergesort`실행해서 `a`배열 병합정렬
3. `a`배열 출력
```
public static void main(String[] args) {
    int [] a = {5, 9, 8, 98, 21, 32, 2};
    mergesort(a, 0, a.length - 1);
    system.out.println(Arrays.toString(a));
}
```
---

### 어 근데 왜 오버플로우 뜨냐

~~인터넷 검색 결과~~

>'java.lang.StackOverflowError' 오류는 보통 무한 재귀 호출로 인해 발생합니다.

>'mergesort'메소드에서 재귀적으로 자신을 호출하고 있을 수 있습니다.

뭔 소린지 모르겠다.

---

### 이것저것 만져본 결과

1. 위 `mergesort`메소드에서 `if (a.length == 1)` 문장을 `(a.length <= 1)`로 바꿨다.  
2. `mid`를 구하는데에 `left`와 `right`인덱스를 이용해야한다.  
&rarr; `int mid = (left + right) / 2`로 바꿨다.
3. *이런미친.* `while`에 조건을 반대로 썼었다. `for`이랑 너무 똑같게 생각한거같다.  
&rarr; 조건에 있는 부등호 바꿔준다.

---

### 다시 `a` 배열 정렬
정렬한 `b` 배열을 다시 `a` 배열로 복사해주질 않아서 정렬되기 전 `a`배열만 출력된다.

```       
 for (int m = left; m <= right; m++)
       a[m] = b[m - left];
```

해석을 해보자면...  
`for (int m = left; m <= right; m++)`  
&rarr; `m` int형 변수에 `left`를 대입.   
&rarr; `m` 이 `right`에 닿을때까지 (index 끝에 닿을 때까지)  
&rarr; `m++`

`a[m] = b[m - left]`
&rarr; `a`배열 의 m번째 인덱스의 값에 `b`배열 (m - left)번째 인덱스 값 대입  
&rarr; 왜 (m - left)일까? : 배열 `b`는 `left`부터 시작하기 때문이다. `a`배열의 시작은 left (= m)가 맞지만, `b`배열의 시작은 left가 아니라 0이다.  
따라서 (m - left)를 넣어주어 `b`배열 인덱스를 0, 1, 2, 3 늘려주는 것이다.

#### 끗


