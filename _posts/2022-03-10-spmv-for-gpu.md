---
title: "SpMV on GPU"
categories:
- 기타
keywords:
- data structure
---
**An Efficient Sparse Matrix Multiplication for skewed matrix on GPU, HPCC12**
<!--more-->


**개요**
- 새로운 COO format 중 하나인 Aligned_COO format을 제안.
- Power-law graph를 처리할 때 발새하는 세가지 문제점(load balancing, alignment, synchronization)을 해결하여 성능향상
- 현존 방식과 다른 concurrency degree decision (성능 향상, no extra memory overhead)
- Aligned_COO format을 이용하는 SpMV kernel을 설계 기존 COO_flat kernel에 비해 1.0~25.72배 향상됨. 
- Accuracy에 관해서도 0.89~48.8배 향상됨.

2012년도 power-law graph를 처리하려는 연구가 진행되었다. 이번 연구에서는 새로운 format을 사용하여 기존 format을 사용한 kernel로 발생하는 문제점을 어느정도 해결한 것을 보여준다. 

연구에서 가장 크게 문제삼는 것은 load balancing이다. Graph processing에서 degree가 높은 