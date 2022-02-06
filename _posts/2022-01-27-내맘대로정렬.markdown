---
layout: post
title: "localeCompare에 관하여"
date: 2022-01-27 12:12:52 +0900
categories: 자바스크립트
tags: [자바스크립트]
---

자바스크립트를 공부할수록 유용하고 편리한 내장 메서드들이 많다는 것을 느낀다. 그 중에서 localeCompare라는 상당히 재미있는(?) 녀석이 있는데, 이번 글에서는 이 녀석에 관한 이야기를 해보고 싶다. 또한, 이 localeCompare을 기존의 sort 메서드에 활용하는 방안을 서술해 보겠다.  

### localeCompare 소개

먼저 MDN 사이트를 참고하여 localeCompare을 간략히 소개해보겠다.  
localeCompare()는 String 메서드다. 기준 문자열과 비교했을 때, 비교되는 문자열이 정렬상 앞에 오는지, 뒤에 오는지, 혹은 같은 순서인지 알려주는 숫자를 리턴한다. 다음과 같이 사용한다.  

<span style="font-size: 1.1rem">referenceStr.localeCompare(compareString[, locales[, options]])</span>

1. referenceStr이 compareString 보다 정렬상 앞에 오면 **<u>음수</u>**를 반환한다.  
2. referenceStr이 compareString 보다 정렬상 뒤에 오면 **<u>양수</u>**를 반환한다.  
3. referenceStr이 compareString 보다 정렬상 같은 순서이면 **<u>0</u>**을 반환한다.  

예제를 통해서 더 쉽게 이해해보자.

```Javascript
'a'.localeCompare('b'); // -1 (또는 다른 음수)

'b'.localeCompare('a'); // 1 (또는 다른 양수)

'a'.localeCompare('a'); // 0
```

주석에서 볼 수 있듯이, 음수는 반드시 -1, 양수는 +1이 아니며 다른 숫자일 수도 있다. 이는 브라우저나 버전에 따라 달라질 수 있으므로 유의해야 한다.  

### sort에 활용
나는 이 localeCompare라는 메서드를 sort의 비교함수에 사용하면 유용할 것 같다는 생각이 들었다. (아니다 다를까, MDN 사이트에서도 그렇게 사용할 것을 권장하고 있었다) 특히, String 배열을 정렬할 때 사용할 수 있는데, 예를 들면 다음과 같다.

```Javascript
const array = ['banana', 'apple', 'orange'];

// 오름차순 정렬
array.sort((a, b) => a.localeCompare(b)); // ['apple', 'banana', 'orange']

// 내림차순 정렬
array.sort((a, b) => b.localeCompare(a)); // ['orange', 'banana', 'apple']
```

다만, String 배열은 이렇게 비교함수를 전달하지 않아도 정렬이 가능하다. 다음과 같이 더 간단하게 말이다.

```Javascript
const array = ['banana', 'apple', 'orange'];

// 오름차순 정렬
array.sort(); // ['apple', 'banana', 'orange']

// 내림차순 정렬
array.sort().reverse(); // ['orange', 'banana', 'apple']
```

"그럼 localeCompare을 사용하는 것은 불필요한 일 아니야?"라고 반문할지도 모른다. 하지만 그렇다고 생각하지 않는다. **<u>String 배열을 특수한 기준에 따라 정렬해야 하는 경우</u>**도 있을 수 있기 때문이다. 예를 들어, 각 문자열의 세 번째 문자를 기준으로 정렬해야 한다고 생각해 보자. 이 때는 localeCompare과 같은 추가적인 메서드가 필요하다. 다음 코드를 보자.

```Javascript
const array = ['banana', 'apple', 'orange'];
array.sort((a, b) => a[2] - b[2]); // ['banana', 'apple', 'orange'] 오답
```

배열이 제대로 정렬되지 않는다. a[2]와 b[2]는 문자열이기 때문에 NaN이 리턴되기 때문이다. 이런 경우 문자열의 전후를 비교할 수 있는 localeCompare이 사용될 수 있다. 

```Javascript
const array = ['banana', 'apple', 'orange'];
array.sort((a ,b) => a[2].localeCompare(b[2])); // ['orange', 'banana', 'apple'] 정답
```

한편, charCodeAt을 사용해서 정렬하는 것도 가능하다. charCodeAt은 인덱스에 해당하는 문자의 unicode 값을 리턴하는 메서드다.

```Javascript
const array = ['banana', 'apple', 'orange'];
array.sort((a ,b) => a.charCodeAt(2) - b.charCodeAt(2)); // ['orange', 'banana', 'apple'] 정답
```

둘 중 무엇을 사용해도 상관없지만, 개인적으로 localeCompare이 더 깔끔해보이기는 하다. 그리고 정렬에 더 특화된 메서드라는 생각도 든다. 아무튼, **<u>String 배열을 디테일하게 정렬하고 싶다면</u>**, 두 메서드를 사용할 것을 추천한다.  

브라우저 지원은 다음과 같다. (이미지 클릭 시 caniuse 사이트로 이동)  

1) localeCompare  
<a href="https://caniuse.com/?search=localecompare" target="_blank">
  <img src="https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0127_1.png?raw=true" alt="localeCompare 브라우저 지원 이미지">
</a>

2) charCodeAt  
<a href="https://caniuse.com/?search=charCodeAt" target="_blank">
  <img src="https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0127_1.png?raw=true" alt="localeCompare 브라우저 지원 이미지">
</a>

### 마치며
하나의 문제를 해결할 수 있는 도구들이 여럿 존재한다. 각각의 도구들을 손에 익힌 뒤에, 상황에 맞게 적절히 활용하는 것이야말로 좋은 개발이라고 믿는다.  

### 참고자료
localeCompare - MDN Web Docs  
sort - MDN Web Docs  
caniuse.com  

(제 글에서 틀린 것이 있다면 꼭 지적해 주세요. 감사히 배우겠습니다)  