
## Unified Modeling Language

![](images/software-engineering/uml.png)

- 개발하려는 소프트웨어의 전체적인 윤곽을 파악할 수 있도록 12가지 다이어그램 제시
- 실제 프로젝트에서는 특성에 맞게 3~5개의 다이어그램 작성

---

#### 1. 유스케이스 다이어그램

- 요구분석 이후 만들어진 산출물을 표현하는 도구
	 - 구조적 분석 방법: DFD(Data Flow Diagram)
	 - 정보공학 방법: ERD(Entity Relationship Diagram)
	 - 객체지향 방법: Usecase Diagram

- 유스케이스 다이어그램은 시스템이 제공하는 기능을 다음 세가지로 표현
	- 유스케이스(기능)
	- 액터(사용자)
	- 사용자와 기능 사이 관계

---

#### 2. 액터
- `사용자 액터`: 시스템을 사용하는 사람(역할)
- `시스템 액터`: 해당 프로젝트에 속하지 않지만 데이터를 주고받는 등 서로 연동되는 또 다른 시스템, 유스케이스에서 시스템 엑터 방향으로 화살표 표현
	- ex) 학사관리시스템에서 휴학신청 시 미반납 도서를 확인하기 위한 도서관리시스템
- `주요 액터`: 시스템에게 작업의 실행을 요구하는 능동적 입장의 액터
- `보조 액터`: 유스케이스로부터 요청을 받아 수동적으로 작업을 하는 액터
	- ex) 시스템을 유지 및 관리하는 사람 
- `프록시 액터`: 액터와 시스템의 중간 위치에서 무언가를 대신해주는 액터
	- ex) 학사관리 시스템에서 교수 대신 교과목을 추가 등록하는 일을 하는 조교

##### 액터 이름 규칙
- 이름만 봐도 액터의 의미를 알 수 있도록 정한다
- 역할을 나타내는 단어로 사용자 액터의 이름을 정한다
	- ex) 직원(X), 학사담당직원(O)
- 시스템 액터는 시스템 이름을 사용한다

##### 액터 목록

![](images/software-engineering/actor-list.png)

각 액터에 관한 설명을 붙인 액터 목록을 만들어, 그 액터가 시스템을 사용해 어떤 일을 하는지 한 눈에 파악하도록 한다

---

#### 3. 유스케이스

- 사용자가 시스템을 통해 사용하고 싶은 기능
	- ex) 수강신청, 성적등록, 교과목등록 등
- 유스케이스는 작은 단위의 기능
- 여러 유스케이스가 모여 서브시스템을 이루고, 서브시스템이 모여 개발시스템을 구성
- 전체 시스템 = 유스케이스의 모음 

##### 유스케이스 식별
- 유스케이스를 찾는 과정을 개발자가 하지만 사용자 관점에서 정의
- 설계나 구현 환경을 염두하지 않고 **사용자 요구사항만으로 독립적으로 정의**

##### 유스케이스 검증
- 시스템이 아닌 수작업으로 이루어지는 것은 유스케이스에 포함될 수 없음
- 유스케이스 내 이벤트 흐름 전체를 액터가 사용하는지 확인
	- ex) 수강관리 -> 수강신청, 수강신청 조회

##### 유스케이스 이름
- 사용자 관점에서 이해할 수 있는 이름 사용
	- ex) 성적정보생성(X), 성적등록(O), 성적조회(O)
- 어떤 기능을 하는지 알 수 있도록 동사형 명사 사용
- 사용자가 시스템을 통해 얻으려는 최종 목적이 나타나는 이름 사용
	- ex) 성적등록

##### 유스케이스 목록
- 위 액터 목록과 유사하게 유스케이스 목록을 만든다
- 사용자나 개발자가 유스케이스 기능을 이해하는데 도움
- 정상흐름과 대안흐름(예외) 둘 다 포함하여 작성

---

#### 4. 관계

![](images/software-engineering/usecase-diagram.png)

##### 액터 -> 유스케이스 관계
- 액터와 유스케이스는 연관관계를 방향성을 갖는 실선으로 표현 
- 화살표는 데이터가 흘러가는 방향이 아닌, 제어의 흐름을 나타냄

##### 유스케이스 -> 액터 관계
- 유스케이스의 수행 결과를 액터에게 알려줄 때 사용 
- 유스케이스에서 액터로 방향성을 갖는 실선으로 표현
- 통보기능은 시스템 내에서 이루어져야 함

##### 유스케이스 -> 시스템 액터 관계
- ex) 휴학승인(유스케이스) -> 도서관리시스템(시스템 액터)
- ex) 주문결제(유스케이스) -> 신용카드 인증 회사(시스템 액터)

##### 액터의 일반화 관계
- 로그인과 같은 모든 액터가 수행해야 하는 기능은 일반화한 액터로 표현
	- 교수, 조교, 학생.... -> 사용자

##### 액터 간의 연관 관계
- 액터가 또 다른 액터를 통해 유스케이스를 이용하는 것
	- ex) 학생이 조교를 통해서만 휴학신청이 가능한 경우

##### 포함 관계
- 화살표 방향이 기본 유스케이스에서 피포함 유스케이스 방향, 점선으로 나타냄
- 스테레오 타입으로 <<\include\>> 표기
	- 피포함 유스케이스: 공통으로 사용하기 위해 만든 유스케이스
	- 기본 유스케이스: 피포함 유스케이스를 호출하는 유스케이스
- 로그인 유스케이스와 같은 **선행 조건과 혼동하지 않도록 주의**

##### 확장 관계
- 기준 유스케이스와 확장 유스케이스 사이 관계
- 확장 유스케이스 - - <<\extend\>> - - > 기준 유스케이스
- 특정 조건에 따라 확장 유스케이스가 수행되는 경우 적용
	- ex) 글을 등록할 때 파일을 첨부하는 기능을 선택적으로 수행할 수 있다

---
# References
https://m.blog.naver.com/msoffice7/221740368928