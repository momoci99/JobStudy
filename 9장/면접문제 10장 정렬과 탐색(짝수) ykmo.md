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


풀이 

> elementAt을 이용한 리스트의 길이 추측

- i가 너무 크면 elementAt가 -1을 반환하는것을 이용.
- 리스트를 벗어날때 까지 값을 점점 크게해봄.
- 리스트는 정렬되어있음을 이용하여 1, 2, 4, 8, 16 과 같이 늘려봄
- 리스트의 길이가 n 일때 (O log n) 시간 안에 길이를 구할 수 있음.

    왜 O(long n)인가?

    q=1에서 시작한다고 가정.

    q가 n보다 커질 때 까지 q는 두 배씩 증가함. 

    2^k = n 이되는 k값은 무엇인가? 
    
    log의 정의에 따라 k = log(n)과 같음.


- 길이를 찾았다면 이진탐색과 비슷하게 수행.
- 단, 중간 지점이 -1이라면 왼쪽만 탐색함.


```java
int search(Listy list, int vlaue) {
    int index = 1;
    while (list.elementAt(index) != -1 && list.elementAt(index) < value){
        index *=2;
    }
    return binarySearch(list, value, index / 2, index);
}



int binarySearch(Listy list, int value, int low, int high){
    int mid;

    while (low <= mid){
        mid = (low + high) / 2;
        int middle = list.elementAt(mid);
        
        //중간값이 현재 value 값보다 크거나
        //-1이라면 왼쪽을 탐색하게 한다.
        if (middle > value || middle == -1){
            high = mid - 1;
        } else if(middle < value){
            low = mid + 1;
        } else {
            return mid;
        }
    }
    return -1;
}


```

    


## 10.6 큰 파일 정렬

한 줄에 문자열 하나가 쓰여있는 20GB짜리 파일이 있다고 하자. 이 파일을 정렬하려면 어떻게 해야 할지 설명하라.

> 모든 데이터를 메모리에 올려둘 수 없다는뜻.

- 가용가능한 메모리만큼 파일을 적재.
- 각 파일을 개별적으로 정렬.
- 정렬후 하나씩 병합.
- 모든 파일의 병합이 끝나면 완벽히 정렬된 파일을 얻음

> 외부정렬이라고 부름






## 10.8 중복 찾기

1부터 N(<=32,000)까지의 숫자로 이루어진 배열이 있다. 배열엔 중복된 숫자가 나타날 수 있고, N이 무엇인지는 알 수 없다. 사용 가능한 메모리가 4KB일 때, 중복된 원소를 모두 출력하려면 어떻게 할 수 있을까?

- 4KB -> 4 * 1024(2^10) * 8 비트의 주소 공간을 사용가능.
- 32*2^10 비트는 32,000보다 큼.
- 따라서 32,000비트로 구성된 비트 벡터를 구성
- 비트 벡터가 하나의 정수를 나타내도록 함.
- 비트 벡터를 순회하면서 각 원소 v에 상응하는 비트 벡터의 비트를 1로 만듬.
- 쉽게말하자면 찾으려는 원소에 마킹하는것.

```java
void checkDuplicates(int[] array){
    BitSet bs = new BitSet(32000);
    for(int i = 0; i<array.length; i++){
        int num = array[i];
        int num0 = num - 1; // bitset은 0에서 시작, 숫자는 1에서 시작.
        if(bs.get(num0))    {
            System.out.println(num);
        } else {
            bs.set(num0);
        }        
    }
}

class BitSet {
    int[] bitset;

    public BitSet(int size){
        bitset = new int[(size >> 5) + 1]; //32로 나눈다.
    }

    boolean get(int pos){
        int wordNumber = (pos >> 5); //32로 나눈다.
        int bitNumber = (pos & 0x1F); //32로 나눈 나머지
        return (bitset[wordNumber] & (1<<bitNumber)) !=0;
    }

    void set(int pos){
        int wordNumber = (pos >> 5); //32로 나눈다.
        int bitNumber = (pos & 0x1F); //32로 나눈 나머지
        bitset[wordNumber] |= 1 << bitNumber;
    }
}

```



## 10.10 스트림에서의 순위

정수 스트림을 읽는다고 하자. 주기적으로 어떤 수의 x의 랭킹(x보다 같거나 작은 수의 개수)을 확인하고 싶다. 해당 연산을 지원하는 자료구조와 알고리즘을 구현하라. 수 하나를 읽을 때 마다 호출되는 메서드 tracker(int x)와, x보다 같거나 작은 수의 개수(x 자신은 제외)를 반환하는 메서드 getRankOfNumber(int x)를 구현하면 된다.

    입력스트림(stream) : 5, 1, 4, 4, 5, 9, 7, 13, 3
    
    getRankOfNumber(1) = 0
    getRankOfNumber(3) = 1
    getRankOfNumber(4) = 3
