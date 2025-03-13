### Java의 Collections.unmodifiableList는 불변이 아님

set/add/remove 같은 수정 method는 지원안해 불변같지만, 내부적으로 this.list=list로 원본 list 참조값 갖고 있어, 원본 list가 변하면 따라서 변하게됨

불변 List 생성 방법 4가지
1. List.of()
2. List.copyOf()
3. Stream.collect(Collectors.toUnmodifiableList())
4. Stream.toList() (JDK16이상)
