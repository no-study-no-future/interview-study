

# 벌크 연산

벌크 연산이란 단건 UPDATE, DELETE를 제외한 **다건의 UPDATE, DELETE 연산을 하나의 쿼리**로 하는 것을 의미합니다. 
JPA에서 단건 UPDATE 같은 경우에는 **Dirty Checking** 을 통해서 수행되거나 save()로도 가능합니다. 
DELETE 같은 경우에는 다건, 단건 모두 쿼리 메서드로 제공됩니다.  

이 방법의 장점은 직접 자유롭게 JPQL를 정의해서 사용할 수 있고, 하나의 쿼리로 많은 데이터를 변경할 수 있다는 점입니다.  

벌크 연산은 영속성 컨텍스트를 무시하고 실행하기 때문에, 영속성 컨텍스트에 있는 엔티티의 상태와 DB에 엔티티 상태가 달라질 수 있습니다.  

<br/><br/>


Querydsl은 스프링 데이터에서의 `@Modifying(clearAutomatically = true)`와 같은 기능이 없어 **직접 영속성 컨텍스트를 클리어해 주어야 합니다.**   

*- clearAutomatically: 해당 쿼리 메서드 실행 직후 영속성 컨텍스트를 clear 할 것인지를 지정하는 Attribute (default 값은 false)*  



<br/>

- 수정 벌크 연산

``` java
    @Test
    @DisplayName("수정 벌크 연산")
    void bulkUpdate() {
        Optional<Member> beforeMember = getMember("member1");
        
        // 벌크 연산은 1차 캐시를 포함한 영속성 컨텍스트를 무시하고 바로 Query를 실행
        // 즉, 벌크 연산 실행 시, 1차 캐시(영속성 컨텍스트)와 DB의 데이터 싱크가 맞지 않게 된다.
        long count = queryFactory
                .update(member)
                .set(member.age, member.age.add(1))
                .execute();
        // 영속성 컨텍스트의 변경내용을 데이터베이스에 반영
        em.flush();
        em.clear();
        
        Optional<Member> afterMember = getMember("member1");

        assertThat(beforeMember.get().getAge()).isNotEqualTo(afterMember.get().getAge());
    }
```

Querydsl은 JPQL로 실행되기 때문에 JPQL이 SQL로 변환된 다음 데이터베이스에서 먼저 조회합니다.  
조회한 결과를 영속성 컨텍스트와 비교하여 영속성 컨텍스트에 엔티티가 있다면 DB에서 조회해온 값을 버리고 기존 엔티티를 반환하는데요.  
이 내용은 [아래](#영속성_컨텍스트와_JPQL)에서 설명하도록 하겠습니다.  

<br/><br/>


- 삭제 벌크 연산

``` java
    @Test
    @DisplayName("삭제 벌크 연산")
    void bulkDelete() {
        Optional<Member> before = getMember("member1");
        assertThat(before.isEmpty()).isFalse();

        long count = queryFactory
                .delete(member)
                .where(member.name.like("%member%"))
                .execute();

        // JPQL은 항상 DB에서 SQL을 실행해서 결과를 조회합니다.

        // JPQL 실행 시, DB에서 데이터를 먼저 확인한 후 영속성 컨텍스트에 해당 엔티티가 있는지 확인을 합니다.
        // DB에 조회한 엔티티가 영속성 컨텍스트에 이미 있으면 DB에서 조회한 결과를 버리고 영속성 컨텍스트에 있던 엔티티를 반환합니다.

        // 삭제 벌크 연산 수행 시, 바로 DB에서 삭제 처리가 됩니다.
        // getMember() 메소드 또한 querydsl 로 jpql로 실행되기 때문에 DB에서 SQL을 실행해서 결과를 조회합니다.
        // 그 결과, 삭제된 데이터라서 DB에서 조회되지 않고 DB에서 삭제된 1차 캐시의 해당 엔티티는 불러오지 않습니다.
        // 따라서 False일 줄 알았던 결과값이 true로 나옵니다.
        Optional<Member> beforeFlush = getMember("member1");
        assertThat(beforeFlush.isEmpty()).isTrue();

        // entityManager에 있는 find() 메소드의 경우
        // 영속성 컨텍스트에서 먼저 찾고 없으면 DB에서 찾기 때문에 위에서 삭제한 멤버 조회 시, 조회가 됩니다.
        Member member = em.find(Member.class, before.get().getId());
        assertThat(member).isNotNull();

        // 영속성 컨텍스트의 변경내용을 데이터베이스에 반영
        em.flush();
        em.clear();

        // em 초기화한 이후에는 null로 조회됩니다.
        Member member1 = em.find(Member.class, before.get().getId());
        assertThat(member1).isNull();

        Optional<Member> afterFlush = getMember("member1");
        assertThat(afterFlush.isEmpty()).isTrue();

    }
```

``` java
    /**
     * name 이름인 멤버 찾기
     * @param name
     * @return
     */
    private Optional<Member> getMember(String name) {
//        return Optional.ofNullable(memberRepository.findByName(name)); // 메소드 쿼리 도 jpql 로 실행된다.
        return Optional.ofNullable(queryFactory
                .selectFrom(member)
                .where(member.name.eq(name))
                .fetchOne());
    }
```

<br/><br/>


### 벌크연산으로 발생할 수 있는 문제점 해결 방법

- **em.refresh() 사용**
    - 벌크연산 직후에 엔티티를 사용해야 한다면 em.refresh()를 사용해서 DB에 해당 엔티티를 다시 조회하면 됩니다.
    - ex) `em.refresh(beforeMember)`
- **벌크 연산 먼저 실행 후 조회하기**
    - 벌크 연산을 먼저 실행하고 나서 해당 엔티티를 조회하면 벌크연산으로 이미 변경된 엔티티를 조회할 수 있습니다.
- **벌크 연산 수행 후 영속성 컨텍스트 초기화**
    - 벌크연산 수행 직후 영속성 컨텍스트를 바로 초기화하여 영속성 컨텍스트에 남아있는 엔티티를 제거하는 것도 좋은 방법입니다.
    - 영속성 컨텍스트를 초기화하면 이후 엔티티를 조회할 때 벌크 연산이 적용된 DB에서 엔티티를 조회할 수 있습니다.


<br/><br/><br/>


# 영속성 컨텍스트와 JPQL

JPQL의 조회 대상으로는 엔티티, 단순 필드 등 다양한 종류가 있는데요.  

JPQL로 엔티티를 조회하면 영속성 컨텍스트에서 관리되지만 엔티티가 아니면 영속성 컨텍스트에서 관리되지 않습니다.  

``` sql
select m from Member m; -- 엔티티 조회 (영속성 컨텍스트에서 관리됨)
select o.address from Order o; -- 임베디드 타입 조회 (관리되지 않음)
select m.id, m.username from Member m; -- 단순 필드 조회 (관리되지 않음)
```

임베디드 타입은 조회해서 값을 변경해도 영속성 컨텍스트가 관리하지 않아 더티 체킹에 의한 수정이 발생하지 않습니다.  
물론 엔티티를 조회하면 해당 엔티티가 가지고 있는 임베디드 타입은 함께 수정됩니다.  

**조회한 엔티티만 영속성 컨텍스트가 관리합니다.**  

<br/><br/>


### ✔ JPQL로 조회 시, 데이터 베이스에서 새로 조회한 엔티티를 버리고 영속성 컨텍스트에 있는 기존 엔티티를 반환하는 이유 🤔

1. 새로운 엔티티를 영속성 컨텍스트에 하나 더 추가하기에는 영속성 컨텍스트는 기본키 값을 기준으로 엔티티를 관리하기 때문에 같은 기본키 값을 가진 엔티티는 등록할 수 없습니다.
2. 기존 엔티티를 새로 검색한 엔티티로 대체하기에는 영속성 컨텍스트에 수정 중인 데이터가 사라질 수 있으므로 이 방식도 위험합니다. 영속성 컨텍스트는 엔티티의 동일성을 보장해야 하기 때문입니다.

따라서 **영속성 컨텍스트는 영속 상태인 엔티티의 동일성을 보장하기 위해 기존 엔티티는 그대로 두고 DB에서 새로 검색한 엔티티를 버립니다.**  



<br/><br/>

- Ref.
  - [인프런 Querydsl 강의](https://www.inflearn.com/course/Querydsl-%EC%8B%A4%EC%A0%84/dashboard)