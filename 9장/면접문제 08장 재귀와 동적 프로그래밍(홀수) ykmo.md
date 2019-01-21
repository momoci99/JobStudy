## 8. 재귀와 동적 프로프래밍(홀수)

#### 8.1 트리플 스텝 
어떤 아이가 n개의 계단을 오른다. 한 번에 1계단 오르기도 하고, 2계단이나 3계단을 오르기도 한다. 계단을 오르는 방법이 몇 가지가 있는지 계산하는 메서드를 구현하라.


- 문제해석
    - ex  : 5개의 계단을.. 
    - 1, 2, 2
    - 3, 1, 1
    - 2, 1 ,1

- 단 한번에 4개 이상의 계단을 오르지는 않는듯.

이런식으로 계단을 오르는 방법이 몇가지 있는지를 계산하는 메서드 구현하라는 뜻


    

- 우선 문제를 바로 풀수는 없으나 n번째 계단을 딛기 바로 직전에 마지막에 딛는 계단이 무엇인지는 알수 있음.

- n 번째 계단(마지막 계단)을 오르는 모든 경로는 1계단, 2계단 3계단 전까지의 경로를 더하면 됨.

n-1 번째 계단에서 한 계단 올라가기
n-2 번째 계단에서 두 계단 올라가기
n-3 번째 계단에서 세 계단 올라가기


이 세가지 경우의 경로를 모두 더하면 됨.

> 주의 : 각 경우의 수는 독립적인 시행이므로 곱해선 안된다. 



1. 무식한 방법(brute force)
- countWays(n-1) + countWays(n-2) + countWays(n-3)

```js
function countWays(n){
    if (n<0){
        return 0;
    } else if(n === 0){
        return 1;
    } else {
        return countWays(n-1) + countWays(n-2) + countWays(n-3);
    }
}

//countWays(3) -> 4
//countWays(4) -> 7
//countWays(5) -> 13

```

2. 메모이제이션 방법
- 같은 값을 구하기 위해 countWays가 불필요하게 중복 호출되는 경우 발생
- n을 이전에 본적이 있다면 캐시에 저장. 값을 새로 계산할때마다 이를 캐시에 저장함.

```cs
//메소드 오버로딩으로 C# 사용


//아래 해법은 int형 자료형의 한계로 오버플로 발생함.
//면접관에게 오버플로 문제가 있다는것을 제시하는것이 좋음.

int countWays(int n){
    int[] memo = new int[n + 1];
    Arrays.fill(memo, -1);
    return countWays(n, memo);
}

int countWays(int n, int[] memo){
    if(n < 0){
        return 0;
    } else if(n == 0){
        return 1;
    } else if(memo[n] > -1){
        return memo[n];
    } else {
        memo[n] = countWays(n - 1, memo) + countWays(n - 2, memo) + countWays(n -3, memo);
        return memo[n];
    }
}

```


#### 8.3 마술 인덱스 
배열 A[0 ... n-1] 에서 A[i] = i 인 인덱스를 마술 인덱스(magic index)라 정의한다. 정렬된 상태의 배열이 주어졌을 때, 마술 인덱스가 존재한다면 그 값을 찾는 메서드를 작성하라. 배열 안에 중복된 값은 없다.


풀이

1. 무식한 방법
> 단순히 배열을 순회하면서 조건에 맞는 원소를 찾아 반환.

```js
function magicSlow(array){
    for(var i = 0; i<array.length; i++){
        if(array[i] === i){
            return i;
        }
    }
    return -1;
}

```

2. 정렬된 상태를 이용한 이진 탐색.

마술 인덱스 특성상 가운데 지점의 원소를 기준으로 마술 인덱스는 오른쪽에 있다.

|원소값 | -40 | -20 | -1 | 1 | 2 | 3 | 5 | 7 | 9 | 12 | 13 |
|-----|-----|-----|----|---|---|---|---|---|---|----|----|
|인덱스 | 0   | 1   | 2  | 3 | 4 | 5 | 6 | 7 | 8 | 9  | 10 |

이유
- 가운데 원소가 A[5] = 3이고 A[mid] < mid -> 3 < 5  마술 인덱스는 그 오른쪽에 있음을 알 수 있다.

- 왼쪽에 없는 이유는 배열의 원소값이 감소함에 따라 인덱스값도 감소한다.
- 즉 가운데 원소의 값이 이미 마술 인덱스가 되기에 작다면 왼쪽으로 진행할 수록 마술 인덱스가 되기에는 너무 작은 값이 된다.


```Cs
int magicFast(int[] array){
    return magicFast(array, 0, array.length - 1);
}

int magicFast(int[] array, int start, int end){
    if(end < start){
        return -1;
    }
    int mid = (start + end) / 2;
    if (array[mid] == mid){
        return mid;
    } else if (array[mid] > mid){
        return magicFast(array, start, mid -1);
    } else {
        return magicFast(array, mid + 1, end);
    }

}
```

단, 중복값이 배열내에 존재한다면 위의 알고리즘은 제대로 동작하지 않는다. 좌측에 있는지 오른쪽에 있는지 알 수 없기 때문.




```cs
//아래 코드는 모든 원소가 전부 다른값을 가진다면
//무식한 방법과 거의 동일한 방식으로 동작한다.
int magicFast(int[] array){
    return magicFast(array, 0, array.length -1);
}

int magicFast(int[] array, int start, int end){
    if(end < start) {return -1;}

    int midIndex = (start + end) /2;
    int midValue = array[midIndex];
    if(midValue == midIndex){
        return midIndex;
    }

    //왼쪽 탐색
    int leftIndex = Math.min(midIndex -1, midValue);
    int left = magicFast(array, start, leftIndex);
    if(left >= 0){
        return left;
    }

    //오른쪽 탐색
    int rightIndex = Math.max(midIndex + 1, midValue);
    int right = magicFast(array, rightIndex, end);
    return right;

}
```








#### 8.5 재귀 곱셈
'*'  연산자를 사용하지 않고 양의 정수 두 개를 곱하는 재귀 함수를 작성하라. 덧셈(addition), 뺄셈(subtraction), 비트 시프팅(bit shifiting) 연산자를 사용할 수 있지만, 이들의 사용 횟수를 최소화해야 한다.

풀이 : 

- 곱셉은 결국 여러번 더하는 행위이다.
- 8 * 7 = (8+8+8+8+8+8+8+8)
- 혹은 8 X 7 격자판의 셀 갯수를 카운팅한다고 생각.



해법  1)

- 단순히 셀을 하나씩 셀수도 있으나 느림.
- 절반을 센 뒤 이를 곱절로 만드는 과정을 재귀로 풀 수도 있음.
- 단 짝수가 아닌경우 곱절이 불가능하므로 하나씩 세서 더해야함.

```cs
int minProduct(int a,int b){
    int bigger = a < b ? b : a;
    int smaller = a < b ? a : b;
    return minProductHelper(smaller, bigger);
}

int minProductHelper(int smaller, int bigger){
    if(smaller === 0){
        return 0;
    } else if(smaller === 1){
        return bigger;
    }

    int s = smaller >> 1;
    int side1 = minProduct(s, bigger);
    int side2 = side1;
    if (smaller % 2) === 1){
        side2 = minProductHelper(smaller - s, bigger);
    }

    return side1 + side2;
}

```

해법 2)

- 해법 1에서 중복되는 부분을 캐시에 넣어 메모이제이션 패턴을 적용

```cs

int minProduct(int a, int b){
    int bigger = a < b ? b : a;
    int smaller = a < b ? a : b;
    int memo[] = new int[smaller + 1];
    return minProduct(smaller, bigger, memo);

}

int minProduct(int smaller, int bigger, int[] memo){
    if(smaller == 0){
        return 0;
    } else if(smaller == 1){
        return bigger;
    } else if(memo[smaller] > 0){
        return memo[smaller];
    }

    //절반을 구함. 짝수가 아니라면 나머지 절반도 구함. 짝수라면 곱절 수행
    int s = smaller >> 1;
    int side1 = minProduct(s, bigger, memo);
    int side2 = side1;
    if(smaller % 2 == 1){
        side2 = minProduct(smaller - s, bigger, memo);
    }
    
    //더해서 캐시에 삽입
    memo[smaller] = side1 + side2;
    return memo[smaller];


    

}

```

해법 3)
```cs
//좀더 효율일 높힌 코드
int minProduct(int a, int b){
    int bigger = a < b ? b : a;
    int smaller = a < b ? a : b;
    return minProductHelper(smaller, bigger);

}

int minProductHelper(int smaller, int bigger){
    if(smaller == 0){
        return 0;
    }
    else if(smaller == 1){
        return bigger;
    }
    
    int s = smaller >> 1;
    int halfProd = minProductHelper(s, bigger);
    if(smaller % 2 == 2){
        return halfProd + halfProd;
    } else{
        return halfProd + halfProd + bigger;
    }
}
```




#### 8.7 중복 없는 순열
문자열이 주어졌을 때 모든 경우의 순열을 계산하는 메서드를 작성하라. 단, 문자는 중복되어 나타날 수 없다.


풀이

풀이 1)  초기사례의 확장법(첫 n-1개 문자의 순열을 이용해서 만들기)

P(a1) = a1

P(a1a2) = a1a2, a2a1

P(a1a2a3) = a1a2a3, a1a3a2, a2a1a3, a2a3a1, a3a1a2, a3a2a1


a1a2a3의 순열을 이용해서 a1a2a3a4의 순열을 만드는 방법은

a1a2a3의 모든 순열에서 모든 위치에 a4를 넣어주면 a1a2a3a4의 모든 순열을 얻을 수 있다.


```cs
ArrayList<String> getPerms(String str){
    if(str == null) {return null;}

    ArrayList<String> permutations = new ArrayList<String>();
    if(str.length() == 0) {
        permutations.add("");
        return permutations;
    }

    char first = str.charAt(0);

    String remainder = str.substring(1);//첫 문자를 지운다
    ArrayList<String> words = getPerms(remainder);
    for(String word : words){
        for(int j = 0; j <= word.length(); j++){
            String s = insertCharAt(word, first, j)
            permutations.add(s);
            
        }
    }
    return permutations;

}

String insertCharAt(String word, char c, int i){
    String start = word.substring(0, i);
    String end = word.substring(i);
    return start + c + end;
}


```



풀이 2) 모든 n-1개 문자의 부분 문자열의 순열을 이용해서 만들기


```cs
ArrayList<String> getPerms(String remainder){
    int len = remainder.length();
    ArrayList<String> result = new ArrayList<String>();

    if(len == 0){
        result.add("");//길이가 0인 문자열을 반환
        return result;
    }

    for(int i = 0; i<len; i++){

        String before = remainder.substring(0, i);
        String after = remainder.substring(i + 1, len);
        ArrayList partials = getPerms(before + after);

        //i를 각 순열의 앞에 덧붙인다.
        for (String s : partials){
            result.add(remainder.charAt(i) + s);
        }
    }
}


```




#### 8.9 괄호
n-쌍의 괄호로 만들 수 있는 모든 합당한(괄호가 적절히 열리고 닫힌)조합을 출력하는 알고리즘을 구현하라.
 

```cs 
void addParen(ArrayList<String> list, int leftRem, int rightRem, char[] str, int index){
    if(leftRem < 0 || rightRem < leftRem) return; //잘못된 상태는 리턴

    if(leftRem == 0 && rightRem == 0){
        list.add(String.copyValueOf(str));
    } else {
        str[index] = '(';
        addParen(list, leftRem - 1, rightRem, str, index + 1);

        str[index] = ')';
        addParen(list, leftRem, rightRem-1, str, index + 1);
    }
}

ArrayList<String> generatParens(int count){
    char[] str = new char[count*2];
    ArrayList<String> list = new ArrayList<String>();
    addParen(list, count, count, str, 0);
    return list;
}


```



#### 8.11 코인
쿼터(25센트), 다임(10센트), 니켈(5센트), 페니(1센트)의 네 가지 동전이 무한히 주어졌을 때, n센트를 표현하는 모든 방법의 수를 계산하는 코드를 작성하라.


#### 8.13 박스 쌓기 
너비 w, 높이 h, 깊이 d, 인 박스n개가 있다. 상자는 회전시킬 수 없으며, 다른 상자 위에 올려 놓을 수 있다. 단, 아래 놓인 상자의 너비, 높이, 깊이가 위에 놓인 상자의 너비, 높이, 깊이보다 더 클때에만 가능하다. 이 상자들로 쌓을 수 있는 가장 높은 탑을 구하는 메서드를 작성하라. 탑의 높이는 탑을 구성하는 모든 상자의 높이 합니다.