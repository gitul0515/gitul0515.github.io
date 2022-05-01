---
layout: post
title: '이벤트 핸들러의 누적 등록'
date: 2022-04-28 12:12:52 +0900
categories: 자바스크립트
tags: [자바스크립트]
---

자바스크립트의 addEventListener 메서드는  
<U>동일한 HTML 요소에서 발생한 동일한 이벤트에 대해</U>  
<U>하나 이상의 이벤트 핸들러를 등록할 수 있다.</U> 다음의 코드처럼 말이다.

### 사례 1  

```Html
<!DOCTYPE html>
<html lang="ko">
  <head></head>
  <body>
    <script>
      const $body = document.querySelector('body');

      $body.addEventListener('click', () => {
        console.log('이벤트1');
      });
      $body.addEventListener('click', () => {
        console.log('이벤트2');
      });
      $body.addEventListener('click', () => {
        console.log('이벤트3');
      });

      $body.dispatchEvent(new Event('click'));
    </script>
  </body>
</html>
```

<img style="margin-left: 0" src="https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0428_1.png?raw=true" alt="콘솔 결과 이미지">

### 사례 2

그런데, 다음의 경우라면 어떻게 될까?  
콜백함수가 이벤트 핸들러로 누적 등록될까 그렇지 않을까?

```HTML
<!DOCTYPE html>
<html lang="ko">
  <head></head>
  <body>
    <script>
      const $body = document.querySelector('body');

      $body.addEventListener('click', () => {
        console.log('이벤트1');
      });
      $body.addEventListener('click', () => {
        console.log('이벤트1');
      });
      $body.addEventListener('click', () => {
        console.log('이벤트1');
      });

      $body.dispatchEvent(new Event('click'));
    </script>
  </body>
</html>
```

정답은 "누적해서 등록된다"이다. 콘솔 결과는 아래와 같다.

<img style="margin-left: 0" src="https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0428_2.png?raw=true" alt="콘솔 결과 이미지">

왜 이런 현상이 발생할까? 그것은 각각의 addEventListener에 전달된 콜백 함수인  
`() => { console.log('이벤트1'); }`의 참조값이 다르기 때문이다.

다시 말해, <U>각각의 콜백 함수는 내용은 같으나 서로 다른 함수(객체)다</U>.  
그렇기 때문에 각각의 콜백 함수가 이벤트 핸들러로 누적 등록된다.

### 사례 3

반대로, <U>참조가 동일한 콜백함수를 전달하면 하나의 이벤트 핸들러만 등록된다</U>.  
아래의 코드처럼 말이다.

```HTML
<!DOCTYPE html>
<html lang="ko">
  <head></head>
  <body>
    <script>
      const $body = document.querySelector('body');

      const callBackFunc = () => {
        console.log('이벤트1');
      };

      $body.addEventListener('click', callBackFunc);
      $body.addEventListener('click', callBackFunc);
      $body.addEventListener('click', callBackFunc);

      $body.dispatchEvent(new Event('click'));
    </script>
  </body>
</html>
```

<img style="margin-left: 0" src="https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0428_3.png?raw=true" alt="콘솔 결과 이미지">

콜백함수의 참조값을 callBackFunc라는 변수에 할당한 뒤 사용하고 있다.  
따라서, 각각의 addEventListener에 전달된 콜백함수는 당연히 같은 함수다.  
그렇기에 하나의 이벤트 핸들러만이 등록된다.

addEventListener 메서드를 사용할 때는 이상의 문제에 주의할 필요가 있다.

### 참고자료

"모던 자바스크립트 Deep Dive", 이웅모, 위키북스, 2020.09.25, pp.764-765
