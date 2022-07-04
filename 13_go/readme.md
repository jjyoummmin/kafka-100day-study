Go
==

1) go 모듈 생성하기, kafka dependency 다운로드 받기
```
# 현재 디렉토리에 go.mod 파일 생성됨
go mod init kafka-go-getting-started  

# 패키지 및 관련 dependency 다운로드 (go.mod, go.sum 파일 업데이트)                     
go get github.com/confluentinc/confluent-kafka-go/kafka    
```

2) 카프카 클러스터 시작하고, `purchases` 토픽 생성하기 (1_quickstart 참조..)
```
docker-compose up -d 

docker exec broker \
kafka-topics --bootstrap-server broker:9092 \
             --create \
             --topic purchases
```

3) producer app 작성 및 컴파일
```
go build -o out/producer util.go producer.go
```

4) consumer app 작성 및 컴파일
```
go build -o out/consumer util.go consumer.go
```

5) producer, consumer 실행하기
```
./out/producer getting-started.properties
./out/consumer getting-started.properties 
```