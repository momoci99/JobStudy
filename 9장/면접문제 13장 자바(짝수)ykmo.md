# 면접문제 13장 자바 (짝수)


## 2.finally에서의 반환
자바의 finally 블록은 try-catch-finally의 try블록 안에 return문을 넣어도 실행되는가?


> 해답 : 실행된다. try 블록을 벗어나는순간 finally 블록에 진입한다.

[링크](https://stackoverflow.com/questions/65035/does-a-finally-block-always-get-executed-in-java)


```java

try {  
    something();  
    return success;  
}  
catch (Exception e) {   
    return failure;  
}  
finally {  
    System.out.println("I don't know if this will get printed out");
}


```

    위 코드의 경우 something()함수가 종료되고 `return`이 실행되어도 finally 이후 `return`된다.



`finally` 블록이 실행되지 않는 경우.

1. System.exit() 가 호출되어 프로그램이 종료되버렸을때.

2. JVM이 먼저 터졌을때

3. try나 catch 블록내에 무한 루프가 존재할때.

4. OS가 강제로 JVM 프로세스를 종료시켰을때

5. 호스트 시스템이 죽었을때(전원 종료, 블루스크린, 하드웨어 에러 등등)

6. 데몬 thread내에서 finally구문이 실행될 예정이었으나 메인 thread가 죽었을때



## 4. 제네릭 vs 템플릿
자바 제네릭(generic)과 c++템플릿(template)의 차이를 설명하라.

### 제네릭(generic)

- 타입 제거(type erasure)라는 개념에 근거
- JVM이 바이트 코드로 변환시 인자로 주어진 타입을 제거함


```java
Vector<String> vector = new Vector<String>();
vector.add(new string("hello"));
String str = vector.get(0);

//컴파일러는 이 코드를 아래와 같이 변환

Vector vector = new Vector();
vector.add(new String("hello"));
String str = (String) vector.get(0);

```
- 제네릭이 있다고하여 크게 달라지는것은 없음.
- 자바 제네릭을 때로는 문법적 양념(syntactic sugar)이라고 부름.



### 템플릿(Template)

- 우아한 형태의 메크로
- 컴파일러는 인자로 주어진 각각의 타입에 대해 별도의 템플릿 코드를 생성한다.
- 그 예로 MyClass<Foo>가 MyClass<Bar>와 정적변수(static variable)을 공유하지 않는것을 알 수 있음.
- 하지만 MyClass<Foo>로 만들어진 두 객체는 정적변수를 공유함.
```cpp
/*** MyClass.h ***/
template<class T> class MyClass {
    public:
        static int val;
        MyClass(int v) {val = v;}
};

/*** MyClass.app ***/
template<typename T>
int MyClass<T>::bar;

template class MyClass<Foo>;
template class MyClass<Bar>;

/*** main.cpp ***/

MyClass<Foo> * foo1 = new MyClass<Foo>(10);
MyClass<Foo> * foo2 = new MyClass<Foo>(15);
MyClass<Bar> * bar1 = new MyClass<Bar>(20);
MyClass<Bar> * bar2 = new MyClass<Bar>(35);

int f1 = foo1->val;//15
int f2 = foo2->val;//15
int b1 = bar1->val;//35
int b2 = bar2->val;//35

```

### 차이점

- 자바의 정적변수는 MyClass로 만든 모든 객체가 공유함. 제네릭 인자의 타입과는 상관없다.
- C++템플릿은 int, double같은 기본 타입을 인자로 전달가능, 제네릭은 불가함(Integer 사용)
- 자바는 제네릭 타입인자를 제한할 수 있음. `CardDeck`을 제네릭 클래스로 정의할 때 `CardGame`의 하위 클래스만 사용되도록 제한할 수 있다.
- C++템플릿은 인자로 주어진 타입으로부터 객체를 생성할 수 있다. 자바는 불가함.
- 자바의 제네릭 타입의 인자는 정적 메서드나 변수를 선언하는 데 사용될 수 없다. MyClass<Foo>나 MyClass<Bar>이 메서드와 변수를 공유함.
- C++은 이 두 클래스를 다른 클래스로 처리하므로 템플릿 타입 인자를 정적 메서드나 변수를 선언하는데 사용 가능
- 자바에서 MyClass로 만든 모든 객체는 제네릭 타입 인자에 관계없이 모두 동등한 타입임. 실행시간에 타입 인자 정보 삭제
- C++은 다른 템플릿 타입 인자를 사용해 만든 객체는 서로 다른 타입의 객체임.

> 자바 제네릭과 C++템플릿은 비슷해보이나 매우 다름.




## 6. 객체 리플렉션
자바의 객체 리플렉션(object reflection)을 설명하고, 이것이 유용한 이유를 나열하라.


> 자바 클래스와 객체에 대한 정보를 프로그램 내에서 동적으로 알아 낼 수 있도록하는 기능.


- 프로그램 실행 시간(runtime)에 클래스 내부의 메서드와 필드에 대한 정보를 얻을 수 있음.

- 클래스의 객체를 생성할 수 있다.

- 접근제어자(private)에 관계없이 해당 필드에 대한 참조를 얻어내어 값을 가져오가나 설정할 수 있다.


예

```java
/* 인자(parameters) */
Object[] doubleArgs = new Object[] {4.2, 3.9};

/* 클래스 가져오기 */
Class rectangleDefinition = Class.forName("MyProj.Rectangle");

/* Rectangle rectangle = Rectangle(4.2, 3.9); 와 같다 */
Class[] doubleArgsClass = new Class[] {double.class, double.class};
Constructor doubleArgsConstructor = retangleDefinition.getConstructor(doubleArgsClass);


Rectangle rectangle = (Rectangle) doubleArgsConstructor.newInstance(doubleArgs);

/*Double area = rectangle.area(); 와같다*/
Method m = rectangleDefinition.getDeclaredMethod("area");
Double area = (Double) m.invoke(rectangle);


```
위의 코드는 아래와 같다.

```java
Rectangle rectangle = new Rectangle(4.2, 3.9);
Double area = rectangle.area();
```

- 객체 리플렉션은 아래와 같은 이점이 있다.
    - 프로그램이 어떻게 동작하는지 *실행시간* 에 관측하고 조정할 수 있도록 해준다.
    - 메서드나 생성자, 필드에 직접 접근하므로 디버깅이나 테스트할때 유용함.
    - 호출할 메서드를 몰라도 이름을 사용하여 호출할 수 있음
        - 메서드 이름을 주면 해당정보를 사용하여 객체를 생성하고 메서드를 호출 할 수 있음.
        - 이 과정을 리플렉션 없이 시도하면 if문을 매우 복잡하게 사용해야함.



## 8. 람다 랜덤
람다(lamda) 표현식을 사용해서 임의의 부분집합을 반환하는 함수 List getRandomSubset(List<Integer> list)를 작성하라. 공집합을 포함한 모든 부분집합이 선택될 확률은 같아야 한다.


```Java
//람다 표현식을 안쓴 코드
List<Integer> getRandomSubset(List<Integer> list){
    List<Integer> subset = new ArrayList<Integer>();
    Random random = new Random();
    for (int item : list){
        /* 동전 던지기 */
        if(random.nextBoolean()){
            subset.add(item);
        }
    }
    return subset;
}


```Java
//람다 표현식 사용
List<Integer> getRandomSubset(List<Integer> list){
    Random random = new Random();
    List<Integer> subset = list.stream().filter(
        k->{return random.newxtBoolean();/*동전 던지기*/
        }).collect(Collectors.toList());
    return subset;
}

```


```Java
//함수 내부에 정의된 Predicate 사용
Random random = new Random();
Predicate<Object> flipCoin = o -> {
    return random.nextBoolean();
};

List<Integer> getRandomSubset(List<Integer> list){
    List<Integer> subset = list.stream().filter(flipCoin).collect(Collectors.toList());

    return subset;
}



```

