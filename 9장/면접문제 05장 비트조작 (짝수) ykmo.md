## 05. 비트 조작(짝수)

#### 5.2 2진수를 문자열로 : 0.72와 같이 0과 1 사이의 실수가 double 타입으로 주어졌을 때, 그 값을 2진수 형태로 출력하는 코드를 작성하라. 길이가 32 이하인 문자열로 2진수로 정확하게 표현할 수 없다면 ERROR를 출력하라. 

```js

//소수부분을 계속 2로 곱하여 0이 될때까지 진행하는 2진수 변환법 사용
function printBinary(num){

    var bin = "";
    //예외처리
    if(num >= 1 || num <= 0){
        return "ERROR";
    }

    while(num>0){
        
        //길이제한
        if(bin.length >= 32){
            console.log("길이제한");
            return "ERROR";
        }
        
        //주어진수 2곱
        var t_double =  num * 2;

        //2를 곱해서 1보다 크면 이진수 배열에 1을 추가
        if(t_double >= 1){
            bin = bin + "1";

            //1을 뺀다.
            num = t_double - 1;
        }
        //2를 곱해서 1보다 크면 이진수 배열에 0을 추가
        else{
            bin = bin + "0";
            num = t_double;
        }
    }
    return bin;
}
```


#### 5.4 다음 숫자 : 양의 정수가 하나 주어졌다. 이 숫자를 2진수로 표기했을때 1비트의 개수가 같은 숫자 중에서 가장 작은 수와 가장 큰 수를 구하라.

    풀이 : n 안의 1의 개수가 같을때까지 n을 증가시키거나 감소시킴



#### 5.6 변환 : 정수 A와 B를 이진수로 표현했을 때, A를 B로 바꾸기 위해 뒤집어야 하는 비트의 개수를 구하는 함수를 작성하라.

    xor의 성질을 이용하면된다. xor 했을때 bit가 1이면 서로 다르다는뜻이다.
    

```js


function differentBitCount(A,B){
    var c = A^B;

    //A XOR B의 2진수를 구한다.
    var binC = c.toString(2);

    binC.replace("0",'');

    return binC.length;
}


```



#### 5.8 선그리기 : 흑백 모니터 화면은 하나의 바이트 배열에 저장되는데, 인접한 픽셀 여덟 개를 한 바이트에 묶어서 저장한다. 화면의 폭은 w 이며, w는 8로 나누어 떨어진다(따라서 어떤 바이트도 두 행에 걸치지 않는다.) 물론, 화면 높이는 배열 길이와 화면 폭을 통해 유도해 낼 수 있다. 이때(x1, y)에 서(x2,y)까지 수평선을 그려주는 함수를 작성하라. 메서드 용법(method signature)은 다음과 같다. 

> drawLine(byte[] screen, int width, int x1, int x2, int y)

    x1에서 x2까지 for 문 돌면서 픽셀 세팅하면됨



```java
void drawLine(byte[] screen, int width, int x1, int x2, int y){

    
    
    int start_offset = x1 % 8;
    int first_full_byte = x1 / 8;
    if (start_offset != 0){
        first_full_byte++;
    }

    int end_offset = x2 % 8;;
    int last_full_byte = x2 / 8;
    if (end_offset != 7){
        last_full_byte--;
    }

    for (int b = first_full_byte; b <= last_full_byte; b++){
        screen[(width / 8) * y + b] = (byte) 0xFF;
    }

    byte start_mask = (byte) (0xFF >> start_offset);
    byte end_mask = (byte) ~(0xFF >>(end_offset + 1));

    if((x1 /8) == (x2 / 8)) {
        byte mask = (byte) (start_mask & end_mask);
        screen[(width / 8) * y + (x1 / 8)] |= mask;
    } else {
        if(start_offset != 0){
            int byte_number = (width / 8) * y + first_full_byte - 1;
            screen[byte_number] |= start_mask;
        }
        if (end_offset != 7){
            int byte_number = (width / 8) * y + last_full_byte + 1;
            screen[byte_number] |= end_mask;
        }
    }
}

```