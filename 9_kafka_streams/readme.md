Kafka Streams
=============

#### 등장 배경
카프카 기반으로 작성한 app이 커질수록, consumer가 점점 복잡해진다.

예를 들어 개인을 식별할 수 있는 정보를 가린다든지, 메세지 format을 바꾼다든지 하는 단순한 stateless 작업에서 각종 aggregation, enrichment 복잡한 stateful 작업들이 필요해진다.

consumer api 섹션에서 살펴봤던 코드를 보면 정말 별게 없다.

Timewindow, 늦게 도착하는 메세지 처리, 순서 안맞는 메세지 처리, lookup table 을 핸들링 하는 코드를 직접 짜야하고, 특히 aggregation, enrichment 같은 것들은 state 저장하고 있어야함.   
(프로세스 heap 메모리에 -> fault tolerance가 중요해짐..)

그래서 카프카에서 대신 제공하는 `stream processing api` 가 바로 kafka streams이다!


#### 특징
consumper grouop에 기반을 둔 scalable computation 가능.
(여러대의 consumer 인스턴스가 stream processing 워크로드를 나눠서 처리한다.)

state 관리를 `off-heap` 으로 함.  
`로컬 디스크`에 저장, 또 같은 state를 카프카 클러스터의 토픽안에도 저장함.
(consumer 노드가 아예 사라질 수도 있으니까, 토픽 안에도 저장해서 다시 가져올 수 있도록 함.)

카프카 스트림즈는 그냥 java library이고, 별개로 띄워야하는 인프라 컴포넌트 같은 것이 아니기 때문에 spring boot, micronaut 같은 rest api를 serve하는 기존 서비스에 스트림 프로세싱 기능을 붙이는 것은 아주 간단하다.  
(integrates with your services as a library)

#### 예시
```java
StreamsBuilder builder = new StreamsBuilder();

builder.stream("raw-movies", Consumed.with(Serdes.Long(), Serdes.String())).mapValues(Parser::parseMovie).map((key, movie) -> new KeyValue<>(movie.getMovieId(), movie)).to("movies", Produced.with(Serdes.Long(), movieSerde));

KTable<Long, Movie> movies = builder.table("movies", Materialized.<Long, Movie, KeyValueStore<Bytes, byte[]>>as("movies-store").withValueSerde(movieSerde).withKeySerde(Serdes.Long()));

KStream<Long, String> rawRatings = builder.stream("raw-ratings", Consumed.with(Serdes.Long(), Serdes.String()));
KStream<Long, Rating> ratings = rawRatings.mapValues(Parser::parseRating).map((key, rating) -> new KeyValue<>(rating.getMovieId(), rating));

KStream<Long, Double> numericRatings = ratings.mapValues(Rating::getRating);

KGroupedStream<Long, Double> ratingsById = numericRatings.groupByKey();

KTable<Long, Long> ratingCounts = ratingsById.count();
KTable<Long, Double> ratingSums = ratingsById.reduce((v1, v2) -> v1 + v2);

KTable<Long, Double> ratingAverage = ratingSums.join(ratingCounts, (sum, count) -> sum / count.doubleValue(),Materialized.as("average-ratings"));

ratingAverage.toStream().to("average-ratings");

KTable<Long, String> ratedMovies = ratingAverage.join(movies, (avg, movie) -> movie.getTitle() + "=" + avg);

ratedMovies.toStream().to("rated-movies", Produced.with(Serdes.Long(), Serdes.String()));
```