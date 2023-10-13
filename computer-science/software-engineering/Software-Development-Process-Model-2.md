
## Agile Process Model

- 애자일(agile): 날렵한, 민첩한
- 고객의 요구에 민첩하게 대응하고, 그때그떄 주어지는 문제를 풀어나가는 방법론
- 대표적인 애자일 방법론: XP(eXtreme Programming), Scrum, Crystal

#### 애자일의 기본 가치 (고객, 소통)
폭포수 모델같이 계획을 기반으로 하는 산출물 위주의 무거운 방법론과 대비,
애자일 연합: 가벼운 프로세스 방법론의 공통적인 특성을 모은 애자일 선언문 발표

- 프로세스와 도구 중심이 아닌, 개개인과의 상호 소통 중시
- 문서 중심이 아닌, 실행 가능한 소프트웨어 중시
- 계약과 협상 중심이 아닌, 고객과의 협력 중시
- 계획 중심이 아닌, 변화에 대한 민첩한 대응 중시

#### 애자일 프로세스 개발 방법
1. 가장 기본이 되는 기능만 1차 요구사항으로 분석하고 이를 반복으로 나누어 개발
2. 사용자가 릴리스 1을 사용하는동안 개발자는 2차 개발 진행
3. 위 과정 반복

- 반복적인 개발을 통한 잦은 출시를 목표로 한다
- 실행 가능한 프로토타입을 만들어 사용자에게 자주 확인 받는다
- 빠른 시간안에 일부지만 소프트웨어를 사용할 수 있게 하는 것을 중요시 한다

---

## 스크럼

![](images/software-engineering/scrum.png)

- 소프트웨어 개발보다는 팀의 개선과 프로젝트 관리에 중점을 둔 애자일 방법론
- 경험적 관리 기법 중 하나
- 구체적인 프로세스 지침보단 개발팀을 효율적으로 운영하는 방식

#### 1. 제품 기능 목록(Product Backlog)
- 사용자가 요구하는 제품의 기능 목록
- 고객 측 대표(제품 책임자, Product Owner)가 요구사항에 대한 우선순위 결정
- 즉 제품 책임자가 요구사항 목록에 우선순위를 매겨 제품 기능 목록 작성
- 제품 기능 목록은 일반적으로 한 주기동안은 수정되지 않음

	# **사용자 스토리**
	- 제품 기능이 도출되면 각 기능을 간략하게 서술(주로 포스트잇)
	- 스토리가 완료되는 조건 서술 (Given-When-Then)
	- 테스트 케이스 도출을 위해 비기능 요소는 구체적이고 정확하게 서술 
		- 속도가 빨라야 한다 (X), 동접자 2000명까지 1기가바이트 속도 유지 (O)
	- 사용자가 필요로 하는 내용 위주로 간략하게 서술
		- 학생은 수강신청을 한다, 교수는 성적 등록을 한다 등등
		
	# **스토리 포인트**
	- 요구사항의 규모를 측정하는 단위로 업무량을 이용해 산정
	
	# **중요도**
	- 우선순위를 매기기 위해 사용자 스토리의 중요도를 판단하는 일은 주로 업무 분석가가 담당한다.


#### 2. 스프린트 계획 수립
- Sprint: 전력 질주, 작은 단위의 개발 업무를 단기간 내 전력 질주해 수행한다는 의미
- 스프린트가 정해지면 중간에 멈추지 않고 전력 질주해서 끝내야 함
- 개발 완료 여부와 관계 없이 정해진 일정이 지나면 종료

	# **스프린트 계획 회의** 
	- 하나의 스프린트에 어떤 사용자 스토리를 몇 개 포함할 것인지 결정
	- 스프린트 기간 결정 (보통 1~4주)
	- 전체적인 스프린트 계획 회의와 세부적인 스프린트 계획 회의로 나뉨
	
	# **스크럼 마스터**
	- 스프린트 중 외부 개발 방해 요소 차단 역할

	# **전체적인 스프린트 계획 회의**
	 - 제품 책임자를 통해 사용자가 원하는 바를 파악하는 데 중점
	 - 스크럼 마스터는 제품 기능 목록(Product Backlog) 검토, 팀원과 토의

	# **세부적인 스프린트 계획 회의**
	- 우선순위가 높은 항목을 어떻게 구현할 것인지 구체적 작업 계획 세우기
	- 제품 기능 목록에서 개발할 항목을 결정하고 스프린트 구현 목록 작성
	- 팀원들은 작업을 수행하는 데 소요되는 시간 추정

	# **스프린트 구현 목록(Sprint Backlog) 작성**
	- 제품 기능 목록에 있는 스토리 중에서 선택해 작성
	- 스프린트 기간 내 완료할 수 있는 수준에서 결정
	- 세부 작업 항목, 작업자, 예상 작업 시간 등의 정보 작성
	- SP(스토리 포인트, Story Point), MD(연 작업 시간, Man Day)


#### 3. 스프린트 수행
- 스프린트 현황판, 일일 스크럼 회의, 소멸 차트

 # **소멸 차트**
	 - 일일 스크럼 회의 이후 스크럼 마스터가 작성
	 - 가로축(시간), 세로축(SP), 계획 그래프(점선), 실제 그래프(실선)
	 - 날짜별 남은 작업을 나타내는 그래프, 시간이 지남에 따라 차트가 감소
	 - 계획 그래프와 실제 그래프를 비교하여 개발 진행 현황을 직관적으로 파악 가능
 
 # **일일 스크럼 회의**
	- 매일, 서서, 짧게(약 15분), 전원 참석, 스프린트 현황판 업데이트
	- 어제 한 일, 오늘 할 일 발표
		
 **스프린트 현황판**
   - ex) 칸반보드
   - 개발 팀의 개발 현황(할 일, 진행중, 완료, 스프린트 목표) 등을 나타냄


#### 4. 스프린트 개발 완료

- 최종 제품 완성


#### 5. 스프린트 개발 완료 후
- 스프린트 검토 회의
	- 최종 제품을 참석자에게 시연하고 요구사항에 부합하는지 검토
	- 개선할 점에 대한 피드백 받기

- 스프린트 회고
	- 규칙을 잘 준수했는지, 개선할 점이 있는지 등을 회고
	- 단점보다는 강점을 찾아 더 극대화하는데 주안점을 둠
	- 문제점은 확인하고 기록하는 정도로만 진행


#### 6. 배포 목록(Release Backlog) 작성
- 배포 목록: 제품 기능 목록의 항목 중 이번 배포 본에 포함하기로 결정한 것

---

### 스크럼 개발 관련자의 역할

#### 제품 책임자
- 제품 기능 목록 작성
- 비즈니스 관점에서 우선순위와 중요도를 매기고 새로운 항목 추가
- 스프린트 계획 수립까지만 역할 수행, 스프린트 시작 이후 팀 운영에 관여 X

#### 스크럼 마스터
- 제품 책임자를 돕는 조력자
- 업무를 배분만 하고, 일은 강요하지 않음
- 개발 과정에서 스크럼의 원칙과 가치를 지키도록 지원
- 개발 과정에 방해될 요소를 찾아 제거

#### 스크럼 팀
- 사용자 요구사항을 사용자 스토리로 도출하고 구현
- 기능을 작업 단위로 나누고, 일정이나 속도를 추정해서 제품 책임자에게 알려줌
- 하나의 스프린트에서 생산된 결과물을 제품 책임자에게 시연
- 매일 스크럼 회의에 참여하여 진척 상황 점검


---
## References
https://medium.com/dtevangelist/scrum-dfc6523a3604





