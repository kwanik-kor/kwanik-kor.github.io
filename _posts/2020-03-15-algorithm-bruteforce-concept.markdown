---
layout: post
title: "브루트 포스(Brute Force)란?"
subtitle: "일명 무식하게 풀기"
categories: algorithm
tags: bruteforce
commetns: true
---

## 개요
>   컴퓨터의 빠른 계산 능력을 이용해 가능한 경우의 수를 일일이 나열하면서 답을 찾는 방법, 다시 말해 '완전 탐색(exhaustive search)'이라고 부른다.

---
1.  개념
알고리즘 문제를 풀이하다 보면 때로는 쉬운 문제를 어렵게 풀려고 한다. 아직 성장하고 공부하는 현재의 나는 이러한 실수를 자주하는 것 같다.  이 때 한 번 쯤 던져봐야할 질문은 바로 __"무식하게  풀 수 있을까?"__ 이다. 이름 그대로 가능한 모든 방법을 탐색해보는 것으로, 무식해 보일 수도 있지만 어찌보면 '속도가 빠르다'는 컴퓨터의 장점을 가장 잘 살린 방법이기도 하다.

1.  언제 써야 할까?
우선 첫 번째 조건은 __특별한 조건이 없어야 한다.__ 다시 말해, 가능한 모든 경우의 수를 세어 보는 것과 같은 문제에 적용하기 좋다. 예를 들어 순열과 조합 만들기가 대표적인 예시라고 할 수 있다.
  
__순열(permutation)__ 이란 _'서로 다른 $$N$$개의 원소를 일렬로 줄 세우기'_ 이며, __조합(combination)__ 이란 _'서로 다른 $$N$$개의 원소를 순서 없이 골라내기'_ 다. 이 때 조합은 이항계수로 풀이할 수 있는데 이는 조금 이따 다시 보기로 한다.
  
그런데 모든 순열과 조합 문제를 완전탐색을 통해서 풀어서는 안된다. 단적으로 순열의 수는 $$N!$$을 띄게 되는데 N이 10만 넘어가더라도 상당한 수의 연산을 해야하기 때문이다. 이 말인 즉슨, 입력의 크기가 작거나, 탐색하는 횟수가 극히 제한되는 문제에서 이를 활용할 필요가 있다는 의미다.

1.  그럼 어떻게?
    +   완전탐색으로 풀 수 있을까?
        완전탐색의 시간 복잡도는 __'가능한 후보의 모든 수'__ 로 표현이 가능한데, 이는 결국 답의 수에 비례해서 걸리는 시간이 증가하게 된다. 따라서 제한 시간안에 '완전탐색'을 통해 답을 생성해 낼 수 있을지 생각해보아야 한다.
    +   분할하자.
        원하는 답을 찾아가기까지의 과정을 여러개의 선택으로 분할한다.
    +   분할된 조각을 합쳐가자.
        분할을 통해 만들어진 조각에서 답의 일부분을 만들어내고 나머지 부분을 재귀를 통해서 완성한다.
    +   답을 생성하자.
        조각이 하나만 남았을 경우 혹은, 하나도 남지 않은 경우에는 답을 생성한 것이므로 이를 채택한다.

1.  필요한 개념
    +   재귀호출(recursive)
        일반적인 큰 문제는 작은 문제들로 다시 나눌 수 있다. 이 때,  각각 나뉘어진 작은 작업단위들은 결국 같은 작업을 하게되는데, 이를 하나의 함수로 만들고 부분적인 문제를 해결하고 나면 나머지를 다시 자신을 호출하여 실행하는 것을 의미한다.
    +   말보다는 코드 _ 조합을 완전탐색을 통한 재귀로 만들어보자

    ```java
    public void recursiveSelect(int n, ArrayList<Integer> list, int toPick){
        //원하는 개수(toPick)만큼 뽑아냈기 때문에 조합을 출력해주고 리턴.
        if(toPick == 0){
            for(Integer i : list)
                System.out.print(i + " ");
            System.out.println();
            return;
        }
        //list가 비어있다면, 0부터 그렇지 않다면 고를 수 있는 가장 작은 수를 선택하기
        int min = list.isEmpty()? 0 : list.get(list.size() - 1) + 1;
        for(int next = min; next < n; next++){
            //다음 선택을 list에 추가해주고 재귀호출
            list.add(next);
            recursiveSelect(n, list, toPick - 1);
            //다른 선택지로 넘어가기 위해 선택한 요소를 다시 제거
            list.remove(list.size() - 1);
        }
    }
    ```
    위 코드는 0 ~ n 까지의 수 중 toPick만큼의 조합을 만들어내는 것이다.  

    이를 재귀 호출로 해결하지 않았다면 굉장한 중첩 for문을 사용해야 할 것이다. 하지만 재귀함수를 통해 이를 일종의 모듈화 해둠으로써 n과 toPick만 바꾼다면 언제든지 원하는 수의 조합을 이끌어낼 수 있게 된다.
      
    다른 완전탐색을 풀이함에 있어도 재귀함수에 대한 개념과 사용이 익숙해지지 않는다면 다소 어려움을 겪을 수 있는 것 같다. ~~내가 그렇다.~~

1.  결론
    완전탐색은 결국 만들어질 수 있는 모든 경우의 수 만큼 시간이 걸리게 되므로, 먼저 이를 선택할 것인지에 대한 판단을 빠르게 내린다. 이 다음에 문제를 작은 문제로 분할하여 재귀를 통해 작은 정답들을 모아 큰 하나의 답안을 도출해낸다.
  
    다른 실전문제들을 풀면서 계속해서 연습하자  

---
참고도서: 프로그래밍 대회에서 배우는 알고리즘 문제해결 전략 1 _ 인사이트 _ 06.무식하게 풀기(p.145)