# 면접문제 09장 (홀수)

## 9.1 주식데이터:
- 개요
    - 서비스: 폐장 시점에 주가정보(시작가, 종가, 최고가, 최저가) 제공
    - 대상: 최대 1,000개의 클라이언트
- 조건
    - 데이터는 이미 주어짐
    - 데이터는 어떤 형태로든 저장 가능
- 책임
    - 개발과 배포
    - 지속적 시스템 모니터링
    - 사용자 전송 정보 관리
- 여러분이 생각하는 방법을 설명한 다음, 어떤 접근법을 택했는지 그방법은 선택한 이유는 무엇인지 설명하시오.

### 집중해야하는 측면
- 클라이언트 측의 사용 편의성
- 서버 측 편의성
- 나중에 생길 수 있는 요구를 수용하기위한 유연한 구조
- 규모 확장성과 효율성
### 방안 #1
- 데이터를 간단한 텍스트파일로 저장한 뒤 FTP 서버 제공
- 내용을 살펴보기 쉽고, 백업하기도 편함(유지보수성 높음)
- 쿼리를 날리려면 텍스트 파일을 파싱해야 하는 등 과적잉 복잡
- 새로운 데이터가 텍스트에 추가되면 기존 파싱방법이 동작하지 않을 수도 있음
### 방안 #2
표준적인 SQL 데이터베이스를 이용해서 클라이언트가 해당 데이터베이스를 직접 사용하도록 만들수 있음
#### 장점
- 클라이언트가 자유롭게 쿼리를 날리는것이 가능
- 표준적인 SQL 데이터베이스만 제공하면 되므로 구현이 매우 간단하다
- 표준적인 방식이므로 클라이언트가 서비스를 기존 애플리케이션에 통합하기 쉬워진다.
#### 단점
- 개발된 결과물이 필요이상으로 무거움
- 데이터를 조회하고 관리하도록 추가 구현이 필요함
- 보안 문제가 있음(수정권한, 비효율적인 쿼리로 부하)

### 방안 #3
값들을 JSON으로 제공
```
{
    "2018-10-12": [
        { 
            "company": "foo",
            "open": 126.23,
            "high": 130.27,
            "low": 122.83,
            "close": 127.30
        },
        ...
    ]
}
```
#### 장점
- 전송이 쉽고, 알아보기 쉽고 처리가 쉽다.
- JSON은 거의 표준에 가까우므로 대부분의 프로그래밍언어가 다루기 쉽다.
- 새로운 데이터를 삽입해도 클라이언트 파서가 깨지지 않는다
- 데이터가 JSON형태고 저장되기 때문에 기존의 백업 도구를 사용할 수 있다.

#### 단점
- 클라이언트에게 필요한게 자료중 일부라고 해도 모두 받을 수 밖에없다.
- 쿼리를 수행하려면 전체파일을 파싱해야한다.

## 9.3 웹 크롤러:
웹에 있는 데이터를 긁어 오는 크롤러(crawler)를 설계할 때, 무한루프(infinite loop)에 빠지는 일을 방지하려면 어떻게 해야 하는가?

### 해답
- 웹은 링크에 의해 만들어지는 그래프로 가정
    - 주소 cycle이 발생하면 무한루프
    - 이미 방문한 페이지의 주소 v를 hash[v]에 저장
    - 이 그래프를 너비우선으로 탐색한다고 가정
    - 페이지를 방문할때마다 큐에 넣되 hash에 있으면 무시한다
- URL의 인자가 달라졌을때 페이지가 달라질 가능성
    - 하지만 두개의 페이지가 서로 다른페이지인지 구분할 완벽한 방법은 없음
- 페이지의 유사성을 판별하여 가중치를 두는 방법론
    1. 페이지를 열어 해당 페이지의 특정한 섹션과 URL을 토대로 시그너처(서명)를 생성한다.
    2. 데이터베이스 쿼리를 통해 해당 시그너처의 페이지가 최근에 탐색된 적이 있는지 살핀다.
    3. 만일 해당 시그너처를 갖는 페이지가 퇴근에 탐색된 적이 있으면 해당 페이지의 우선순위를 낮춰서 데이터페이스에 추가한다.
    4. 그렇지 않다면 해당 페이지를 탐색하고, 그 페이지에 연결된 링크를 데이터베이스에 추가한다.

위와같이 구현하면 웹을 탐색하는 행위는 결코 끝나지 않겠지만 무한루프는 막는다.

탐색이 끝나도록 하고싶다면 탐색되기 위해 만족해야 하는 취소 우선순위를 설정해둔다.


## 9.5 캐시:
- 시스템
    - 간단한 검색엔진 웹 서버
    - 100개의 컴퓨터가 검색요청을 처리
    - processSearch(string query)라는 요청을 보내면 검색결과를 반환
- 조건
    - 어떤 컴퓨터가 요청을 처리하게 될지는 그때그때 다름
    - processSearch 메서드는 아주 고비용
- 최근 검색 요청을 캐시에 저장하는 메커니즘을 설계하라. 데이터가 바뀌었을 때 어떻게 캐시를 갱신할 것인지 반드시 설명하라.

### 가정
- 모든 쿼리는 최초로 호출된 서버에서 처리된다.
- 캐시하고자 하는 쿼리의 수는 굉장히 크다.(수백만개)
- 서버 간 호출은 상대적으로 빨리 처리된다.
- 쿼리의 결과는 정렬된 URL 리스트이다. 아래와같은 정보가 따라 붙는다.
    - 최대 50글자의 제목
    - 200 글자의 요약문
- 가장 인기있는 쿼리의 경우 항상 캐시에 보관되어 있다.,

### 시스템 요구사항
캐시는 다음의 두가지 주요 기능을 제공해야한다

- 주어진 키를 사용한 빠른 탐색
- 오래된 데이터를 버리고 새로운 데이터로 교체하는 기능

### 단계 #1: 단일 시스템에 대한 캐시 설계
오래된 데이터를 제거하고, 키로 빠르게 탐색하는 자료구조를 어떻게?

- 연결리스트: 최신데이터는 앞으로 옮기고, 리스트가 지정된 크기를 넘어가면 연결리스트의 마지막 항목을 제거하도록 구현 가능
- 해시테이블: 데이터의 효율적인 탐색이 가능하지만, 오래된 데이터를 제거하기 어렵다

이 두가지 자료구조의 장점을 합치면 된다. 연결리스트를 두어 오래된 데이터를 제거하고, 해시테이블을 사용하여 키값을 빠르게 탐색한다. 연결리스트에서 제거될때 해시테이블에서 동시에 제거하면 된다.

### 단계 #2: 여러서버로 확장
특정한 쿼리가 항상 같은서버로 전송되는것을 보장 못함

가장먼저 결정해야하는건 서버 간 캐시를 어떻게 공유할것인가

#### 방법 #1: 각 서버에 별도의 캐시를 둔다
방법
- 각각의 서버에서 따로 동작하는 별도의 캐시를 둔다

장단점
- 서버간 통신이 불필요하므로 상대적으로 빠르지만
- 같은 쿼리가 반복되더라도 새로운 쿼리로 인식될 확률이 높다

#### 방법 #2: 각 서버에 캐시 복사본을 둔다
방법
- 각 서버에 전체 캐시의 복사본을 유지하는 것이다.
- 새로운 데이터가 캐시에 추가되는 순간 그 데이터는 모든 서버로 보내진다.

단점
- 캐시를 갱신할때마다 N개의 서로 다른 서버로 전송하고 업데이트 하므로 부하가 큼
- 공간의 낭비가 있음

#### 방법 #3: 각 서버에 캐시의 일부를 저장한ㄷ
방법
- 캐시를 분할하여 일부만 저장하는 방법
- 캐시가 어디저장되어있는지 알아내고 그 값을 살펴봐야함
- 공식 hash(query) % N을 사용하여 쿼리를 배정하는 방법이 있음
    - 배정된 서버는 캐시가 있으면 캐시를 반환하고 아니면 쿼리를 직접 실행함
    - 아니면, 캐시가 없을경우 null을 반환하고 다시 쿼리를 실행하는방법으로 할수도 있음. 하지만 서버간 호출이 늘어나서 비효율적.

### 단계 #3: 내용이 변경되면 결과를 갱신
- 매우 빈번히 실행되는 쿼리의 경우, 결과값이 변경되면 값을 교체할 필요가 있음
- 주기적으로 쿼리의 내용을 갱신해야할 필요성이 있음

쿼리의 내용이 바뀌는 상황
1. URL이 가리키는 페이지 내용이 바뀔 때
2. 페이지의 랭킹이 바뀌어서 결과의 순서가 변경될 때
3. 특정한 쿼리에 관련있는 새로운 페이지가 등장할 때

상황 #1, #2
- 캐시된 쿼리가 어떤 URL에 대한것인지를 알려줄 별도의 해시테이블이 필요.
- 이과정은 다른 캐시들과는 다른 서버에 독립적으로 실행, 하지만 저장공간이 많이 필요
- 저장된 캐시를 주기적으로 탐색한뒤 갱신하는 방법이 있을 수 있음

상황 #3
- 위보다 까다롭다
- 질의의 where절이 하나의 조건만을 포함할경우 조건들의 경우의 수를 다 갱신하면 됨. 하지만 너무 제한적임.
- 가장 적합한 방법은, 캐시에 저장된 데이터를 시간이 지나면 자동으로 버리는 것. 이렇게하면 모든 데이터를 주기적으로 갱신할 수 있다.

### 단계 #4: 개선
아주 빈번한 쿼리가 존재한다는 사실을 이용
- 서버i가 해당 쿼리를 매번 서버j에게 전달하는 대신 해당 쿼리 자체를 서버j에 캐싱하는 방법이 있을수도 있음

무작위로 선택된 서버에 쿼리요청을 하는걸 개선
- 쿼리의 해시값(그리고 캐시의 위치)에 따라 서버를 적절히 선택하도록 개선

특정 데이터(뉴스 같은 것)은 더 자주 갱신되어야함
- '시간이 지나면 캐시에서 버리는' 방법을 이용하여 갱신


## 9.7 개인 재정 관리자:
개인 재정 관리 시스템을 어떻게 설계할지 설명하라. 이 시스템은 여러분의 은행 계정과 연동되어있어야 하며 소비 습관을 분석하고 그에 맞게 적잘하게 추천할 수 있어야 한다.

### 1단계: 문제의 범위 한정하기
- 계정을 만들고 은행 계좌를 추가한다. 여러은행 계좌 가능. 나중에 추가 가능.
- 은행이 허락하는 모든 금융기록을 가져올 수 있다
- 금융기록은 출금, 입금, 잔액이다.
- 각각의 금융거래는 카테고리를 가지고 있다(의류, 음식 등)
- 카테고리는 필요시 사용자가 수정할수있다.
- 현재는 웹사이트지만 나중에는 모바일도 고려할 수 있다
- 이메일 알림을 받을 수 있다.(지출액 설정 초과 알림 등)
- 목록과 거래를 연결짓는 사용자 특유의 규칙같은건 없다.

### 2단계: 합리적인 가정을 하자
- 은행 계좌를 추가하거나 삭제하는건 흔치 않다.
- 주로 금융기록 쓰기 연산이 많다. 사용자는 일주일에 한번정도 웹을 확인한다.
- 이미 배치된 거래는 룰이 바뀌어도 다시 재배치 되지 않는다.
- 우리 시스템이 직접 은행에 데이터를 요청해야 한다.
- 예산을 넘어선 경고 메일은 즉시 보내지 않아도 된다.(24시간 유예)

### 3단계: 주요 구성요소 그리기
- 단순하게 생각하면, 사용자가 접속하면 서버에 요청을 보내 은행에서 데이터를 받아와서 처리할수 있지만. 메일을 보내야 하므로 부적절함
- 은행데이터를 주기적으로 받아오게해야함(매일 혹은 매시간)
```
은행 데이터 동기화
        ↓
원시 거래 데이터    →   분류기
                        ↓
프론트엔드 ←→ 목록별로 분류된 거래
    ↑↓               ↓
예산 데이터 ←   예산 분석기
```

### 4단계: 핵심 문제 파악하기
주요문제점: 데이터를 굉장히 많이 사용함. 좀 더 가볍고 즉각적으로 반응하고, 비동기적인 시스템을 원함

이메일 시스템:
- 주기적으로 데이터로 메일을 보낸다면 사용자가 직접 분석을 해야함
- 사용자가 설정한 예산을 넘었을경우에만 큐에 넣어서 알려주도록 함

사용자 비활성화:
- 유령회원의경우 짧은 텀으로 배치작업을 돌릴 필요가 없다.
- 이들을 완전히 지우거나, 활성화된 패턴을 보고 우선순위를 부여할 수 있다.

병목점:
- 이 시스템의 가장 큰 병목점은 거대한 데이터를 읽어온뒤 분석하는 부분
- 은행 데이터를 비동기식으로 여러서버를 통해 가져올 수 있으면 좋다

분류기(categorizer)와 예산 분석기(budget analyzer)
- 각각의 거래에 의존성은 없다
- 표준 데이터베이스를 사용해야하나?
    - 많은 거래가 한번에 들어오는 상황에 적합하지 않을 수 있다
    - 다수의 join 연산을 하는 걸 원치 않음
- 거래내역을 평범한 텍스트파일로 저장하는것이 나을 수 있다
- 사용자가 많을경우, 목록별 분류는 판매자의 이름으로 하는것이 효율적일 수 있다
```
원시거래데이터(판매자별) → 사용자별로 묶기 → 분류된 거래내역 갱신
                               ↓
                    병합 & 사용자별로 묶기 & 분류
                               ↓
                            예산갱신
```
1. 원시 거래 데이터를 받은 뒤 판매자별로 묶는다
2. 판매자에 적합한 목록을 선택(흔한 판매자 캐시) 해당 목록을 모든 거래에 적용
3. 목록을 적용한 후 사용자별로 거래를 다시 묶는다
4. 거래내역은 해당 사용자의 저장소에 입력된다
5. 사용자의 예산이 카테고리별로 분류되어 갱신한다
6. 마지막 데이터(분류된 거래 내역과 예산 분석 자료)만이 데이터베이스에 저장된다

#### 사용자가 목록을 변경한 경우
- 사용자가 해당 건을 다른 카테고리로 재배치 할 수도 있음
- 이런경우 해당 거래내역이 들어있는 저장소를 갱신해야함
- 또한 카테고리별로 예산을 더하고 빼는 작업을 해야함
- 아니면 단순하게 처음부터 재계산을 하면 됨 충분히 빠름

### 연관된 문제들
- 모바일 앱을 사용하려면 설계의 어떤부분을 바꿔야하나?
- 항목을 개별 목록으로 배치하는 부분을 어떻게 설계?
- 적정 예산을 추천하는 부분은 어떻게 설계?
- 사용자가 특정 판매자에 대해 거래 내역을 분류하는 규칙을 만들 수 이게 하려면 위의 설계를 어떻게 바꿔야 할까?