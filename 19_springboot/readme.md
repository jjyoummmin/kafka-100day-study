Spring Boot
===========

준비하기
```
brew install gradle

export JAVA_HOME=$(/usr/libexec/java_home)
echo $JAVA_HOME
```

1) 카프카 클러스터 시작하고, `purchases` 토픽 생성하기 (1_quickstart 참조..)
```
docker-compose up -d 

docker exec broker \
kafka-topics --bootstrap-server broker:9092 \
             --create \
             --topic purchases
```

2) config, app, producer, consuer 코드 작성
```
# config
application.yaml

# app code
SpringBootWithKafkaApplication.java
Producer.java
Consumer.java
```

3) 빌드
```
gradle build
```

4) producer, consumer 실행
```
gradle bootRun --args='--producer'
gradle bootRun --args='--consumer'
```