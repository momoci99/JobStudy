# 면접문제 07장 객체 지향 설계 (홀수) jungeol

## 7.1: 카드 한벌
카드 게임에 쓰이는 카드 한 벌을 나타내는 자료구조를 설계하라. 그리고 블랙잭 게임을 구현하려면 이 자료구조의 하위 클래스를 어떻게 만들어야 하는지 설명하라

블랙잭 룰:
- 플레이어들은 카드를 2장씩 받고 점수합이 21에 가장 가까운 사람이 승리함
- 플레이어는 카드를 원하는 만큼 계속 받을 수 있음
- 점수 합이 21이 넘으면 리타이어
- 처음 2장이 10점짜리와 에이스이면 바로 승리
- 인원은 2~8명
- 카드는 조커를 뺀 52장
- 에이스는 1 or 11점
- J, Q, K는 10점
```javascript
/* 카드 한벌 자료구조 */
class Card {
    constructor(suit, num) {
        this.suit = suit;
        this.num = num;
        this.available = true
    }
    getValue() { }
    setAvailable() { }
    getAvailable() { }
}

class Deck {
    constructor() {
        this.cards = [];
        this.index = 0;
    }
    initCards() { }
    shuffle() { }
    hasNext() { }
    getCard() { }
}

class Hand {
    constructor(peopleNum) {
        this.cards = [];
    }
    score() { }
    addCard(card) {
        cards.push(card);
    }
}

/* 블랙잭 게임 */
class BlackJackHand extends Hand {
    // 21이 넘지않는 가장 큰수나, 21이 넘은 가장 작은수를 반환
    score() { }
    // 에이스를 1로 사용할 수도있고 11로도 사용할수있는 경우의수 반환
    possibleScores() {}

    busted() {
        return score() > 21;
    }
    is21() {
        return score() == 21;
    }
    isBlackJack() { }
}

class BlackJacCard extends Card {
    constructor(suit, num) {
        super(suit, num);
    }
    value() {
        if (isAce()) return 1;
        else if (isFaceCard()) return 10;
        else return num;
    }
    minValue() {
        if(isAce()) return 1;
        else return value();
    }
    maxValue() {
        if(isAce()) return 11;
        else return value();
    }
    isAce() {
        return num === 1;
    }
    isFaceCard() {
        return num >= 11 && num <= 13;
    }
}
```

## 7.3: 주크박스
객체 지향 원칙에 따라 음약용 주크박스(musical jukebox)를 설계하라

컴포넌트
- JuckBox
- CD
- Song
- Artist
- Playlist
- Display

액션
- Playlist 생성(추가, 삭제, shuffle)
- CD 선택
- Song 선택
- 큐에 Song을 삽입
- Playlist에서 다음 Sing을 선택

사용자
- 사용자 추가
- 사용자 삭제
- 신용 정보
```typescript
class Jukebox {
    private cdPlayer: CDPlayer;
    private user: User;
    private cdCollection: Set<CD>;
    private playlist: Playlist;

    constructor(cdPlayer: CDPlayer, user: User, cdCollection: Set<CD>, playlist: Playlist) {
        // ...
    }
    public setUser(u: User): void {
        this.user = u;
    }
    public getCurrentSong(): Song {
        return this.playlist.getCurrentSong();
    }
}

class CDPlayer {
    private p: Playlist;
    private c: CD;

    /* 생성자 */
    constructor(p?: Playlist, c?: CD) { 
        this.p = p || null;
        this.c = c || null;
    }

    /* 노래재생 */
    public playSong(): void { }

    /* getter/setter */
    public getPlaylist(): Playlist {
        return this.p;
    }
    public setPlaylist(p: Playlist): void { }
    public getCd(): CD {
        return this.c;
    }
    public setCd(c: CD): void { }
}

class Playlist {
    private song: Song;
    private queue: Array<Song>;
    
    constructor() { }

    public getCurrentSong(): Song {
        return this.song;
    }

    public getNextToPlay(): Song {
        return this.queue[0];
    }

    public queueUpSong(s: Song): void {
        this.queue.push(s);
    }
}

class CD { /* id, artist, song 등의 정보를 보관 */ }

class Song { /* id, CD, title, length 등의 정보를 보관 */ }

class User {
    private name: String;
    private id: number;
    /* getter setter 등등 */
}
```

## 7.5: 온라인 북 리더
온라인 북 리더(online book reader) 시스템에 대한 자료구조를 설계하라.

다음과 같은 기능을 제공한다고 가정함
- 사용자 가입 정보 생성 및 확장
- 서적 데이터베이스 검색
- 책 읽기
- 한 번에 한 명의 사용자만 사용 가능함
- 해당 사용자는 한 번에 한 권의 책만을 읽을 수 있음

```typescript
/* 서비스 몸체에 해당 */
class OnlineReaderSystem {
    private library: Library;
    private userManager: UserManager;
    private display: Display;

    private activeBook: Book;
    private activUser: User;

    constructor() {
        this.library = new Library();
        this.userManager = new UserManager();
        this.display = new Display();
    }

    public getLibrary(): Library { return this.library; }
    public getUserManager(): UserManager { return this.userManager; }
    public getDisplay(): Display { return this.display; }
    
    public getActiveBook(): Book { return this.activeBook; }
    public setActiveBook(book: Book): void { 
        this.activeBook = book;
        this.display.displayBook(book);
    }
    public getActiveUser(): User { return this.activUser; }
    public setActiveUser(user: User): void {
        this.activUser = user;
        this.display.displayUser(user);
    }
}

class Library {
    private books: Map<number, Book>;
    constructor() {
        this.books = new Map();
    }
    public addBook(id: number, details: string): Book {
        if (this.books.has(id)) {
            return null;
        }
        let book = new Book(id, details);
        this.books.set(id, book);
        return book;
    }
    public remove(b: Book);
    public remove(n: number);
    public remove(arg: any): boolean {
        const id = arg instanceof Book ? arg.getId() : arg;
        if (!this.books.has(id)) {
            return false;
        }
        this.books.delete(id);
        return true;
    }

    public find(id: number): Book {
        return this.books.get(id);
    }
}

class UserManager {
    private users: Map<number, User>;

    constructor() {
        this.users = new Map();
    }
    public addUser(id: number, details: string, accountType: number): User {
        if (this.users.has(id)) {
            return null;
        }
        const user = new User(id, details, accountType);
    }
    public find(id: number): User { return null; }
    public remove(id: number): boolean { return true; }
}

class Display {
    private activeBook: Book;
    private activeUser: User;
    private pageNumber: number;

    displayBook(book: Book): void { /* display book */ }
    displayUser(user: User): void { /* display user */ }

    pageChange() { }

    refreshUserName() {}
    refreshTitle() {}
    refreshDetails() {}
    refreshPage() {}
}

class Book {
    private id: number;
    private details: string;

    constructor(id: number, details: string) {
        this.id = id;
        this.details = details;
    }

    public getId(): number {
        return this.id;
    }
    public getDetails(): string {
        return this.details;
    }
}

class User {
    private userId: number;
    private details: string;
    private accountType: number;

    constructor(userId: number, details: string, accountType: number) {
        this.userId = userId;
        this.details = details;
        this.accountType = accountType;
    }

    /* getter setter */
}
```

## 7.7: 채팅 서버: 
채팅 서버를 어떻게 구현할 것인지 설명하라. 특히, 다양한 백엔드 컴포넌트, 클래스, 메서드에 대해 자세히 설명하라. 어떤 문제가 가장 풀기 어려울 것으로 예상되는가?

지원하는 기능
- 온라인/오프라인 로그인
- 친구 추가 요청
- 상태 메시지의 갱신
- 시작된 채팅창에 새로운 메시지 추가

요구사항으로부터 배울 수 있는것
- 사용자, 요청 상태, 온라인 상태, 메시지 등의 개념을 정의해야함

시스템의 구성
- DB
    - 사용자 리스트, 채팅 내역 등을 보존
    - BigTable 혹은 유사한 시스템
- 클라이언트 서버간 통신
    - XML/JSON
    - 잘압축된 형태는 아니지만, 컴퓨터와 사람 모두가 잘읽을 수 있음
- 서버
    - 서버는 여러대로 구성, 데이터는 서버에 분할해 저장
    - SPOF(Single-point-of-failure)

### 핵심 개체와 메서드

유저 매니저
```typescript
/* 사용자와 관련된 핵심 메소드 */
/* 다른 이의 연락처 목록에 사용자를 추가하는 일을 처리 */
class UserManager {
    /* 싱글톤 */
    static instance: UserManager;
    /* 사용자 ID 저장 */
    private usersById: Map<Number, User>;
    /* 계정 이름 저장 */
    private usersByAccount: Map<string, User>;
    /* 온라인 상태의 유저 */
    private onlineUsers: Map<Number, User>;

    public static getInstance(): UserManager {
        if (this.instance === null) {
            this.instance = new UserManager();
        }
        return this.instance;
    }
    /* 친구 추가 요청 */
    public addUser(fromUser: User, toAccountName: String): void { }
    /* 친구 승인 요청 */
    public approveAddRequest(req: AddRequest) { }
    /* 친구 승인 거부 */
    public rejectAddRequest(req: AddRequest) { }
    public userSignedOn(accountName: String) { }
    public userSignedOff(accountName: String) { }
}
```
1. A가 addUser를 클릭하면 요청은 서버로 전달
2. 사용자 A가 requestAddUser(User B)를 호출
3. 이 메서드는 다시 UserManager.addUser를 호출
4. UserManager는 User클래스 A.sentAddRequest와 사용자 B.receiveAddRequest를 호출


유저 클래스
```typescript
class User {
    private id: Number;
    private status: UserStatus = null;

    /* 1:1 채팅방 */
    private privateChats: Map<Number, PrivateChat>;
    /* 그룹 채팅방 */
    private groupChats: Array<GroupChat>;
    /* 받은 Add Request */
    private receivedAddRequest: Map<Number, AddRequest>;
    /* 보낸 AddRequest */
    private sentAddRequest: Map<Number, AddRequest>;
    /* 주소록 */
    private contacts: Map<Number, User>;

    private accountName: String;
    private fullName: String;

    constructor(id: Number, accountName: String, fullName: String) { }
    public sendMessageToUser(): boolean { return null; }
    public sendMessageToGroup(): boolean { return null; }
    public setStatus(status: UserStatus): void { }
    public getStatus(): UserStatus { return this.status; }
    public addContact(user: User): void { }
    public receiveAddRequest(req: AddRequest): void { }
    public sendAddRequest(req: AddRequest): void { }
    public removeAddRequest(req: AddRequest): void { }
    public addConversation(conversation: GroupChat);
    public addConversation(conversation: PrivateChat);
    public addConversation(conversation: any): void { }
    public getId(): Number { return this.id; }
    public getAccountName(): String { return this.accountName; }
    public getFullName(): String { return this.fullName; }
}
```

채팅방 관련
```typescript
abstract class Conversation {
    protected participants: Array<User>;
    protected id: Number;
    protected messages: Array<Message>;

    public getMessages(): Array<Message> { return this.messages; }
    public addMessage(m: Message): boolean { return null; }
    public getId(): Number { return this.id; }
}

class PrivateChat extends Conversation {
    constructor(user1: User, user2: User) { super(); }
    public getOtherParticipant(primary: User): User { return null; }
}

class GroupChat extends Conversation {
    public removeParticipant(): void { }
    public addParticipant(user: User):void { }
}

class Message {
    private content: String;
    private date: Date;
    constructor(content: String, date: Date) { }
    public getContent(): String { return this.content; }
    public getDate(): Date { return this.date; }
}
```

상태 및 리퀘스트 클래스
```typescript
/* 다른유저의 요청을 공통화 */
class AddRequest {
    private fromUser: User;
    private toUser: User;
    private date: Date;
    private status: RequestStatus;

    constructor(from: User, to: User, date: Date, status: RequestStatus) { }public setStatus(status: RequestStatus): void { }
    public getStatus(): RequestStatus { return this.status; }
    public getFromUser(): User { return this.fromUser; }
    public getToUser(): User { return this.toUser; }
    public getDate(): Date { return this.date; }
}

class UserStatus {
    private message: String;
    private type: UserStatusType;
    constructor(message: String, type: UserStatusType) {}
    public getMessage(): String { return this.message; }
    public getType(): UserStatusType { return this.type; }
}

enum UserStatusType {
    Offline, Away, Idle, Available, Busy
}

enum RequestStatus {
    Unread, Read, Accepted, Rejected
}
```

### 풀기 어려운 문제

1. 사용자가 온라인인지 어떻게 알 수 있는가? 확실하게 알 수 있는 방법은?
    - 로그인과 로그아웃 체크만으로는 부정확할 수 있다.(인터넷이 죽는 경우 등)
    - 주기적으로 사용자의 상태를 확인하는 방법(ping)을 사용하면 정확성을 높일 수 있다.
2. 정보 불일치 문제는 어떻게 처리하는가?
    - 어떤정보는 메모리에 있고, 어떤정보는 데이터베이스에 있다고 하자.
    - 그정보들이 동기화 되지 않으면 어떻게 되겠는가?
    - 어느쪽이 맞다고 해야하는가?
3. 서버의 규모 확장성은 어떻게 확보해야하나?
    - 꽤 중요한 문제
    - 데이터를 여러 서버에 분할해서 저장해야 할 필요도 생길것
    - 분산된 정보 간 불일치 문제를 더 깊이 고민해봐야함
4. Dos(Denial of Service) 공격은 어떻게 막나?
    - (방대한양의 트래픽을 전송하여 마비시키는 행위)
    - 클라이언트들이 서버로 데이터를 푸시(push)할 수 있다.
    - 그들이 Dos공격을 시도하면 어떻게 되나?
    - 이러한 공격을 어떻게 막을 수 있나?

## 7.9 순환 배열:
CurcularArray 클래스를 구현하라. 이 클래스는 배열과 비슷한 자료구조이지만 효과적으로 순환(rotate)이 가능해야한다. 클래스는 가능한 제네릭타입(generic type) 혹은 템플릿(template)으로 구현하는게 좋고, for (obj o : circularArray)와 같이 표준 표기법으로 순회(iterate)가 가능해야한다.

CurcularArray를 구현하는 부분과 iteration을 지원해야하는 부분 2가지로 나눠서 생각해야함
### CircularArrayClass 구현

일반 배열로 CircularArray를 구현하는 방법:

1. rotate를 호출할때마다 실제 원소를 시프트 하는 방법
2. rotate를 호출할때마다 head를 이동하는 방법

2번이 효율적

```typescript
class circularArray<T> {
    private items: Array<T>;
    private head: number = 0;

    constructor(size: number) {
        this.items = new Array<T>(size);
    }

    private convert(index: number): number {
        if (index < 0) {
            index += this.items.length;
        }
        return (this.head + index) % this.items.length;
    }

    public rotate(shiftRight: number): void {
        this.head = this.convert(shiftRight);
    }

    public get(i: number): T {
        if (i < 0 || i >= this.items.length) {
            throw new Error("...");
        }
        return this.items[this.convert(i)];
    }

    public set(i: number, item: T) {
        this.items[this.convert(i)] = item;
    }
}
```
### iterator 구현하기

```typescript
class circularArray<T> {
    private items: Array<T>;
    private head: number = 0;

    private pointer: number = 0;

    constructor(size: number) {
        this.items = new Array<T>(size);
    }

    public next(): { value: T, done: boolean }{
        if (this.pointer >= this.items.length) {
            return { value: null, done: true };
        }
        const last = this.convert(this.head - 1);
        const curr = this.convert(this.pointer);
        this.pointer += 1;
        return {
            done: last === curr,
            value: this.items[curr]
        };
    }

    private convert(index: number): number {
        if (index < 0) {
            index += this.items.length;
        }
        return (this.head + index) % this.items.length;
    }

    public rotate(shiftRight: number): void {
        this.head = this.convert(shiftRight);
    }

    public get(i: number): T {
        if (i < 0 || i >= this.items.length) {
            throw new Error("...");
        }
        return this.items[this.convert(i)];
    }

    public set(i: number, item: T) {
        this.items[this.convert(i)] = item;
    }
}
```

## 7.11 파일 시스템:
메모리 상주형 파일 시스템(in-memory file system)을 구현하기 위한 자료구조와 알고리즘에 대해 설명해 보라. 가능하면 코드 예제를 들어 설명하라.

- 많은 지원자들이 너무 저수준이라 당황할 수 있는 문제
- 파일 시스템을 구성하는 컴포넌트에 대해 생각해보면 공략가능
1. 단순하게 File과 Directory로 구성
2. Directory안에는 File과 Directory의 집합이 들어있음
3. 공통속성이 많으므로 Entry로 구분

```typescript
namespace MyFileSystem {
    export abstract class Entry {
        protected parent: Directory;
        protected created: number;
        protected lastUpdated: number;
        protected lastAccessed: number;
        protected name: String;
    
        constructor(n: String, p: Directory) {
            this.name = n;
            this.parent = p;
            this.created = (new Date()).getMilliseconds();
            this.lastUpdated = (new Date()).getMilliseconds();
            this.lastAccessed = (new Date()).getMilliseconds();
        }

        public delete(): boolean {
            if (parent == null) return false;
            else return this.parent.deleteEntry(this);
        }

        public abstract getSize(): number;

        public getFullPath(): String {
            if (parent == null) return this.name;
            else return this.parent.getFullPath() + "/" + this.name;
        }

        /* getter setter */
        public getCreationTime(): number { return this.created; }
        public getLastUpadateTime(): number { return this.lastUpdated; }
        public getLastAccessedTime(): number { return this.lastAccessed; }
        public changeName(name: String): void { this.name = name; }
        public getName(): String { return this.name; }
    }
    
    export class File extends Entry{
        protected content: String;
        protected size: number;

        constructor(name: String, p: Directory, size: number) {
            super(name, p);
            this.size = size;
        }

        public getSize(): number { return this.size; }
        public getContent(): String { return this.content; }
        public setContent(c: String): void { this.content = c; }
    }

    export class Directory extends Entry {
        protected contents: Array<Entry> = [];

        constructor(n: String, p: Directory) {
            super(n, p);
        }

        public getSize(): number {
            let size = 0;
            for (let e of this.contents) {
                size += e.getSize();
            }
            return size;
        }

        public numberOfFiles(): number {
            let count = 0;
            for (let e of this.contents) {
                if (e instanceof Directory) {
                    count++;
                    count += e.numberOfFiles();
                } else {
                    count++;
                }
            }
            return count;
        }

        public deleteEntry(entry: Entry): boolean {
            let idx = null;
            for (let i in this.contents) {
                if (this.contents[i] === entry) {
                    idx = i;
                    break;
                }
            }
            if (idx !== null) {
                this.contents.splice(idx, 1);
            }
            return idx !== null;
        }
        public getContents(): Array<Entry> { return this.contents; }
    }
}
```