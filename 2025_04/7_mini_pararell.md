# `parallelStream`

> Java 8에서 도입된 Stream API 기능 중 하나로, 데이터를 병렬로 처리할 수 있게 해준다.
> 

Spring에서 직접 제공하는 기능은 아니지만, Spring 기반 애플리케이션에서도 자주 활용되므로 Spring과 함께 사용되는 개념으로 많이 다루어진다.

## 장점

1. 코드의 간결함
2. CPU자원 활용성 좋음
→ CPU 코어에 따라 자동으로 스레드 분산
3. 빠른 병렬 처리
→ 대량의 데이터를 처리할 때 좋음

## 동작 방식

- Collection에서 parellelStream()을 호출하여 사용할 수 있다.
- 내부적으로 Java의 Fork/Join Framework를 사용한다.
- 데이터를 자동으로 나누어 여러 스레드에서 병렬로 처리하고 그 결과를 조합한다.

## 주의사항

- 병렬처리가 항상 빠른 것은 아니다, 작은 데이터셋에서는 오히려 오버헤드로 느려질 수 있다.
- 공유 자원 접근 시 주의해야한다. (동기화 필요)
- 순서가 보장되지 않는다.

## 예시

```java
 // 미소지기만 필터링 하기
    private List<MisojigiResponse> getMisojigiListByEmployeeTypeCode(
            String siteNo,
            String empTypeCode,
            String searchItemType,
            String searchItemValue
    ) {
        // 미소지기 정보 리스트 조회 COS API 호출
        List<ClientMisojigiEmpInfoResponse> clientMisojigiEmpInfoResponseList = callMisojigiList(siteNo, "N", searchItemType, searchItemValue).getList();
        return clientMisojigiEmpInfoResponseList.parallelStream()
                .filter((misoInfo -> misoInfo.getEmployeeTypeCode().equals(empTypeCode))) // Employee Type이 S, 미소지기만 필터링 하여 조회
                .map(this::makeMisoInfoObject)
                .toList();
    }
```

# Compeatable Future

> Java8에서 도입된 비동기/병렬 처리를 위한 API
> 

명시적으로 작업을 비동기로 수행하고, 완료 후 작업을 이어서 처리하는 방식이다.

## 예시

```java
CompletableFuture.supplyAsync(() -> {
    // 비동기 작업
    return "result";
}).thenApply(result -> {
    // 결과 후속 처리
    return result.toUpperCase();
});

```

```java
 // 미소지기만 필터링 하기
    private List<MisojigiResponse> getMisojigiListByEmployeeTypeCode(
            String siteNo,
            String empTypeCode,
            String searchItemType,
            String searchItemValue ) {
        // 미소지기 정보 리스트 조회 COS API 호출
        List<ClientMisojigiEmpInfoResponse> clientMisojigiEmpInfoResponseList = callMisojigiList(siteNo, "N", searchItemType, searchItemValue).getList();

        // 병렬 실행할 Executor 설정
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(20);
        executor.initialize();

        MosUserModel userContext = MosUserContextHolder.getUserContext();

        List<CompletableFuture<MisojigiResponse>> futures = clientMisojigiEmpInfoResponseList.stream()
                .filter(misoInfo -> misoInfo.getEmployeeTypeCode().equals(empTypeCode)) // Employee Type이 S, 미소지기만 필터링 하여 조회
                .map(misoInfo -> CompletableFuture.supplyAsync(() ->
                        {
                            MosUserContextHolder.setUserContext(userContext);
                            return makeMisoInfoObject(misoInfo);
                        }, executor
                ))
                .toList();

        return futures.stream().map(CompletableFuture::join).toList();
    }
```