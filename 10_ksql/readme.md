ksql
====

kafka streams는 이미 존재하는 java application에 라이브러리를 추가해 functional paradigm으로 많은 스트림 프로세싱 작업을 할 수 있다.

하지만, java app이 없거나 운영 관점에서 순수한 stream processing 업무만 하는 job을 따로 두는 것이 좋겠다고 생각 될때는 ksql db를 사용해보자.

- event streaming database for apache kafka
- 클러스터로 구성된 별개의 node 위에서 동작한다. 
- kafka cluster 바깥에, ksql 서버를 운영하고 (카프카 브로커 입장에서는 client) application이 요청할 수 있는 REST 인터페이스를 둬서 SQL 구문으로 쿼리할 수 있게함
- SQL로 쿼리하므로, app language는 무엇이든 상관 없음.
- kafka connect integration도 제공. 

예시)  
```sql
CREATE TABLE rated_movies AS
   SELECT  title,
           release_year,
           sum(rating) / count(rating) AS avg_rating
   FROM ratings
   INNER JOIN movies ON ratings.movie_id = movies.movie_id
   GROUP BY title,
            release_year;
```



ksql db application을 새로 프로비저닝 해야함.
ksql db가 schema registry에서 직접 스 키마를 가져오므로, stream 스키마를 직접 명시해 줄 필요가 없어서 편리


#### push query vs pull query

`pull query` -> 현재 상태를 출력하고 terminate (즉시 종료 되며 한 시점의 value를 리턴 함) only available against table
`push query` -> pull query 구분에 `emit changes` 키워드를 더한 것. terminate 되지 않고 계속 돌면서, 리얼타임으로 변경 사항을 계속 emit 함. ctrl+c를 눌러서 종료시키기 전까지 계속 돈다. (종료 되지 않으며 모든 변경 사항을 리턴 함.) applicable to all stream and table objects.