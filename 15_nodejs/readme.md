Node.js
=======

1) 필요한 패키지 설치하기
```
npm i node-rdkafka
```

2) 카프카 클러스터 시작하고, `purchases` 토픽 생성하기 (1_quickstart 참조..)
```
docker-compose up -d 

docker exec broker \
kafka-topics --bootstrap-server broker:9092 \
             --create \
             --topic purchases
```

3) 