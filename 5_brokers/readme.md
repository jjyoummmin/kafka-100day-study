Brokers
=======

### brokers
- 카프카 프로세스가 돌고 있는 `physical machine`
- 별개의 컴퓨터 서버, cloud 인스턴스, pod 안의 container 등등 그 형태는 다양할 수 있음
- 각각을 kafka broker 프로세스가 돌고 있는 independent machine으로 바라볼 수 있음 
- 각 브로커는 몇가지 파티션을 맡아서 manage 한다.
- 파티션에 event write, read를 담당 
- 파티션간의 replication 담당 
- 이정도로 simple하게 유지됨 



### replication
- 한 broker가 죽었을 떼를 대비해, 고가용성을 위해 파티션을 여러 브로커에 replicate해서 저장.
- 1 leader, n-1 follower
- leader 에 produce 하고 나면 알아서 복사 됨
- 개발자로서 직접 신경써야 할 것은 없음, 그냥 그런게 있다는 것만 알고 있으면 됨. 