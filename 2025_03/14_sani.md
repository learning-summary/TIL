프로젝트 표준 준수 활동으로 SonarQube Issue들을 처리하며 만나게된 여러 issue들을 정리해보았습니다.
</br>


|issue|사유|해결|ruleset id|
|---|-------|----|---|
|Ternary operators should not be nested |__가독성 저하__ </br> 삼항 연산자를 중첩해 사용하면 가독성 떨어짐. JSX에서는 conditional rendering을 위해 삼항 연산자 사용이 권장되지만,  마찬가지로 중첩될 경우 가독성 떨어짐|별도의 함수로 분리 |typescript:S3358|
|This assertion is unnecessary since it does not change the type of the expression.|__불필요__</br> if문으로 string type임을 확인 했는데 string으로 타입 캐스팅|불필요한 타입 캐스팅 삭제|typescript:S4325|
|Specify a target origin for this message.|__보안 취약__</br> window를 open한 opener에게 callback으로 데이터를 넘기려 poseMessage Web API를 호출했는데, targetOrigin으로 '*'을 명시함. 해커의 malicious request를 통해 다른 origin에 data 전달 가능성 존재|opener의 origin으로 명시|typescript:S2819|

