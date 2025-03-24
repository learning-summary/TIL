### [Mybatis] statementType="CALLABLE" 이란?

[https://overcode.tistory.com/entry/Mybatis의-statementTypeCALLABLE-은-무엇입니까](https://overcode.tistory.com/entry/Mybatis%EC%9D%98-statementTypeCALLABLE-%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9E%85%EB%8B%88%EA%B9%8C)

- 프로시저에서 out으로 지정한 값들이 해당 쿼리요청 파라미터 map에 담김. 즉 procedure의 결과값을 요청파라미터에 담아옴

```java
<select id="query" statementType="CALLABLE" parameterType="dto">
	{
    	CALL PROCEDURE_NAME(
            #{field1, mode=IN, jdbcType=VARCHAR},
            #{field2, mode=IN, jdbcType=VARCHAR},
            #{field3, mode=IN, jdbcType=VARCHAR},
            #{result1, mode=OUT, jdbcType=VARCHAR},
            #{result2, mode=OUT, jdbcType=VARCHAR})
}
</select>
```
