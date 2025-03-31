[[WEB] Graceful Shutdown](http://velog.io/@mini_mouse_/WEB-Graceful-Shutdown)

# Graceful Shutdown
> Graceful Shutdown 이란, 우아한 종료로 애플리케이션이 종료될 때 즉시 종료되는 것이 아니라, 현재 진행하고 있는 작업을 마무리 하고 리소스를 정리한 이후 종료하는 방식을 의마한다.

서버 애플리케이션에서 일반적인 Graceful Shutdown은 `SIGTERM` 신호를 받았을 때, 
새로운 요청은 차단하고 기존에 처리 중이었던 요청들은 모두 완료한 뒤에 프로세스를 종료한다.

만약, 서버 애플리케이션이 요청을 처리하는 중에 즉시 종료된다면, 트랜잭션 비정상 종료, 데이터 손실, 사용자 경험 저하 등의 문제가 발생할 수 있다.

# SIGTERM과 SIGKILL의 차이점
> SIGTERM과 SIGKILL은 유닉스 및 리눅스 운영체제에서 사용되는 프로세스 종료 시그널이다.

## SIGKILL
> SIGKILL은 프로세스를 강제로 즉시 종료하게 하는 신호이다.

## SIGTERM
> SIGTERM은 프로세스가 해당 시그널을 핸들링할 수 있다. 따라서 프로세스가 종료하기 이전에 수행되어야 하는 절차들을 안전하게 수행할 수 있다.

# Spring 환경에서 Graceful Shutdown을 하는 방법?
```YML
server.shutdown=graceful
spring.lifecycle.timeout-per-shutdown-phase=20s // 타임 아웃
```

스프링에서는 Graceful Shutdown 설정을 지원한다.
단, 기존 처리 중인 요청 중에 데드락이나 무한 루프가 발생하면 프로세스가 종료되지 않을 수 있기 때문에 스프링에서는 이를 예방하기 위해 타임 아웃 설정을 지원한다.