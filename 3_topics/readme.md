Topics
======

카프카에서 event를 organize하는 가장 기본적인 unit
- named container for similar events

- 전체 시스템 상에 여러개의 topic을 생성할 수 있고, topic간에 중복된 이벤트를 가지고 있어도 됨.

`queue` x `log` o  
- durable logs of events
- append only
- seek by offset, not indexed

이벤트는 immutable 하다

retention period 만큼 durable 하다.
- log이기 때문에
- disk의 파일 시스 템에 로그로 저장됨




