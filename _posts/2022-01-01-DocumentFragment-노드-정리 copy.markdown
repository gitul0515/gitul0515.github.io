---
layout: post
title: "Document Fragment 노드 정리"
date: 2022-01-01 12:12:52 +0900
categories: 자바스크립트
tags: [자바스크립트]
---

DOM을 변경하는 것은 높은 비용이 드는 작업이므로 횟수를 줄이는 것이 유리하다.  
그러므로 여러 요소를 DOM에 추가할 때는 **Document Fragment 노드**를 사용하는 것이 좋다.  

## Document Fragment 노드
Document Fragment 노드는 별도의 서브 DOM을 구성하여 기존 DOM에 추가할 때 사용한다.  다음과 같은 주요 특징이 있다.  

1. 부모 노드가 없어서 **기존 DOM과는 별도로 존재**한다.  
   그러므로 Document Fragment에 자식 노드를 추가하여도  
   기존 DOM에는 어떠한 변경도 발생하지 않는다.  

2. Document Fragment 노드를 DOM에 추가하면 **자신은 삭제**되고  
   자신의 자식 노드만 DOM에 추가된다.  
  
<br>
![Document Fragment 노드](https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0103_1.png?raw=true)
<br>
<br>
Document Fragment 노드를 생성하는 메서드는 **createDocumentFragment**이다.  
다음의 코드를 보자.  

## 코드 실습
```HTML
<!DOCTYPE html>
<html lang="ko">
  <head>
  </head>
  <body>
    <ul id="number"></ul>
  </body>
  <script>
    const number = document.getElementById('number');

    // Document Fragment 노드 생성
    const fragment = document.createDocumentFragment();

    ['one', 'two', 'three'].forEach(text => {
      // li 요소 노드 생성
      const li = document.createElement('li');
      // 텍스트 노드 생성
      const textNode = document.createTextNode(text);

      // 텍스트 노드를 li 요소 노드의 자식으로 추가
      li.appendChild(textNode);

      // li 요소 노드를 Document Fragment 노드의 자식으로 추가
      fragment.appendChild(li);
    });

    // Document Fragment 노드를 number 요소 노드의 자식으로 추가
    number.appendChild(fragment);
  </script>
</html>
```
<br>
위 코드에서 DOM 변경이 발생하는 것은 한 번 뿐이다. 리플로우와 리페인트도 한 번만 실행된다. (Document Fragment 노드를 기존 DOM에 추가할 때)  
  
그러므로 여러 요소 노드를 DOM에 추가하려면  
Document Fragment 노드를 사용하는 것이 효율적이다.  

## 참고자료
이웅모, "모던 자바스크립트 Deep Dive", 위키북스, 2020, pp. 725-727