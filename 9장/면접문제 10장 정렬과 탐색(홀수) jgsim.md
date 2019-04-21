# 면접문제 10장 정렬과 탐색(홀수)

## 10.1 정렬된 병합:
정렬된 배열 A와 B가 주어진다.
A의 끝에는 B를 전부 넣을 수 있을 만큼 충분한 여유공간이 있다.
B와 A를 정렬된 상태로 병합하는 메서드를 작성하라.

### 해답
- A와 B의 모든 원소를 다 소진할 때까지 이 둘을 비교해가면서 순서에 맞게 삽입하면된다.
- 어떤 원소를 A의 앞부분부터 삽입할 경우 기존원소를 뒤로 밀어야 한다는 문제가 생김
- 따라서 여유공간이 있는 배열 뒤쪽부터 삽입하면 된다.
- 배열B의 원소가 먼저 소진된경우, A의 남은 원소들은 이미 제자리에 있기 때문에 다시 복사할 필요는 없다.
```java
void merge(int[] a, int[] b, int lastA, int lastB) {
    int indexA = lastA - 1; // 배열 a의 마지막 원소
    int indexB = lastB - 1; // 배열 b의 마지막 원소
    int indexMerged = lastB + lastA - 1; // 병합된 배열의 마지막 위치

    // a와 b를 마지막 원소부터 병합해 나간다.
    while (indexB >= 0) {
        // a의 마지막 원소 > b의 마지막 원소
        if (indexA >= 0 && a[indexA] > b[indexB]) {
            a[indexMerged] = a[indexA]; // 복사
            indexA--;
        } else {
            a[indexMerged] = b[indexB]; // 복사
            indexB--;
        }
        indexMerged--;
    }
}
```

## 10.3 회전된 배열에서 검색:
n개의 정수로 구성된 정렬 상태의 배열을 임의의 횟수만큼 회전시켜 얻은 배열이 입력으로 주어진다고 하자.
이 배열에서 특정한 원소를 찾는 코드를 작성하라. 
회전시키기 전, 원래 배열은 오름차순으로 정렬되어있었다고 가정한다.

- 이진탐색 문제
- 정렬이 되어있으나 회전된상태라는게 까다롭다
- 하지만 자세히 보면, 배열의 반은 정상적인 순서(오름차순)으로 배열되어 있다는 것을 알 수 있다.

가능한 상황
```
배열1: {10, 15, 20, 0, 5}
배열2: {50, 5, 20, 30, 40}
배열3: {2, 2, 2, 3, 4, 2}
```
1. 배열1에서 5: 10 < 20 이므로 왼쪽 절반은 정상 순서. 10과 20 사이에 5가 없으므로 오른쪽을 탐색해야 한다.
2. 배열2에서 5: 50 > 20 이므로, 오른쪽 절반은 정상 순서. 50과 20 사이에 5가 없으므로 왼쪽을 탐색해야 한다.
3. 배열3처럼 가운데 원소와 맨왼쪽 원소의 값이 같은 경우.
    - 맨오른쪽 원소의 값이 다른지를 살핌
    - 다르면 오른쪽 절반 탐색
    - 아니라면 양쪽을 전부 탐색

```java
int search(int a[], int left, int right, int x) {
    int mid = (left + right) / 2;
    if (x == a[mid]) { // 원소를 찾음
        return mid;
    }
    if (right < left) {
        return -1;
    }

    /* 왼쪽 절반이나 오른쪽 절반 중 하나는 정상적으로 정렬된 상태여야 한다. 
     * 어느쪽이 정상적으로 정렬되어 있는지 확인하고, 정상적으로 정렬된 부분을
     * 이용해서 어느쪽에서 x를 찾아야 하는지 알아낸다. */
    if (a[left] < a[mid]) { // 왼쪽이 정상적으로 정렬된 상태
        if (x >= a[left] && x < a[mid]) {
            return search(a, left, mid - 1, x); // 왼쪽을 탐색
        } else {
            return search(a, mid + 1, right, x); // 오른쪽을 탐색
        }
    } else if (a[mid] < a[left]) { // 오른쪽이 정상적으로 정렬된 상태
        if (x > a[mid] && x <= a[right]) {
            return search(a, mid + 1, right, x); // 오른쪽을 탐색
        } else {
            return search(a, left, mid - 1, x); // 왼쪽을 탐색
        }
    } else if (a[left] == a[mid]) { // 왼쪽 혹은 오른쪽 절반이 전부 반복된다.
        if (a[mid] != a[right]) { // 오른쪽이 다르다면 오른쪽 탐색
            return search(a, mid + 1, right, x); // 오른쪽 탐색
        } else { // 아니라면 양쪽 모두 탐색
            int result = search(a, left, mid - 1, x); // 왼쪽 탐색
            if (result == -1) {
                return search(a, mid + 1, right, x); // 오른쪽 탐색
            } else {
                return result;
            }
        }
    }
    return -1;
}
```

# 10.5 드문드문 탐색:
빈 문자열이 섞여 있는 정렬된 문자열 배열이 주어졌을 때, 특정 문자열의 위치를찾는 메서드를 작성하라.

- 빈문자열이 있으므로 이진탐색을 변형하여 적용해야함.
- 수정해야 하는곳은 mid 지점의 원소가 빈 문자열일 경우 mid를 가장 가까운 일반문자열을 가리키도록 하는것

```java
int search(String[] strings, String str, int first, int last) {
    if (first > last) return -1;
    /* mid를 중간지점으로 옮긴다. */
    int mid = (last + first) / 2;

    /* mid가 비어있다면 mid와 가장 가까운 일반 문자열을 찾는다 */
    if (strings[mid].isEmpty()) {
        int left = mid - 1;
        int right = mid + 1;
        while (true) {
            if (left < first && right > last) {
                return -1;
            } else if (right <= last && !strings[right].isEmpty()) {
                mid = right;
                break;
            } else if (left <= fist && !strings[left].isEmpty()) {
                mid = left;
                break;
            }
            right++;
            left--;
        }
    }

    /* 문자열을 확인한 뒤 필요하면 재귀 호출을 수행한다. */
    if (str.squals(strings[mid])) { // 찾았다!
        return mid;
    } else if (strings[mid].compareTo(str) < 0) { // 오른쪽 탐색
        return search(strings, str, mid + 1, last);
    } else { // 왼쪽 탐색
        return search(strings, str, first, mid - 1);
    }
}
```
- 최악의 경우에 이 알고리즘의 시간복잡도는 O(n)
- 빈문자열을 찾는경우, 어디것을 찾아야 할지, 오류로 처리해야 할지 논의 필요

## 10.7 빠트린 정수:
음이 아닌 정수 40억 개로 이루어진 입력파일이 있다. 이 파일에 없는 정수를 생성하는 알고리즘을 작성하라. 단, 메모리는 1GB만 사용할 수 있다.

- 정수가 int 형이라고 가정한다면, 중복된 값이 있을것이다.
- 제공된 메모리는 1GB, 즉 80억 비트가 있으므로 모든 정수값을 메모리의 각 비트에 대응시킬 수 있다.

1. 40억 비트 크기인 비트 벡터(bit vector)를 만든다. 비트 벡터란 int형(혹은 다른 자료형) 배열의 각 비트에 불린 값을 압축해서 자장하는 배열을 뜻한다. 각 int는 32개의 불린 값을 저장할수 있다.
2. BV를 0으로 초기화 한다.
3. 파일의 모든 숫자를 읽어 나가면서 BV.set(num, 1)을 호출한다.
4. 이제 0번째 인덱스부터 BV를 다시 훑는다
5. 값이 0인 첫 번째 인덱스를 반환한다.
```java
long numberOfInts = ((long) Integer.MAX_VALUE) + 1;
byte[] bitfield = new byte [(int) (numberOfInts / 8)];
String filename = "..."

void findOpenNumber() throws FileNotFoundException {
    Scanner in = new Scanner(new FileReader(finename));
    while (in.hasNextInt()) {
        int n = in.nextInt ();
        /* OR 연산을 사용해서 bitfield에 상응하는 숫자를 찾아 n번째
        * 위치에 값을 넣는다(예를 들어 숫자 10은 바이트(byte)
        * 배열에서 두 번째 인덱스의 2번째 비트의 위치를 의미한다.) */
        bitfield [n / 8] |= 1 << (n % 8);
    }

    for (int i = 0; i < bitfield.length; i++) {
        for (int j = 0; j < 8; j++) {
            /* 각 바이트의 비트를 개별적으로 확인한다.
             * 만약 0번째 비트가 찾고자 하는 비트라면 그에 상응하는 값을 출력한다. */
            if (bitfield[i] & (1 << j)) == 0) {
                System.out.println (i * 8 + j);
                return;
            }
        }
    }
}

```

## 정렬된 행렬 탐색:
각 행과 열이 오름차순으로 정렬된 M x N 행렬이 주어졌을때, 특정한 원소를 찾는 메서드를 구현하라.

- 구현 방법은 2가지가 있을 수 있음
### 해법 #1: 쉬운 해법
- 간단하게 행마다 2진 탐색을 시도 하는것
- 총 M개의 행이 있고, 각 행을 탐색하는데 O(log n)이 걸리므로
- 시간복잡도는 O(m log n)
- 알고리즘을 더 발전시켜 나가기 전에, 면접관에게 이런게 있다고 언급하는것도 좋음

정렬에 대한 사실
- 열의 시작점 n > x: x는 열 왼쪽
- 열의 마지막 n < x: x는 열 오른쪽
- 행의 시작점 n > x: x는 행 위쪽
- 행의 마지막 n < x: x는 행 아래쪽

```java
boolean findElement(int[][] matrix, int elem) {
    int row = 0;
    int col = matrix[0].length - 1;
    while (row < matrix.length && col >= 0) {
        if (matrix[row][col] == elem) {
            return true;
        } else if (matrix[row][col] > elem) {
            col--;
        } else {
            row++;
        }
    }
    return false;
}

```
## 10.11 Peak과 Valley:
정수 배열에서 'peak'란 현재 원소가 인접한 정수보다 크거나 같을 때를 말하고,
'valley'란 현재 원소가 인접한 정수보다 작거나 같을때를 말한다.
예를 들어 {5, 8, 6, 2, 3, 4, 6}이 있으면, {8, 6}은 'peak'가 되고,
{5, 2}는 'valley'가 된다. 정수 배열이 주어졌을때, 'peak'와 'valley'가 번갈아 등장하도록 정렬하는 알고리즘을 작성하라.

### 부분 최적 해법
- 정렬되지 않은 배열이 주어지고 정렬을 했다고 가정
- peak를 제자리에 놓으면 자동적으로 valley도 됨
예시
```
  0 1 4 7 8 9
1. 0은 괜찮음
2. 1은 잘못 놓였다. 1의 위치를 0 또는 4와 바꿀 수 있다.
  1 0 4 7 8 9
3. 4는 괜찮다
4. 7도 잘못 놓여졌다. 4 혹은 8과 바꿀 수 있다. 4랑 바꾸자
  1 0 7 4 8 9
5. 9도 잘못 놓여졌다. 8과 바꾸자
  1 0 7 4 9 8
```

알고리즘 명시

1. 배열을 오름차순으로 정렬
2. 인덱스 1에서부터 원소를 두개씩 건너뛰면서 읽어 나감
3. 각 원소를 이전원소와 바꾼다.
    - 세개의 원소가 항상 small <= medium <= large 순서로 놓임
    - 원소를 맞바꾸면 중간값이 언제나 peak가 된다


```java
vaoid sortValleyPeak(int[] array) {
    Arrays.sort(array);
    for (int i = 1; i < array.length; i += 2) {
        swap(array, i - 1, i);
    }
}

void swap(int[] array, int left, int right) {
    int temp = array[left];
    array[left] = array[right];
    array[right] = temp;
}
```

### 최적 해법
- 최적화 하려면 정렬하는 부분을 제거해야함.
- 배열의 값은 small <= medium <= large로 볼 수 있다.
- 따라서 숫자 0, 1, 2로만 표현할 수 있다.

숫자 경우의 수
```
0 1 2
0 2 1 // peak
1 0 2 
1 2 0 // peak
2 1 0
2 0 1
```

숫자를 모두 peak로 변경
```
0 1 2 -> 0 2 1
0 2 1 // peak
1 0 2 -> 1 2 0
1 2 0 // peak
2 1 0 -> 1 2 0
2 0 1 -> 0 2 1
```

두 원소를 맞바꾸었을때 이전에 처리했던 수열의 규칙이 깨지는 경우가 있을까?
- 중간값과 왼쪽 값을 맞바꾸면 왼쪽은 valley가 된다.
- 중간값이 왼쪽 값보다 작으므로 valley에 그보다 더작은 값을 놓게 되는꼴
- 결국 규칙은 깨지지 않는다.

```java
void sortValleyPeak(int[] array) {
    for (int i = 1; i < array.length; i += 2) {
        int biggestIndex = maxIndex(array, i - 1, i i + 1);
        if (i != biggestIndex) {
            swap(array, i , biggestIndex);
        }
    }
}

void maxIndex(int[] array, int a, int b, int c) {
    int len = array.length;
    int aValue = a >= 0 && a < len ? array[a] : Integer.MIN_VALUE;
    int bValue = b >= 0 && b < len ? array[b] : Integer.MIN_VALUE;
    int cValue = c >= 0 && c < len ? array[c] : Integer.MIN_VALUE;

    int max = Math.max(aValue, Math.max(bValue, cValue));
    if (aValue == max) return a;
    else if (bValue == max) return b;
    else return c;
}
```
