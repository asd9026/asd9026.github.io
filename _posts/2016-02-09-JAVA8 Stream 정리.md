---
layout: post
title:  "JAVA8 Stream 정리"
date:   2016-02-09 01:40:00 +0530
categories: jekyll update
---

## JAVA8 Stream
JAVA8에 새롭게 추가된 것 중 하나가 Stream이다.
이 Stream API가 추가되면서 복잡한 데이터를 처리할 때 질의로 표현 할 수 있게 되었다.
기존에는 컬렉션을 통하여 작성하였지만 이제 Stream API를 사용하므로서 두 가지 장점을 얻을 수 있다.
- 원하는 값을 얻기 위해 직접 구현할 필요없이 SQL query처럼 원하는 값을 표현해 주면 된다.
<br>
ex)

| SQL query | JAVA8 Stream |
| --- | --- |
| SELECT AVG(속성) FROM 테이블 | 컬렉션.stream().map( x -> x.getInt() ).average(); |

- 처리해야할 컬렉션이 매우 큰 사이즈인 경우 속도 향상을 위한 병렬처리를 쉽게 구현할 수 있다.
<br>
ex) ```.stream()```을 ```.parallelStream()```으로 변경하면 내부적으로 멀티코어로 동작

<br>

다음은 Stream 적용 유무에 따른 예제이다.

- JAVA7 이하에서의 처리 방법

```
List groceryTransactions = new ArrayList();
for (Transaction t : groceryTransactions) {
    if (t.getType() == Transaction.GROCERY) {
        groceryTransactions.add(t);
    }
}
Collections.sort(groceryTransactions, new Comparator() {
    public int compare(Transaction t1, Transaction t2) {
        return t2.getValue().compareTo(t1.getValue());
    }
});

List transactionIds = new ArrayList();
for (Transaction t : groceryTransactions) {
    transactionIds.add(t.getId());
}
``` 

- JAVA8에서의 stream을 적용한 처리 방법

```
List transactionsIds = 
    transactions.stream()
        .filter(t -> t.getType() == Transaction.GROCERY)
        .sorted(Comparator.comparing(Transaction::getValue).reversed())
        .map(Transaction::getId)
        .collect(Collectors.toList());
```

<br>

stream을 적용함으로써 코드가 간편화 되어 가독성 또한 높아진 것을 알 수 있다. 물론 아직 키워드를 잘 모르기 때문에 이해하기 어려울 수도 있다.

<br>

하지만 위의 코드를 하나씩 뜯어 보면 더욱 이해하기 쉽다는 것을 금방 알 수 있게 된다.

- stream() 메소드: List의 아이템을 Stream으로 가져오는 역활
- filter() 메소드: 주어진 조건(predicate)을 충족하는 아이템을 가져오는 역활
- sorted() 메소드: 주어진 comparator에 맞게 정렬
- map() 메소드: 아이템에서 원하는 정보를 추출
- collect() 메소드: 컬렉션으로 반환
 
여기서 collect() 메소드를 제외한 다른 메소드들은 stream을 반환하기 때문에 이들을 서로 연결하여 파이프라인형태로 처리할 수 있었다.

<br>

### 참고 람다 표현식, 메소드 참조
람다 표현식(t -> t.getType())과 메소드 참조(Transaction::getValue)의 의미는 간단하다.
<br>
- 람다 표현식의 경우 t는 임의의 변수로 x, z, k... 등 어떠한 것이든 사용해도 된다. 이러한 임의의 변수는 stream으로 가져온 컬렉션에서 각각의 아이템들을 의미하며, ```->```는 아이템 t의 어떠한 정보를 사용할 지를 나타낸다.
즉, ```t -> t.getType()```는 컬렉션 내의 모든 아이템들의 .getType()를 각각 가져온다.
<br>
- 메소드 참조의 경우 Transaction 클래스 내의 getValue()이라는 메소드를 실행하겠다는 의미이다.

<br>
<br>

### 동작과정을 그림으로 보면 아래와 같다.
- 간략도
<br>
![image](https://cloud.githubusercontent.com/assets/16336685/12953245/dfe81748-d05c-11e5-853a-6507fb1c335e.png)
<br>
- 상세도
<br>
![image](https://cloud.githubusercontent.com/assets/16336685/12953260/f4ff0402-d05c-11e5-9041-1cd8a9743d95.png)

### 정리
이와 같이 stream을 활용하면 복잡한 데이터를 간결하게 처리할 수 있으며, 속도향상도 기대할 수 있다. 또한 쿼리문처럼 표현 되므로써 코드의 가독성 또한 높일 수 있다.


#### 참고사이트
[자바 스트림 API blog](http://starplatina.tistory.com/entry/%EC%9E%90%EB%B0%94-%EC%8A%A4%ED%8A%B8%EB%A6%BC-API#recentTrackback)
<br>
[자바8 스트림 API 소개 slideshare](http://www.slideshare.net/madvirus/8-api)
<br>
JAVA 8 DOCS API [home](https://docs.oracle.com/javase/8/docs/api/) /  [stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)
