## 9. 시스템 설계 및 규모 확장성(짝수)

#### 9.2 소셜 네트워크 
페이스북이나 링크드인과 같은 대규모 소셜 네트워크를 위한 자료구조는 어떻게 설계하겠는가? 두 사람 사이의 최단 경로를 보여 주는 알고리즘은 어떻게 설계하겠는가?(가령, 나-> 밥-> 수잔-> 제이슨-> 당신)


풀이 :

#### 단계 1 문제를 단순화하라 - 수백만의 사용자를 잊으라



- 각 사용자 -> 노드
- 친구 관계 -> 간선

노드와 간선으로 하나의 그래프를 만들 수 있다.

두 사용자간의 경로를 알고 싶으면, 한 사용자에게서 시작해서 너비 우선 탐색을 돌려보면 된다.

- 왜 너비 우선 탐색인가?

너비 우선 탐색은 **두 노드 사이의 최단 경로** 혹은 **임의의 경로를 찾고 싶을 때** 사용할 수 있는 방법.

반면 깊이 우선 탐색은 **모든 노드를 방문** 하기에 좋은 방법이므로 너비 우선 탐색을 사용한다.


혹은 양방햔 너비 우선 탐색을 사용 할 수 있다. 하나는 출발지에서 나머지 하나는 도착지에서 시작해서 너비 우선 탐색 두 개를 동시에 돌리는것을 말함. 두 탐색이 어느 지점에서 충돌하는 순간 경로를 찾는것.


#### 구현

- BFSData : 해시테이블(isVisited) 과 큐(toVisit)와 같이 너비 우선 탐색에 필요한 자료를 저장하는 클래스

- PathNode : 탐색하는 경로를 표현하는 클래스. 방문했던 경로를 지정하는 `previousNode`와 `Person`을 저장


```java
LinkedList<Person> findPathBiBFS(HashMap<Integer, Person> people, int source, int destination){
    BSFData sourceData = new BFSData(people.get(source));
    BFSData destData = new BFSData(people.get(destination));

    while(!sourceData.isFinished() && !destData.isFinished()){
        /* 출발점에서 탐색 시작 */
        Person collision = searchLevel(people, sourceData, destData);
        if(collision != null){
            return mergePaths(sourceData, destData, collision.getID());
        }

        /* 도착지에서 탐색 시작*/
        collision = searchLevel(people, destData, sourceData);
        if(collision != null){
            return mergePaths(sourceData, destData, collision.getID());
        }
    }
    return null;
}

/* 한 담계 탐색을 마친 뒤 충돌한 지점을 반환 */
Person searchLevel(HashMap<Integer, Person> people, BFSData primary, BFSData secondary){
    /*한번에 한 단계씩만 탐색할것. 현재 단계에 얼마나 많은 노드가
    * 존재하는지 세어보고 그 만큼의 노드에 대해서만 탐색을 수행
    * 그리고 큐에 계속해서 노드를 추가 */

    int count = primary.toVisit.size();
    for(int i = 0; i < count; i++){
        //첫 번째 노드를 빼낸다.
        PathNode pathNode = primary.toVisit.poll();
        int personId = pathNode.getPerson().getID();

        //이미 방문한 적이 있는지 확인한다.
        if(secondary.visited.containsKey(personId)){
            return pathNode.getPerson();
        }

        //인접한 노드들을 큐에 추가한다.
        Person person = pathNode.getPerson();
        ArrayList<Integer> friends = person.getFriends();
        for(int friendId : friends){
            if(!primary.visited.containsKey(friendId)){
                Person friend = people.get(friendId);
                PathNode next = new PathNode(friend, pathNode);
                primary.visited.put(friendId, next);
                primary.toVisit.add(next);
            }
        }
    }
    return null;

}

//두 탐색지점이 만난 경로를 병합한다.
LinkedList<Person> mergePaths(BFSData bfs1, BFSData bfs2, int connection){
    PathNode end1 = bfs1.visited.get(connection); //end1 -> 출발지점
    PathNode end2 = bfs2.visited.get(connection); //end2 -> 도착지점
    LinkedList<Person> pathOne = end1.collapse(false);
    LinkedLsit<Person> pathTwo = end2.collpase(true);//순서를 뒤집는다.
    pathTwo.removeFirst();
    pathOne.addAll(psthTwo);
    return pathOne;
}

class PathNode {
    private Person person = null;
    private PathNode previousNode = null;
    public PathNode(Person p, PathNode previous){
        person = p;
        previous = previous;
    }

    public Person getPerson() {return person;}

    public LinkedList<Person> collapse(boolean startsWithRoot){
        LinkedList<Person> path = new LinkedList<Person>();
        PathNode node = this;

        while(node != null){
            if(startsWithRoot){
                path.addLast(node.person);
            } else {
                path.addFirst(node.person);
            }
            return path;
        }
    }
}

class BFSData {
    public Queue<PathNode> toVisit = new LinedList<PathNode>();
    public HashMap<Integer, PathNode> visited = new HashMap<Integer, Path>();

    public BFSData(Person root){
        PathNode sourcePath = new PathNode(root, null);
        toVisit.add(sourcePath);
        visited.put(root.getID(), sourcePath);
    }

    public boolean isFinished() {
        return toVisit.isEmpty();
    }
}


```


- 수학적인 원리

모든 사용자에게 k명의 친구가 있고, 노드 S와 노드 D가 친구 C를 공유한다고 가정.

- 일반적인 너비 우선 탐색으로 S -> D로 갈때 : 약 k + k * k 개의 노드를 거쳐야햠. S의 친구 k명과 각 k명의 친구들 k 명.

- 양방향 너비 우선 탐색 : 2k 노드만 거치면 됨. S의 친구 k명과 D의 친구 k명. 2k가 k + k * k 보다 훨씬 작음.

경로의 길이 q에 대해 일반화시.

너비 우선 탐색 : O(K^q).
양방향 너비 우선 탐색:O(K^(q/2) + k^(q/2)), 즉 O(k^(q/2)).
 

각 노드에게 100명의 친구가 있을 때, A->B->C->D->E 와 같은 경로를 예로 들때,

- 너비우선 탐색시 1억개(100^4)의 노드를 탐색해야함
- 양방향 너비 우선탐색시 2만개(2 * 100^2)의 노드를 살펴봐야함.

일반적으로 양방향 너비 우선 탐색이 빠르지만, 시작 지점과 도착 지점에 모두 접근 가능할 때에나 사용 가능함. 따라서 항상 사용할 수 있는 건 아님을 명심.



#### 단계 2 수백만 사용자의 처리

링크드인이나 페이스북 규모의 서비스에서는 컴퓨터 하나만으로는 부족함. 따라서 위에서 설계한 단순한`Person`은 서버환경에서는 제대로 동작하지 않을것.

찾고자 하는 '친구'는 같은 서버에 있지 않을 수 도 있음. 따라서 ID로 구성되는 친구 리스트를 만들고, 다음과 같이 탐색해야함.


1. 친구의 Id : int machine_index = getMachineIDForUser(personID);
2. #machine_index 컴퓨터로 간다.
3. 해당 컴퓨터에서 Person friend = getPersonWithID(personID);


```java
class Server {
    HashMap<Integer, Machine> machines = new HashMap<Integer, Machine>();
    HashMap<Integer, Integer> personToMachineMap = new HashMap<Integer, Integer>();

    public Machine getMachineWithId(int machineID){
        return machines.get(machineID);
    }

    public int getMachineIDForUser(int personID){
        Integer machineID = personToMachineMap.get(personID);
        return machine == null ? -1 : machineID;
    }

    public Person getPersonWithID(int personID){
        Integer machineID = personToMachineMap.get(personID);
        if(machineID == null) {return null;}

        Machine machine = getMachineWithId(machineID);
        if(machine == null) { return null};

        return machine.getPersonWithID(personID);

    }
}


class Person {
    private ArrayList<Integer> friends = new ArrayList<Integer>();
    private int personID;
    private String info;

    public Person(int id) {this.personID = id;}
    public String getInfo() {return info;}
    public void setInfo(String info){this.info = info;}
    public ArrayList<Integer> getFriends() {return friends;}
    public int getID() {return personID;}
    public void addFriend(int id) {friends.add(id);}
}


```







#### 9.4 중복 URL 
100개의 URL이 있다. 중복된 문서를 찾으려면 어떻게 해야 하는가? 여기서 '중복'이란 같은 URL 이라는 뜻이다.


- 각각의 URL이 평균적으로 100개의 문자로 구성.
- 각 문자는 4Byte라고 가정.
- 100억개의 URL -> 4TB의 메모리가 필요함.
- 이만한 자료는 메모리에 보관 불가.


-> 단순하게 생각. 만약 메모리에 다 올릴수 있다고 가정한다면..

- 이미 살펴본 URL에 대해 true를 반환하는 해시테이블을 사용.

-> 실제로 메모리에 다 올릴 수 없는 상황을 생각

- 데이터 일부를 디스크에 저장
- 데이터를 여러 서버에 분할 저장.


**해법 1. 디스크 저장**

- 모든 데이터를 한 서버에 저장
- 이때 문서 목록은 두번 처리되어야함.
- 첫 단계에서 URL을 1GB 크기 4,000개로 분할.
- 각 URL을 .txt 파일에 저장하는 방법.
- x = hash(u) % 4000과 같이 결정하면됨.
- URL을 그 해시값(% 파일 개수)에 따라 분할.
- 각 파일을 메모리에 올려 URL의 해시테이블 생성
- 중복 검사

**해법 2. 디스크 저장**
- URL을 .txt라는 파일에 저장하는 대신 서버 x에 전송
- 연산을 병렬로 처리할 수 있음.
- 단, 4,000개의 서버가 모두 완벽하게 동작해야함.
- 이는 비현실적임


> 면접관과 두 가지 방법 모두 토의할것.






#### 9.6 판매 순위
한 전자상거래 회사는 가장 잘 팔리는 제품의 리스트(전체에서 그리고 각 목록별로)를 알고 싶어한다. 예를 들어, 어떤 제품은 전체 제품중에서 1,056번째로 잘 팔리지만 운동 장비 중에서는 13번째로 잘 팔리고, 안전용품 중에서는 24번째로 잘 팔릴 수 있다. 이 시스템을 어떻게 설계할지 설명하라.



### 1단계 : 문제의 범위 한정하기

- 잘 팔린다는것은 어떤 의미인가?
    - 평생에 걸쳐 팔린총량?
    - 지난달?
    - 지난주?
    - 지수함수형 붕괴방식

- 이 부분은 면접관과 토의해 볼 만한 내용임.

> 이 문제에서는 단순하게 지난주 전체 판매량이라고 가정.


### 2단계 : 합리적인 가정을 하자

- 통계 결과가 언제나 100% 최신 데이터가 아닐 수 있다고 가정.
- 인기있는 제품은 1시간 전의 데이터 일수도 있음.
- 반면 인기가 없는 제품은 하루 전의 데이터 일 수도있음.
- 인기있는 제품은 정확도가 중요함.
- 반면 인기없는 제품은 약간의 오차가 허용됨.
- 가장 인기있는 제품은 한시간마다 갱신된다고 가정
- 데이터의 시간범위가 정확할 필요는 없음.
- 목록별 분류는 매매를 하는 주체(판매자 이름)을 기준으로 함.
- 가격이나 날짜는 기준이 아님.


> 초반에는 이러한 가정을 많이하는것이 좋음.


### 3단계 : 주요 구성요소 그리기

- 주요 부분을 설명하는 간단하면서도 기본적인 시스템을 설계해야함.
- 화이트 보드에 그려보자.




### 4단계 : 핵심 문제 파악하기

- 분석은 비용이 비싸다
    - 일정 규모 이상 시스템에서 쿼리는 비용이 많이 드는 작업.
    - DB는 단순히 전체 판매량만 알고 있으면 됨.
    - DB에 모든 구매목록을 나열하지 않음
    - 지난주의 전체 판매에 관한 부분만 저장
    - 물건을 구매하면 지난주 전체 판매 정보를 갱신.

- 전체 판매를 기록하는 부분은 고민이 필요함.
    - 열 하나를 사용하여 지난주 판매량을 기록할 시 매일 전체 판매량의 갱신 필요.
    - 이를 개선한 구조가 필요함
    - 따라서 아래의 테이블 사용


| 제품ID 	| 총량 	| 일요일 	| 월요일 	| 화요일 	| 수요일 	| 목요일 	| 금요일 	| 토요일 	|
|--------	|------	|--------	|--------	|--------	|--------	|--------	|--------	|--------	|
|        	|      	|        	|        	|        	|        	|        	|        	|        	|
|        	|      	|        	|        	|        	|        	|        	|        	|        	|
|        	|      	|        	|        	|        	|        	|        	|        	|        	|
|        	|      	|        	|        	|        	|        	|        	|        	|        	|
|        	|      	|        	|        	|        	|        	|        	|        	|        	|
|        	|      	|        	|        	|        	|        	|        	|        	|        	|
|        	|      	|        	|        	|        	|        	|        	|        	|        	|


- 환형 배열과 동일한 구조
- 매일 해당 날짜의 데이터를 갱신함.
- 이와 별개로 제품 ID와 해당 제품 목록을 저장하고 있는 테이블이 필요함.

| 제품ID 	| 목록 ID 	|
|--------	|---------	|
|        	|         	|

- 목록별로 판매 순위를 얻고 싶으면 두 테이블을 Join

> 여전히 DB에 너무 자주 기록함.


- DB의 접근을 줄이기 위해 메모리 내의 캐시(in-memory cache)를 활용.
- 이때 로그의 순서를 고려하지 않으면 제품간 판매 기간에 큰 차이가 발생함.
- 이 문제점을 해결하기 위해 특정 시점까지의 자료만 DB에 넣는다면 편향되지 않는다.



> join은 비용이 비싸다.

- 각 목록마다 제품들의 정보를 읽고 정렬하는데 Join 연산이 필요함.
- DB를 사용하지 않고 제품 구매시 발생하는 로그 파일만 활용하는 방법도 존재.
- 구매정보를 제품 ID & 타임 스탬프와 같은 구매정보를 간단한 텍스트 파일에 쓰면됨.
- 제품 ID와 시간 범위를 이용하여 주기적으로 프로그램 수행.

> 로그 기반 방법

    /스포츠 장비
    1423,Dec 13 08:23-Dec 13 08:23,1
    4221,Dec 13 15:22-Dec 15 15:45,5

    /안전 장비
    1423,Dec 13 0:23-Dec 13 08:23,1
    5221,Dec 12 03:19-Dec 12 03:28,19



- 각 목록에서 가장 잘 팔리는 제품을 구하기위한 방법
    - 일반적인 목록을 위한 새로운 디렉터리 생성
    - 이 안에 모든 구매정보 기록
    - 매우 많은 파일이 생성될것임.
    - 혹은 N-way 합병도 고려할 수 있음.

> 다른 방법

- 자료가 항상 최신일 필요가 없다는 가정에 기반
    - 가장 인기있는 제품들의 자료만 최신으로 유지
    - 각 목록별로 가장 인기있는 제품들을 쌍별(pairwise)로 합친다.
    - 두 개의 목록을 하나의 쌍으로 만들어 가장 인기있는 제품 100개를 하나로 합침.
    - 다음 목록쌍에도 반복함
    - 모든 제품에 대한 순위는 하루에 한번 정도만 병합
    - 확장이 용이한 방법이며 서로 의존관계가 없어 파일을 병렬처리가 가능함





#### 9.8 Pastebin
Pastebin과 같은 시스템을 설계하라. Pastebin은 텍스트를 입력하면 접속 가능한 임의의 URL을 생성한 뒤 반환해 주는 시스템이다.


