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