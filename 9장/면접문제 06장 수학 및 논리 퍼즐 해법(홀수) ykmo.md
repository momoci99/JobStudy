#### 6.1 무거운 알약 : 약병 20개가 있다. 이 중 19개에는 1.0그램짜리 알약들이 들어 있고, 하나에는 1.1그램짜리 알약들이 들어 있다. 정확한 저울 하나가 주어졌을 때, 무거운 약병을 찾으려면 어떻게 해야할가? 저울은 딱 한번만 쓸수 있다.



1. 약병이 두개고 둘중 하나에 무거운 알약이 있다고 가정한다면
각각의 약병에서 하나의 알약을 하나씩 꺼낸다면, 두 알약의 무게의 합은 1.0 + 1.1 = 2.1 그램이지만 어느 약병에서 꺼낸게 0.1그램 더 무거운지는 알 수 없다.


2. 만약 약병 #1에서 알약을 하나 꺼내고 약병 #2에서 알약을 2개 꺼낸다면, 두가지 경우로 나누어 볼 수 있다.

    2-1. 약병 #1이 무거운쪽이었다면
    1.1 + 1.0 + 1.0 = 3.1

    2-2. 약병 #2이 무거운쪽이었다면 
    1.0 + 1.1 + 1.1 = 3.2


3. 2에서 도출한 방식으로 일반화시키면 #1의 병에서는 알약 한 개, #2의 병에서는 알약 2두 개, #3에서 세 개를 꺼내는 방식으로 모든 약병에 반복


4. 만약 모든 알약의 무게가 각각 1.0 그램이라면 저울에는 210이 된다. (1+2+3+.....+20=20*21/2=210) 만약 이보다 더 많다면 더 무거운 알약의 존재 때문이다.

5. 무거운 알약이 들어 있는 약병의 변호는 다음의 수식으로 구할 수 있다. (전체 무게-210그램) / 0.1그램










#### 6.3 도미노 : 8X8 크기의 체스판이 있는데, 대각선 반대 방향 끝에 있는 셀(cell) 두 개가 떨어져 나갔다. 하나의 노미노로 정확히 두 개의 정사각형을 덮을 수 있을때, 31개의 도미노로 보드 전체를 덮을 수 있겠는가? 여러분의 답이 옳다는 것을 증명하라(에를 들거나, 왜 불가능한지를 보이면 된다.)


직접 대입했을때 불가능함을 알게됨. 




#### 6.5 물병 : 5리터짜리 물병과 3리터짜리 물병이 있다. 물은 무제한으로 주어지지만 계량컵은 주어지지 않는다. 이 물병 두개를 사용해서 정확히 4리터의 물을 계량하려면 어떻게 해야 할까? 물병의 형태가 좀 괴상해서, 물을 정확히 절반만 담는것 따위는 불가능하다.


1. 5리터짜리 빈 물병을 만든다.

2. 5리터짜리에 3리터짜리 물을 채운다. 

3. 새로운 3리터를 2에서 만든 5리터 병에 채운다. 그럼 3리터 병에는 2리터가 빠져서 1리터만 남게된다.

4. 5리터짜리 빈 물병을 만들고 3리터를 채운다

5. 3에서 만든 1리터를 4에 채운다. 끝





#### 6.7 대재앙 : 대재앙 이후에 여왕은 출산율 때문에 근심이 이만저만이 아니다. 그래서 그녀는 모든가족은 여자 아이 하나를 낳거나 어마어마한 벌금을 내야한다는 법령을 만들어 발표했다. 만약 모든 가족이 이 정책을 따른다면 (즉, 여자 아이 한명을 낳을때 까지 계속 낳는다면) 다음세대의 남녀 비율은 어떻게 되겠는가? 남자 혹은 여자를 임심할 확률은 같다고 가정한다.

> 0.5에 가깝게 나온다.


````js
function getChild (){


    var result = {
        boy : 0,
        girl : 0
    }

    while(true){
        var percentage = Math.floor(Math.random() * 10);   
        console.log(percentage);
        //아들
        if(percentage % 2 === 0){

            result.boy = result.boy + 1;
            console.log("아들");
        }
        else {
            result.girl = result.girl + 1;
            console.log("딸");
            return result;
        }
    }

}


function createBaby(){

    var result = {
        boy : 0,
        girl : 0
    }
    
    for(var i = 0; i<3000; i++){

        var t_result = getChild();
        result.boy = result.boy + t_result.boy;
        result.girl = result.girl + t_result.girl;

    }
    console.log(result);

}

````



#### 6.9 100 라커 : 복도에 100개의 라커가 있다. 어떤 남자가 100개의 라커 문을 전부 연다. 그러고 나서 짝수 번호의 라커를 전부 닫는다. 그다음에는 번호가 3의 배수인 라커를 순서대로 찾아다니며 열려 있으면 닫고, 닫혀 있으면 연다. 이런 시긍로 복도를 100번 지나가면(마지막에는 100번째 라커의 문을 열거나 닫을것이다) 열린 라커문은 몇 개가 되겠는가?

>문제 이해 안됨..?

>50개

```js
function setUpLocker(){
    var lockerArray = [];

    for(var i = 0; i<100; i++){
        lockerArray.push({
            locker :{
                doorOpened : true
            }

        })
    }    
    return lockerArray;
}

function closeEvenLocker(lockerArray){
    var index = 1;
    for(var i = 0; i<=lockerArray.length; i++){
        if(index % 2 === 0){
            lockerArray[i].locker.doorOpened = false;
        }
        index++;
    }
    return lockerArray;
}

function toggleDoor(lockerArray){
    var index = 1;
    for(var i = 0; i<=lockerArray.length; i++){
        if(index % 3 === 0){
            
            //열려있으면 닫고
            if(lockerArray[i].locker.doorOpened){
                lockerArray[i].locker.doorOpened = false;

            //닫혀있으면 염
            }else{
                lockerArray[i].locker.doorOpened = true;
            }

            
        }
        index++;
    }

    return lockerArray;
}

function showLockerStatus(lockerArray){

    var doorOpenedCounter = 0;

    lockerArray.forEach(element => {
        if(element.locker.doorOpened){
            doorOpenedCounter++;
        }
        
    });

    console.log(doorOpenedCounter);
}

function start(){
    var lockerArray = setUpLocker();


    lockerArray =  closeEvenLocker(lockerArray);
    
    for(var i = 0; i<100; i++){
        lockerArray = toggleDoor(lockerArray);
    }

    showLockerStatus(lockerArray);
}
```