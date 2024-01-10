# Java Collectors.toMap()을 통한 List -> Map 변환

Java 개발 중 엔티티나 DTO의 키를 통해 List를 Map으로 변환하는 일이 자주 생긴다.  
그때, Java Collectors.toMap()을 통해 아래와 같이 간단하게 Map으로 변환할 수 있다.

```java
// ProductDto의 List를 생성
final List<ProductDto> productDtoList = getProductDtoList();

// ProductDto의 ID를 통해 Map을 생성 
// key: ProductDto.id, value: ProductDto
final Map<Long, ProductDto> productDtoMap = productDtoList.stream()
    .collect(Collectors.toMap(
            ProductDto::getId, // key (인스턴스 메서드 레퍼런스 사용)
            Function.identity() // value (입력받은 인스턴스 그대로 리턴)
        )
    );
```

혹은 아래처럼 람다식으로도 가능하다.  

```java
// ProductDto의 ID를 통해 Map을 생성 (람다식 사용)
final Map<Long, ProductDto> productDtoMap = productDtoList.stream()
    .collect(Collectors.toMap(
            productDto -> productDto.getId(), // key (람다식 사용)
            productDto -> productDto // (람다식 사용)
        )
    );
```

이때 주의할 점은 List안의 객체 중 key 중복이 발생할 경우 ```IllegalStateException```이 발생할 수 있다.  
따라서, 키 중복이 발생할 수 있는 경우 아래처럼 병합처리를 통해 예외 발생을 방지할 수 있다.

```java
// Key 중복이 발생할 수 있는 ProductDto의 List를 생성
final List<ProductDto> productDtoList = getDuplicatedProductDtoList();

// ProductDto의 ID를 통해 Map을 생성 
// 중복 발생 시 새 것으로 대치 (상황에 맞는 로직 구현할 것)
final Map<Long, ProductDto> productDtoMap = productDtoList.stream()
    .collect(Collectors.toMap(
            ProductDto::getId, // key
            Function.identity(), // value
            (oldOne, newOne) -> newOne // mergerFunction 사용, 대체 로직을 구현하면 된다 (람다식 사용)
        )
    );
```

앞으로는 쉽게 List에서 Map으로 변환하자!  

테스트 코드 참고: https://github.com/yoonpunk/java-example/blob/main/src/test/java/com/example/java/stream/CollectorsListToMap.java