# 면접문제 10장 (짝수)

## 10.2 Anagram 묶기

철자 순서만 바꾼 문자열(anagram)이 서로 인접하도록 문자열 배열을 정렬하는 메서드를 작성하라.
(Write a method to sort an array of strings so that all the anagrams are next to each
other.)

- 풀이

> 아나그램으로 이루어진 문자열중 동일한 문자를 그룹핑하여라

    ex 
    input  {god, cab, abc, dog, man} 

    output {abc, cab, dog, god, man} 

    abc<->cab
    dog<->god
    man

[참고링크](https://stackoverflow.com/questions/15515324/how-to-sort-an-array-of-strings-with-anagrams-next-to-each-other)




- 두 문자열이 서로 같은 문자열인지 확인 할 수 있는 쉽고 빠른 방법이 필요함.
- 문자를 같은 순서로 배열하여 두 단어가 서로 같은지 아닌지 쉽게 확인 가능

#### 방법 1)
- 비교 연산자를 변경.
- 표준 정렬 알고리즘(병합, 큌)을 그대로 사용.
- 두 개의 문자열이 아나그램일때 참을 반환

#### 방법 1-1)

> 아나그램을 쉽게 확인하는 방법

- 각 문자의 출현 빈도를 센 후 같으면 참을 반환
- 혹은 문자열을 정렬한 뒤 비교


```java

//비교 연산자를 구현한 코드
class AnagramComparator implements Comparator<String> {
    public String sortChars(String s){
        char[] content = s.toCharArray();
        Array.sort(content);
        return new String(content);
    }
    
    public int compare(String s1, String s2){
        return sortChars(s1).compareTo(sortChars(s2));
    }
}
```

    위의 방법은 O(n log(n)) 이다.


#### 방법 2) 

> 배열을 완전히 정렬하지 않는점에 착안.

- 버킷정렬의 변형
- 해시를 사용. 정렬된 단어로부터 실제 단어로의 매핑 생성

    acre -> {acre, race, care} 에 대응되도록 묶는다.

- 이 방식으로 모든 인접 문자열을 리스트에 묶고 다시 배열에 삽입


```java

void sort(String[] array){
    HashMapList<String, String> mapList = new HashMapList<String, String>();

    /* anagram 단어 그룹 생성 */
    for (String s : array){
        String key = sortChars(s);
        mapList.put(key, s);
    }

    /* 해시 테이블을 배열로 변환 */
    int index = 0;
    for (String key : mapList.keySet()){
        ArrayList<String> list = mapList.get(key);
        for(String t : list){
            array[index] = t;
            Index++;
        }
    }
}

String sortChars(String s){
    char[] content = s.toCharArray();
    Array.sort(content);
    return new String(content);
}


```













## 10.4 크기를 모르는 정렬된 원소 탐색

배열과 비슷하지만 size 메서드가 없는 Listy라는 자료구조가 있다. 여기에는 i 인덱스에 위치한 원소를 O(1) 시간에 알 수 있는 elementAt(i) 메서드가 존재한다. 만약 i가 배열의 범위를 넘어섰다면 -1을 반환한다(이 때문에 이 자료구조는 양의 정수만 지원한다). 양의 정수가 정렬된 Listy가 주어졌을 때, 원소 x의 인덱스를 찾는 알고리즘을 작성하라. 만약 x가 여러 번 등장한다면 아무거나 하나만 반환하면 된다.

## 10.6 큰 파일 정렬

한 줄에 문자열 하나가 쓰여있는 20GB짜리 파일이 있다고 하자. 이 파일을 정렬하려면 어떻게 해야 할지 설명하라.

## 10.8 중복 찾기

1부터 N(<=32,000)까지의 숫자로 이루어진 배열이 있다. 배열엔 중복된 숫자가 나타날 수 있고, N이 무엇인지는 알 수 없다. 사용 가능한 메모리가 4KB일 때, 중복된 원소를 모두 출력하려면 어떻게 할 수 있을까?

## 10.10 스트림에서의 순위

정수 스트림을 읽는다고 하자. 주기적으로 어떤 수의 x의 랭킹(x보다 같거나 작은 수의 개수)을 확인하고 싶다. 해당 연산을 지원하는 자료구조와 알고리즘을 구현하라. 수 하나를 읽을 때 마다 호출되는 메서드 tracker(int x)와, x보다 같거나 작은 수의 개수(x 자신은 제외)를 반환하는 메서드 getRankOfNumber(int x)를 구현하면 된다.

    입력스트림(stream) : 5, 1, 4, 4, 5, 9, 7, 13, 3
    
    getRankOfNumber(1) = 0
    getRankOfNumber(3) = 1
    getRankOfNumber(4) = 3
