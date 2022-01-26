# 자바 ORM 표준 JPA 프로그래밍 - 기본편
https://www.inflearn.com/course/ORM-JPA-Basic#curriculum

## JPA 시작
### RDB 사용의 문제점
- DB를 조작할 때마다 매번 비슷한 CRUD SQL을 작성해야 한다.
- 객체와 RDB의 차이
  - 상속
    - RDB는 상속이 없어 테이블 별로 쿼리문을 작성해야한다.
  - 연관관계
    - 객체는 A.getB()로 참조하고, RDB는 외래키를 사용한다.
    - 객체는 단방향으로 참조하지만, RDB는 양방향 참조가 가능하다.
  - 데이터 타입
    - RDB의 데이터 타입으로 객체가 올 수 없다.
  - 데이터 식별 방법
  - 이러한 차이는 맵핑, DB 조작 등에서 불편함을 야기한다.
- 엔티티 신뢰
    ```JAVA
  class MemberSerivce{
        public void process(){
            Member member = memberDAO.find(id);
            member.getTeam();//null
            member.gerOrder().getDelivery();//null
        }
  }
  ```
  - find메소드에는 Team과 Order에 관한 join쿼리문을 구현하지 않았다.
  - 만약 다른 사람이 이를 모른 채로 사용하게 된다면 오류가 발생하게 되고 이는 엔티티의 신뢰 문제로 연결된다.
- 동일성
  - 자바의 컬렉션에서 같은 데이터를 여러번 조회해도 그 객체는 모두 같은 객체이다.
  - RDB에서 같은 데이터를 조회하면 조회할 때마다 새로운 객체가 생성되므로 서로 다른 객체가 된다.

- 이와 같은 문제를 해결하기 위해 나온 것이 JPA이다.
### JPA 소개
- JPA는 Java Persistence API의 약자로 자바 진영의 ORM 표준 기술이다.
  - ORM
    - Oriented-Relational Mapping의 약자로 객체와 관계형 데이터베이스를 이어주는 역할을 한다.
- JPA는 애플리케이션과 JDBC API 사이에서 동작한다.
- JPA는 SQL을 작성하여 보내고, 받은 결과를 객체로 반환하는 역할을 한다.
  ![image](https://user-images.githubusercontent.com/63232876/151149783-44d087b7-280b-4b21-819c-852627e1f18b.png)
- JPA 성능 최적화 기능
  - 1차 캐시와 동일성 보장
  - 트랜젝션을 통한 쓰기 지연
  - 지연 로딩
    ```java
    //지연 로딩 
    Member member = memberDAO.find(id); //SELECT * FROM MEMBER
    Team team = member.getTema();
    String name = team.getName(); //SELECT * FROM TEAM
    //미리 team을 조회하지 않고 실제 값이 필요할 때 조회한다.->엔티티 신뢰 문제 해소
    ```
### JPA 구동 방식
![image](https://user-images.githubusercontent.com/63232876/151149872-29771b4c-1ab7-4abd-89c7-2bd62edaf8cb.png)
- EntityManagerFactory는 DB당 하나만 생성
- EntityManager는 스레드 간에 공유 금지
### JPQL
- JPA로 엔티티 객체를 중심으로 직접 쿼리를 작성하지 않고 개발할 수 있다.
- 하지만 복잡한 검색은 쿼리를 작성해야 한다.
- JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어를 제공한다.
- JPQL로 테이블 중심이 아닌 엔티티 객체 중심의 쿼리문을 작성할 수 있다.

## 영속성 컨텍스트
### 영속성 컨텍스트란?
- 엔티티를 영구 저장하는 환경, 가상 DB의 역할을 의미한다.
```java
EntityManger.persist(member);
```
- persist는 DB에 저장하는 것이 아니라 영속성 컨텍스트에 저장하는 것이다.
- 실제 저장은 commit으로 저장된다(flush).
### 엔티티 생명주기
- 비영속 : 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
- 영속 : 영속성 컨텍스트에 관리되는 상태(EntityManager.persist)
- 준영속 : 영속성 컨텍스트에 저장되었다가 분리된 상태(EntityManager.detach,close,clear)
- 삭제: 삭제된 상태(EntityManager.remove)
  #### 준영속 vs 비영속
  - 영속 상태의 엔티티는 @Id로 매핑된 키값이 존재하므로 준영속 상태 엔티티 역시 식별자가 존재한다.
### 영속성 컨텍스트 장점
- 1차 캐시
  - 캐시를 사용해 DB 접근 빈도를 낮추지만, 한 트랜젝션 안에서만 유효하다. 
- 동일성 보장
  - 같은 엔티티 조회시 두 객체는 같다
- 쓰기 지연
  - 커밋을 통해 쿼리를 한 번에 DB로 보낸 후 영속성 컨텍스트를 flush 한다.
- 변경 감지
  - 영속성 컨텍스트 최초의 상태(스냅샷)와 커밋할 때의 변경점을 찾아 DB를 수정한다.
### 플러시
- 영속성 컨텍스트의 내용을 DB와 동기화한다.
- commit()과의 차이는 commit()은 플러시 후 트랜젝션을 끝내는 것을 포함하기 때문에 롤백이 불가능하다.



  