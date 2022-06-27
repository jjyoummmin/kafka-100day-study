Producers
=========

이젠 카프카 클러스터 바깥으로 나와서  kafka를 사용하는 application에 대해서 생각해보자. (=> `producers`, `consumers`)

카프카 native language인 Java로 쓰여진 kafka producer API를 살펴보자

`kafkaProducer` 클래스 => 클러스터 연결 담당. 객체 생성 인자로 config 정보 넘김 (bootstrap broker 주소, security config, 등등)

`ProducerRecord` 클래스 => topic에 write 하고 싶은 key, value pair를 들고 있는 객체 (event wrapper)

```java
producer.send(producerRecord)
```

```java
    try (KafkaProducer<String, Payment> producer = new KafkaProducer<>(props)) {

        for (long i = 0; i < 10; i++) {
            final String orderId = "id" + Long.toString(i);
            final Payment payment = new Payment(orderId, 1000.00d);
            final ProducerRecord<String, Payment> record = 
            new ProducerRecord<>("transactions", 
                                            payment.getId().toString(), 
                                            payment);
            producer.send(record);
        }
    } catch (final InterruptedException e) {
        e.printStackTrace();
    }
```

표면적으로 생각할 부분은 이정도가 다임.


그외에 내부적으로 더 처리해주는 부분
- connection pool 관리
- 네트워크 버퍼링
- broker의 message ack 기다린후 buffer space free 하기
- 메세지 재전송
- 등등

어떤 파티션으로 메세지를 보낼지 결정하는 것은 producer다. (round-robin, hash function..)