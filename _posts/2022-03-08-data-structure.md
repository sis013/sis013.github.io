---
title: "Data Structure Fundamental"
categories:
- 기타
keywords:
- data structure
classes: wide

use_math: true
---
**문제를 통해 배우는 자료구조**
<!--more-->


**1\. Provide a formal definition of "Graph" data structure, and describe its differences to "Tree" data strucutre.**    

A:   
그래프 *G*는 두 개의 집합 *V*와 *E*로 구성된다. *V*는 공집합이 아닌 정점(vertex)의 유한 집합이다. *E*는 정점 쌍들의 집합으로, 이러한 쌍을 간선(edge)이라고 한다.   
*V(G)*와 *E(G)*는 각각 그래프 *G*의 정점들의 집합과 간선들의 잡합을 나타낸다.  
그래프 입장에서 트리는 사이클이 없는(acyclic) 연결 그래프이다. 따라서 트리는 그래프 자료구조에 포함될 수 있다.  

**2\. Describe the formal definition of "Big-Oh", "Big-Omega", and "Big-Theta" notation.**   

A:  
점근 표기법은 같은 기능을 수행하는 두 프로그램의 시간 복잡도를 비교하거나 인스턴스 특성의 변화에 따라 실행 시간의 증가를 예측할 수 있게 하기 위함이다.   
"Big-Oh": 모든 $n$, $n >= n_0$에 대해 $f(n) <= cg(n)$인 조건을 만족시키는 두 양의 상수 $c$와 $n_0$가 존재하기만 하면 $f(n)= O(g(n))$  
"Big-Omega": 모든 $n$, $n >= n_0$에 대해 $f(n) <= cg(n)$인 조건을 만족시키는 두 양의 상수 $c$와 $n_0$가 존재하기만 하면 $f(n)= \Omega(g(n))$.   
"Big-Omega"가 유의미해지려면 $g(n)$은 $f(n) = \Omega(g(n))$이 될 수 있는 한 커야한다.  
"Big-Theta": 모든 $n$, $n >= n_0$에 대해 $c_1g(n) <= f(n) <= c_2g(n)$인 조건을 만족시키는 세 양의 상수 $c_1, c_2$와 $n_0$가 존재하기만 하면 $f(n)= \Theta(g(n))$.  
"Big-Theta" 표기법이 가장 정확하다.  

**3\. Model the World Wide Web with the "Graph" data structure.**    

A: x(모름)  

**4\. Describe the formal definition of "Big-Oh" notation and its strength and weakness compared to real experiments in performance evaluation.**  

A:  
2.를 참고  
Strength: 시간 복잡성의 중요한 부분에만 간단히 집중할 수 있다.  
Weakness: 상한값에 대한 정보만 존재하며, 이 상한이 어느 정도로 좋은지는 알 수 없다.  

**5\. Describe the differences between "Binary tree" and "B tree" data structure.**  

A:  
Definition  
"Binary Tree": *공백*이거나 루트와 왼쪽 서브트리, 오른쪽 서브트리라고 하는 두 개의 분리된 이진 트리로 구성된 노드의 유한집합이다.  
"B Tree": 차수가 $m$인 B-트리(B-tree of order m)는 공백이거나 다음 성질들을 만족시키는 m원(m-way) 탐색 트리이다.  
-  루트 노드는 적어도 두 개의 자식을 갖는다.   
-  루트 노드와 외부 노드를 제외한 모든 노드는 적어도 $[m/2]$ 개의 자식을 갖는다.   
-  모든 외부 노드들은 같은 레벨에 있다.   
*외부 노드는 탐색중에 트리에서 찾고자 하는 원소를 검색하지 못했을 때만 도달할 수 있는 노드를 의미한다.*  
Binary tree는 트리 높이가 $\log_(2)(n+1)$ 이다. 트리의 높이와 메모리 접근 횟수는 밀접한 관련이 있으므로, 트리의 메모리 접근횟수를 최소화 하기 위해
트리의 높이를 줄여야 한다. 따라서 B-tree는 차수가 2보다 높은 균형 트리이다.  
정리: 탐색횟수는 B-tree가 더 높으나, 메모리접근 횟수가 적다.  

**6\. Describe the differences between "B+ tree" and "B* tree" data structure.** 

A:  
B+ 트리에는 두 가지 종류의 노드가 있는데 바로 인덱스(index) 노드와 데이터(data) 노드이다.  
B+ tree의 인덱스 노드는 B tree의 내부 노드와 일치하고 데이터 노드는  외부 노드와 일치한다.  
인덱스 노드는 키(원소 저장x)와 포인터를 저장하고 데이터 노드는 키와 함께 원소를 저장한다(포인터 저장 x).  
데이터 노드는 왼쪽부터 오른쪽 순서대로 서로 링크되어 있고 이중 연결 리스트를 형성한다.  

--참고--  
Definition  
B+ tree: 차수가 *m*인 B+ tree 는 공백이거나 다음의 성질들을 만족시키는 트리이다.   
- 모든 데이터 노드는 같은 레벨에 위치해 있고 리프 노드이다. 데이터 노드는 원소만 포함된다.   
- 인덱스 노든 ㄴ차수 m인 B-tree를 정의한다. 각 인덱스 노드는 키를 갖고 있지만 원소를 갖고 있지는 않다.   
- $A_i(0 \le i \le n < m)$ 가 서브트리에 대한 포인터이고, $K_i(1 \le i \le n < m)$ 가 키일 때, ...(무시)  

**7\. Provide a formal definition of "Max-heap" data structure.**   

A: 
최대(최소) 트리는 각 노드의 키 값이(자식이 있다면) 그 자식의 키 값보다 작지(크지)않은 트리이다. Max-Heap은 최대 트리이면서 완전 이진 트리이다.  
Min-Heap은 최소 트리이면서 완전 이진 트리이다.  

**8\. Provide a formal definition of "B tree" data structure.**  

A:   
5.를 참고  

**9\. Provide a formal definitio of "AVL tree" data structure.**    

A: 
공백 트리는 높이 균형을 이룬다. 트리 $T$가 왼쪽과 오른쪽 서브트리인 $T_L$ 과 $T_R$ 을 ㄱ가진 공백이 아닌 이진 트리라고 할 때 아래 조건을 만족시키면 트리 $T$ 는 높이 균형을 이루며 그 역도 성립한다.   
- $T_L$과 $T_R$이 높이 균형을 이룬다.   
- $|h_L - h_R| \le 1$ ($h_L$과 $h_R$은 각각 $T_L$과 $T_R$의 높이)이다.  

**10\. Provide a formal definition of "M-way tree" data structure.**  

A:  
m-way tree는 공백이거나 다음의 성질들을 만족시킨다.  
- 루트는 최대 $m$개의 서브트리를 가지며 다음의 구조로 이루어진다.   
$n, A_0, (E_1, A_1), (E_2, A_2), ..., (E_n, A_n)$  
여기서 $0 \le i \le n < m$ 에 대해 $A_i$는 서브트리에 대한 포인터이고, $1 \le i \le n < m$에 대해 $E_i$는 원소를 의미한다. 각 원소 $E_i$는 $E_i.K$ 키를 가지고 있다.   
- $1 \le i < n$에 대해, $E_i.K < E_{i+1}.K$ 이다.  
- $E_0.K = -\infty$이고 $E_{n+1}.K = \infty$이다. $0 \le i \le n$에 대해 서브트리 $A_i$의 모든 키는 $E_{i+1}.K$보다 작고 $E_i.K$보다 크다.   
- $0 \le i \le n$에 대해 서브트리 $A_i$ 역시 m-way 탐색 트리이다.   

