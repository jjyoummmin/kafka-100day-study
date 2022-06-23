events
======

#### apache kafka

이벤트 스트리밍 플랫폼.  Real time 스트림 데이터를 수집, 저장, 가공한다.

usecase
- distributed logging
- stream processing
- pub/sub messaging 


#### event 란?

notification + state

event format
- json
- json schema
- avro
- protobuf

kafka event model => `key-value pair`   
(카프카 내부적으로는 그냥 byte고 loosely typed.)  
`key`=> 복잡한 객체일 수도, 아니면 string이나 integer 같은 primitive value일 수도 있다.   
키로 메세지가 토픽의 어느 파티션에 저장될지 결정됨.
