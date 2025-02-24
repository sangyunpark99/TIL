### SubQuery

```java
    @Test
    public void subQuery() { // 서브 쿼리

        QMember memberSub = new QMember("memberSub"); // 서브 쿼리의 Alias가 달라야 합니다.

        List<Member> result = queryFactory
                .selectFrom(member)
                .where(member.age.eq(
                        JPAExpressions
                                .select(memberSub.age.max()) // Alias가
                                .from(memberSub)
                ))
                .fetch();

        assertThat(result).extracting("age").containsExactly(40); //
    }
```

서브 쿼리는 Alias가 겹치기 때문에 따로 선언해 주어야 합니다.

```sql
select m1_0.member_id,m1_0.age,m1_0.team_id,m1_0.username from member m1_0 
            where m1_0.age=(select max(m2_0.age) from member m2_0);
```
이런식으로 쿼리가 발생하게 됩니다.  

```java
@Test
    public void subQueryGoe() { // 서브 쿼리

        QMember memberSub = new QMember("memberSub"); // 서브 쿼리의 Alias가 달라야 합니다.

        List<Member> result = queryFactory
                .selectFrom(member)
                .where(member.age.goe(
                        JPAExpressions
                                .select(memberSub.age.avg()) // Alias가
                                .from(memberSub)
                ))
                .fetch();

        assertThat(result).extracting("age").containsExactly(40); //
    }
```
```sql
select m1_0.member_id,m1_0.age,m1_0.team_id,m1_0.username from member m1_0 where m1_0.age>=(select avg(cast(m2_0.age as float(53))) from member m2_0);
```

```java
@Test
    public void subQueryIn() { // 서브 쿼리

        QMember memberSub = new QMember("memberSub"); // 서브 쿼리의 Alias가 달라야 합니다.

        List<Member> result = queryFactory
                .selectFrom(member)
                .where(member.age.in(
                        JPAExpressions
                                .select(memberSub.age) // Alias가
                                .from(memberSub)
                                .where(memberSub.age.gt(10))
                ))
                .fetch();

        assertThat(result).extracting("age").containsExactly(20, 30, 40); //
    }
```
```sql
select m1_0.member_id,m1_0.age,m1_0.team_id,m1_0.username from member m1_0 where m1_0.age in (select m2_0.age from member m2_0 where m2_0.age>10);
```

```java
 @Test
    public void selectSubQuery() {

        QMember memberSub = new QMember("memberSub");

        List<Tuple> result = queryFactory.select(member.username,
                        JPAExpressions
                                .select(memberSub.age.avg())
                                .from(memberSub)
                )
                .from(member)
                .fetch();

        for (Tuple tuple : result) {
            System.out.println("tuple = " + tuple);
        }
    }
```
```sql
select m1_0.username,(select avg(cast(m2_0.age as float(53))) from member m2_0) from member m1_0;
```

출력 결과 
```
tuple = [member1, 25.0]
tuple = [member2, 25.0]
tuple = [member3, 25.0]
tuple = [member4, 25.0]
```

JPA JPQL은 서브쿼리의 한계점으로 from 절의 서브쿼리(인라인 뷰)는 지원하지 않는다.  
당연히 Querydsl도 지원하지 않는다.  

from절의 서브쿼리 해결 방안  
서브 쿼리를 join으로 변경합니다.(가능한 상황도 있고, 불가능한 상황도 있습니다.)  
애플리케이션에서 쿼리를 2번 분리해서 실행합니다.  
nativeSQL을 사용합니다.  

