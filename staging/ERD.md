# ERD와 연관관계 — 복합키 · CTE · UNION ALL

테이블 간 연관관계를 설계하면서 정리한 개념들입니다. 각 테이블은 자신의 PK를 가지는데, 이 키들이 연관관계의 **키 메이커** 역할을 합니다.

참고한 자료는 다음과 같습니다.

- JPA `@OneToMany`/`@ManyToOne`으로 연관관계 관리: https://velog.io/@goniieee/JPA-OneToMany-ManyToOne%EC%9C%BC%EB%A1%9C-%EC%97%B0%EA%B4%80%EA%B4%80%EA%B3%84-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0
- 데이터 모델링 1:N 관계 / ERD 다이어그램: https://inpa.tistory.com/entry/DB-%F0%9F%93%9A-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%AA%A8%EB%8D%B8%EB%A7%81-1N-%EA%B4%80%EA%B3%84-%F0%9F%93%88-ERD-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251105122745.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251106102237.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251106102354.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251106102406.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251106102835.png]]

## 복합키 (Composite Key)

한 컬럼으로 `JOIN`하는 것이 아니라, **여러 컬럼으로 `JOIN`** 하는 방식입니다.

## CTE (Common Table Expression)

테이블을 쓰기 편한 형태로 가공해 **가상 테이블**을 만드는 기법입니다.

예를 들어 1번 테이블의 특정 데이터만 필요한 2번 테이블이 있다면, 1번 테이블을 CTE로 가공해 연결하는 방식으로 사용합니다.

## UNION ALL

여러 조회 결과를 (중복 제거 없이) 그대로 합쳐서 반환합니다.
