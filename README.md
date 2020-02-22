# flyway 사용법

0. [flyway](#flyway)
1. [파일 경로](#파일-경로)
2. [이름 규칙](#이름-규칙)
3. [gradle 설정법](#gradle-설정법)
4. [명령 종류](#명령-종류)
5. [사용법](#사용법)
99. [팁](#팁)

## flyway

* flyway 는 `community`, `pro`, `Enterprise` 버전이 있음..
* [차이점](https://flywaydb.org/download/) 은 여기서..

## 파일 경로

* `resources/db/migration` 에 위치하게 된다. 

## 이름 규칙

* `[V][version]__[description].sql`
  * ex) `V0.0.1__init.sql`
* `[R]__[description].sql`
  * ex) `R__Create_view.sql`
  
> 버전 명에서는 `.` 이나 `_` 로도 구분 가능하다.

## gradle 설정법

* spring boot initializer 를 사용해서 flyway 를 추가하는 거는 자바단에서 실행하는 것 같다. 안해봐서 모름
* gradle 로 실행하는 방법을 설명할거라 아래 소스를 추가해준다. 

```groovy
plugins {
    id "org.flywaydb.flyway" version "6.2.4"
}

dependencies {
    runtimeOnly 'mysql:mysql-connector-java'
}

flyway {
    url = 'jdbc:mysql://localhost:3307/ha'
    user = 'root'
    password = 'qlalfqjsghekd'
    encoding = 'UTF-8'
}
```

* 위에 드라이버 추가 시킨 것은 flyway 를 실행할 때 gradle 에 작성된 드라이버로 접근하기 때문에 작성해줘야 한다. 자신에 맞는 드라이버 추가 필요

## 명령 종류

* Migrate
  * `resources/db/migration` 에 위치한 SQL 문을 실행해준다.
* Info
  * 현재 마이그레이션 정보를 출력한다. -> `Migrate` 명령을 어디까지 실행했는지
* Validate
  * database 에 적용된 마이그레이션 정보의 유효성을 검증한다. 
* Baseline
  * flyway 로 관리하기 이전에 database 존재시 해당 database 를 flyway baseline 으로 설정할 수 있다. 
  * 잘 모르겠음 실행해봐야 알 것 같음
* Repair
  * 실패한 마이그레이션 항목 제거 (DDL 트랜잭션 지원하지 않는 database만) -> 이전에 `Migrate` 성공적으로 실행한 버전으로 되돌리기?
* Clean
  * 모든 database 의 objects 를 제거한다. 
  
## 사용법

* gradle 에서 사용할 수 있는 명령어는 [링크](https://flywaydb.org/documentation/gradle/#system-properties) 여기서 볼 수 있다.
* `./gradlew flywayMigrate` 를 실행하면 sql 문이 실행되고 database 에 `flyway_schema_history` 이름을 가진 테이블이 생성된다 여기에 자신이 작성한 sql version 이 명시되어 있다. 성공 실패 여부까지도

## 팁

* 이건 왜 그러는지 자세히 안 찾아봐서 모르겠는데 `./gradlew flywayMigrate --debug` 를 했는데 나오는 에러 메시지는 명확하게 나오지 않을 경우 database 에 있는 모든 테이블을 제거한 후 다시 실행하도록 하자 이랬더니 됨.. 
* 이 글을 보면 `build.gradle` 에 db 접근 정보를 입력을 하는데 좀 보기 안 좋을 수 있다. 이럴땐 `flyway.conf` 파일로 따로 분리할 수 있다!!
* `flyway.conf` 파일 구조 [링크](https://flywaydb.org/documentation/configfiles)
* 그 대신 config 파일을 사용하면 명령어 실행시 명시적으로 해당 경로를 지정해줘야 한다. 
  * 실행할 떄는 `./gradlew -Dflyway.configFiles=src/main/resources/db/migration/flyway.conf flywayMigrate` 와 같이 실행해주면 된다. 

### 참조

* [gradle](https://flywaydb.org/documentation/gradle/)
* [flyway](https://flywaydb.org/documentation/gradle/migrate)
* [flyway 설명 블로](https://www.popit.kr/%EB%82%98%EB%A7%8C-%EB%AA%A8%EB%A5%B4%EA%B3%A0-%EC%9E%88%EB%8D%98-flyway-db-%EB%A7%88%EC%9D%B4%EA%B7%B8%EB%A0%88%EC%9D%B4%EC%85%98-tool/)