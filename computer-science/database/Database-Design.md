
## 데이터베이스 설계 단계도

![](images/database/db_design.png)


**개념적 설계 단계까지 DBMS 독립적**
#### 1. 요구사항 수집 및 분석
- 기존 업무문서 조사 & 인터뷰 & 설문조사 등 자료수집

#### 2. 개념적 설계 단계
- DBMS 독립적으로 진행
- 문서에 나타나는 용어(유사어) 정리 & ERD 도출 (IE 표기법 / BARKER 표기법)

#### 3. 논리적 모델링
- DBMS 종속적
- ER 모델로 표현된 개념적 스키마를 관계 데이터베이스 스키마로 사상 (DDL Script)
- 정규화: 데이터 중복과 갱신 이상이 발생하는지 검사

#### 4. 물리적 모델링
- 저장 구조와 인덱스 등을 정의
- 성능 향상을 위한 튜닝 진행(반정규화 등)

---

## 엔티티 분류
문서로부터 도출한 키워드를 행위와 데이터로 나눈다.
각각은 행위엔티티와 실체엔티티에 매핑된다.

- 실체 엔티티
	- 말 그대로 실체가 보이는 데이터를 관리하는 엔티티
	- 모델의 뼈대를 담당, 실체 엔티티 설계를 잘하면 모델의 근간이 튼튼해짐

---

## Identifier & Non-Identifier
- 식별관계(Identifier)
	- 부모 테이블의 기본키를 자식 테이블의 기본키로 설정
	- 주로 다대다 중간테이블 & 부모 엔티티 없이 존재할 수 없는 자식 엔티티에 설정
	- 구조 변경이 어렵다는 단점 (자식이 늘수록 주키가 계속 쌓이게 됨)
	
- 비식별관계(Non-Identifier)
	- 부모 테이블의 기본키를 자식 테이블의 외래키로 설정
	- 구조 변경에 용이하기 때문에 현업에서 선호

---

## References
https://yeongunheo.tistory.com/entry/DB-%EC%84%A4%EA%B3%84%ED%95%98%EB%8A%94-%EB%B2%95-feat-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%AA%A8%EB%8D%B8%EB%A7%81

