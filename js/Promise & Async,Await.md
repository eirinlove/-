## 비동기와 동기



### 개요 (동기, 비동기)

---

**JavaScript는 동기식 언어이다. (Synchronous)**
	한 번에 하나의 작업을 수행하며, 한 작업이 실행되는동안 다른 작업은 멈춘 상태를 유지,
	자신의 차례를 기다린다.

​	그렇기 때문에 함수1 ~ 함수5 의 실행과정에서
​	하나의 함수만 오류를 범해도, 나머지 함수는 작동하지 않는다.

​	예를 들어, 

​					console.log("1");
​					console.log("2");
​					console.log("3");

​	은 1,2,3 이 순서대로 나오는 결과를 가진다.


​	기본적으로 자바는 callstack 개념으로 운용되는데,
​	callstack은 stack 의 LFIO 구조, 즉 맨 마지막 호출된 함수가 맨 먼저 반환되는 형식을 가진다.

​	<img src="Z:\003 개인업무\01 2022년 개인업무\22년_채승언\스터디\Javascript\사진자료\1.png" alt="1" style="zoom:50%;" />

<img src="Z:\003 개인업무\01 2022년 개인업무\22년_채승언\스터디\Javascript\사진자료\2.png" alt="2" style="zoom:50%;" />

<img src="Z:\003 개인업무\01 2022년 개인업무\22년_채승언\스터디\Javascript\사진자료\3.png" alt="3" style="zoom:50%;" />



​	그림을 기준으로는 hi가 먼저 stack에 올라오고,

​	hi가 반환되고, Hello가 쌓이고 반환되고. ...  순서대로 나간다.







**비동기 (asynchronous) 는 어떤 요청에 대해,** 이 요청이 끝날 때까지 기다리는 것이 아닌,
응답에 관계없이 바로 다음 동작이 실행되는 방식이다.



이는, 기본적으로 자바스크립트 엔진 자체로는 무리가 있지만, 브라우저 환경에서

 DOM, AJAX를 통한 제어에 있어, 이벤트 루프, 이벤트 큐 등으로 비동기 제어가 가능하다. 



**비동기 제어**

- callstack에 함수 호출시, callstack에 먼저 등록한다, callstack은 Web API(백그라운드)로 해당 함수를 보낸다.
- Web API는 비동기 함수 이벤트 발생시, 해당 콜백함수를 callback Queue에 Push한다.
- callstack이 비어있는지 이벤트루프(Event Loop)가 확인하게되고, 비어있을경우 callstack에 callback queue에 들어있는 함수를 넘겨준다.
- callstack에 들어온 함수는 실행되고, 사라진다.





간단히 말하면, callstack에 가는걸, web api에서 queue로 보내두고, callstack이 비어있는지 이벤트루프가 검사해
비어있다 싶으면 queue에서 꺼내와서 callstack에서 처리시킨다. 



비동기 처리(동작제어)는 ES5까지는 주로, Callback(함수에 맞물리는 함수[순서제어가능]) 혹은 Ajax메서드를 통해 

처리하였다.





## Promise

JS에서 비동기 동작을 다루기위한 방법, ES6 (ES2015) 에서 추가되었다.



``````js
function delay(sec, callback){
    setTimeout(() => {
    callback(new Date().toISOString());    
        
    }, sec * 1000);
}

delay(1,(result => {
    console.log(1, result);
})
``````

본 함수의 경우,  delay 함수에서의 인자 값은 실행에 필요한 옵션과 실행이

끝났을때 결과값을 반환하기 위한 콜백을 가지고 있다.

그러니까 ,delay의 인자 두개는 각각, setTimeOut과, setTimeout 이후 실행될

callback, 그러니까 delay의 result가 되는 것이다.





반면 이 코드를 보자 .



```js
function delayP(sec){
    return new Promise((resolve,reject) =>{
        setTimeout(()=>{
            
        resolve(new Date().toISOString));
    }, sec* 1000);
    });
}

delayP(1).then((result)=>{
    console.log(1,result);
})
```



delayP 함수는 실행에 필요한 옵션을 파라미터로 넘기고, .then()을 통해  받은 결과를 콜백에 넘긴다.



여기서, then은 promise의 객체이고, promise를 사용할 경우 반드시

delayP().then((result) => {} 화 하여야 한다.



return new Promise(); 는 promise의 인스턴스를 생성하고 리턴하며, 

promise는 (resolve, reject) 두가지의 인자를 가진다.



resolve - 해당 블록의 함수가 끝날때, 다음 일을 호출하라는 의미이다.
resolve - then 이라고, resolve를 통해, 결과값을 넘기게 되면,  then()에서 받은 그 함수에게 넘겨주게 됨
즉, callback() 함수의 역할과 똑같이 쓸 수 있음.



즉, then이  resolve 후에 실행됨.



reject - 해당 블록에서, 오류 및 예외처리 발생시 호출하는 것을 말한다.

​		Promise.reject(reason)  로 구성되며, reason 은,  거부 이유
​		즉, 어떤 이유로부터 거부된 Promise 객체를 말함.





```js
var i =  0;

function goCircle(){

return new Promise((resolve,reject) =>{

resolve(i=3, console.log("hi"));
})
}


goCircle().then(()=>{
i=4;
console.log("why");
console.log(i);
})
```

여기서 도 마찬가지로, resolve 내에서, i값이 먼저 변동되었고,

goCircle이라는 함수가 끝나고 i=4 를 대입하므로 실제로 나오는 것은 i = 4 이다.

즉, hi, why, 4 순서로 표시된다.





쉽게 생각하면 Promise는 약속,
즉, 다른 함수를 실행할 것을 약속하다. 
이것은 resolve 내, 명령이 처리되었을 경우,  then(그 다음에) 처리하라는 것이다.

파기되었을 경우 reject를 실행하고 then을 실행하지 않는다.







참고로, 인자의 resolve, reject는  함수로써 정의될 수 있다.

Promise.new(resolve,reject) 는 function resolve(){}처럼.





Promise의 단계를 살펴보자





![4](Z:\003 개인업무\01 2022년 개인업무\22년_채승언\스터디\Javascript\사진자료\4.png)





* pending : 대기, 처리가 완료되지 않음

* fulfilled : 이행, 성공적으로 처리가 완료된 상태

* rejected : 거부, 처리가 실패로 끝남.



즉, fulfiled = then으로 넘어가고 Async 액션을 취하거나 promise로 반환하는 Resolve 라면

rejected는 then, catch 로 넘어가고,  핸들링 처리, 혹은 promise로 반환하는 reject가 된다.



둘다 최종적으로 then, catch.







```js
var i = 0;


imbad = () => {

return new Promise((resolve,reject) => {


if(i==1){
resolve(console.log("i가 1이네요"));
}

else if(i==0) {
reject(console.log("아쉽네요")); //reject 호출은 catch로 잡힘.
}

})
}


function rejected(){
console.log("i가 0이였네요"); // reject시, 보여줄 로그 따로 저장
}



imbad().then( (res)=>{

console.log("이제 뭐할까요?"); //  프라미스 resolve 처리

}).catch((err) => {rejected();}); // 프라미스 Reject 처리
```



Rejected 까지 반영한 예시, i가 0이면 reject를 반환하고, 1이면 resolve 를 반환한다.



또한 promise -> then 의 처리를 보증하므로**, 체인 형태**로 엮어서 쓸수 있다.

가령 예를 들어, promise에서 i가5면, then처리에서 5를 더하고, then에서 다른 덧셈함수를

리턴하여 +5 시키는 방식으로 쓸 수 있다. (콜백 지옥 탈출가능)



또한 rejected 상태를 Catch문으로 묶거나, 혹은 종류별로 Catch에서 다시  인자를 넣어 함수를 실행해

메시지를 실행해주는 방식으로도 활용가능하다.

````js
var i = 2; 

catch ((err) => { rejected(i)})

function rejected(err_code){

if(err_code == 1){console.log("1번에러")}
if(err_code == 2){console.log("2번에러")}

}
````





## Async, Await



Promise 다음으로 추가된 비동기 처리 패턴으로

ES8 (ES2017) 에서 추가되었다.

목적은 기존 비동기 처리방식인 Callback, Promise의 단점을 보완하고 
가독성 있는 코드 작성을 도와주기 위해 사용한다.





함수 앞에 async 예약어를 붙인다. 
그리고, 함수 내부 로직중, HTTP 통신등의 동기처리로 인해 값이 바뀌는 코드 앞에
await를 붙인다. 

- 비동기 처리 메서드가 promise 객체를 반환해야 함
- 일반적으로 await 대상이 되는 비동기 처리 코드는 Axios등 promise 반환하는 API 호출 함수
- promise 가 아닌 값을 리턴하더라도 resolved promise가 반환됨







Async, Await에서의 예외 처리는 Try Catch를 통해 가능하다.





``` js
async function greet() {

return 'hello';

}

console.log(greet());
```

위 예제를 통해 async의 특징을 알 수 있다.

async가 적용된 함수는, 단순히 return을 반환하는 것이 아닌 promise 객체를 반환한다.



![5](Z:\003 개인업무\01 2022년 개인업무\22년_채승언\스터디\Javascript\사진자료\5.PNG)

promisestate가 fufiled 인데, 이는 성공이 반환되었고, 그 promise로 인해 나온 result가 hello라는 뜻이다.

(async가 아닌 일반 함수면 hello를 바로 출력한다.)



물론 promise 객체가 반환되었기 때문에 return을, Promise.resolve('hello'); 로 써서

promise 문법으로 사용할 수도 있다.

```js
async function greet() {

return Promise.resolve('hello');

}

greet().then(console.log);
```

이는 hello를 반환한다.



즉, **function 앞에 async를 선언한 함수는 언제나 promise 객체를 반환한다.**

**promise가 아닌 값이더라도 promise로 포팅하여 리턴한다.**







await는 async로 선언된 함수에서만 사용이 가능하고,  

먼저 다른 값을 받아올 수 있을법한  코드줄 앞에 추가한다 예를들어







function goExample(d){

var d = http통신("get.user");

return d;

}





function example(){}



var result = goExample(d);

console.log(d);

}



라고 정의 할 때,  값은, goExample이 먼저 실행되어, null 이 나오기 마련이지만



async function() {



var result = await goExample(d);

console.log(d);

}

처럼 바꾸면,  goExample() 함수가 promise 객체를 반환할때(fulfilled 반환할 때) 

실행이 되므로, 순서제어가 가능하다. (인자가 들어온다.) 



