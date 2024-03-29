---
layout: post
title: "검색어 자동 완성 구현하기"
date: 2022-03-26 14:12:52 +0900
categories: 자바스크립트
tags: [자바스크립트, 자료구조&알고리즘, 데브코스]
---

구글이나 네이버 등에서 '단어를 자동으로 완성하는 검색 폼'을 보았을 것이다. 아래 이미지처럼 말이다.  

<img src="https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0326_1.png?raw=true" style="width: 80%" alt="네이버 검색 폼">

본 포스팅에서는 바닐라 자바스크립트를 사용하여 이를 구현해보겠다.   

### 1. 트라이(Trie)
우선 필요한 것은 트라이(Trie)라는 자료구조다. 트라이는 탐색 트리의 일종으로, 문자열을 저장하고 효율적으로 탐색하기 위한 자료구조다.  

<img src="https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0326_3.png?raw=true" style="background-color: #fff; width: 80%" alt="트라이 이미지">


<u>루트는 비어 있고, 밑으로 내려갈수록 문자열이 하나씩 더해지며 저장된다.</u> 각 노드는 현재까지의 문자열을 값으로 갖는다. 또한 추가될 문자를 키로 가지며, 이 키가 자식 노드를 가리키고 있다.  
위 이미지에서 주황색으로 색칠한 노드의 값 'cat', 'bow', 'bag'이 입력된 문자열들이다.  

트라이는 문자열 탐색에 있어 매우 효율적이다. 제일 긴 문자열의 길이를 L이라고 할 때, <u>삽입 및 탐색의 시간복잡도는 O(L)이다.</u> 단순하게 하나씩 문자들을 비교하면서 탐색하는 것보다 훨씬 빠르다. 이에 검색어 자동완성, 사전 찾기, 문자열 검사 등에서 사용되고 있다.  

이제 트라이를 구현해보도록 하자.  

```Javascript
class TrieNode {
  constructor(value = "") {
    this.value = value;
    this.children = new Map();
    this.isWord = false; // 예) 'star'를 삽입했다면, 'star'는 단어지만 'st'는 단어가 아니다.
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  insert(string) {
    let curNode = this.root;
    for (const char of string) {
      if (curNode.children.has(char) === false) {
        curNode.children.set(
          char,
          new TrieNode(curNode.value + char)
        );
      }
      curNode = curNode.children.get(char);
    }
    curNode.isWord = true; // 현재 노드의 값은 '단어'임을 체크한다.
  }
}
```
insert 메서드를 살펴보자. string을 전달받으면, 한 char씩 순회하면서 트라이 노드의 value에 저장해나간다. 또한, Map 객체를 사용하여 노드를 연결하였다. 추가될 문자가 key이며, 자식 노드가 value이다. 이 자식 노드는 curNode.value + char를 값으로 갖는다.  

마지막으로 isWord라는 프로퍼티를 통해, 현재 노드의 값이 삽입된 '단어'임을 체크하였다. 예를 들어, 'star'를 삽입했다면 'star'는 단어이므로 true이고 's', 'st', 'sta'는 단어가 아니므로 false이다.  

### 2. 자동 완성
이제 단어를 자동으로 완성하는 autoComplete 메서드를 구현할 것이다. 하지만 그 전에 필요한 것은 자료구조 큐(Queue)다. "갑자기 왠 큐?"라는 생각이 들었을지도 모른다. 여기서 큐는 '레벨 순회'를 구현하기 위해 필요하다. 레벨 순회는 레벨이 낮은 순서대로 노드를 순회하는 것이다.  
<u>이를 활용하여 단어의 개수가 적은 것부터 탐색할 것이다.</u>  

```Javascript
// 큐(Queue) 자료구조
class Queue {
  constructor() {
    this.queue = [];
    this.front = 0;
    this.rear = 0;
  }

  get size() {
    return this.rear - this.front;
  }

  enqueue(value) {
    this.queue[this.rear++] = value;
  }

  dequeue() {
    const value = this.queue[this.front];
    delete this.queue[this.front];
    this.front++;
    return value;
  }

  isEmpty() {
    return this.size === 0;
  }
}
```
큐의 구현은 어렵지 않으므로 설명을 생략하겠다. 이를 기반으로 Trie의 autoComplete 메서드를 구현해보자.  
```Javascript
  // 단어 자동 완성 메서드
  autoComplete(string) {
    const result = [];
    let curNode = this.root;

    for (const char of string) {
      if (curNode.children.has(char) === false) {
        return '자동 완성할 단어가 없습니다.';
      }
      curNode = curNode.children.get(char);
    }

    // 레벨 순회하면서 단어들을 찾는다.
    const queue = new Queue();
    queue.enqueue(curNode);
    while(!queue.isEmpty()) {
      curNode = queue.dequeue();
      // 자식 노드가 존재하면, 자식 노드를 큐에 삽입한다. 
      if (curNode.children.size > 0) {
        for (const node of curNode.children.values()) {
          queue.enqueue(node);
        }
      } 
      // 해당 노드가 '단어'라면 값을 저장한다. 
      if (curNode.isWord === true) {
        result.push(curNode.value);
      }
    }
    return result;
  }
```
우선 입력받은 string이 있는 노드를 탐색한다. 만일 노드가 없다면, 자동 완성할 단어가 없는 것이므로 따로 처리해준다.  

string이 있는 노드로부터 자식 노드의 레벨 순회를 시작한다. 큐에서 노드를 꺼낸 후, 이 노드의 자식 노드가 존재하면 모든 자식 노드를 큐에 삽입한다. 그리고 꺼낸 노드가 '단어'라면, 다시 말해 isWord === true이면 값을 result 배열에 저장한다.  

반복문이 종료되면, 자동 완성된 단어들을 담은 result 배열을 출력한다.  

결과는 다음과 같다.  

```Javascript
// 초기화
const trie = new Trie();

// 단어 삽입
trie.insert("cat");
trie.insert("can");
trie.insert("canon");
trie.insert("bat");
trie.insert("bag");
trie.insert("baggage");
trie.insert("bow");
trie.insert("bowl");

// 자동 완성 단어 출력
console.log(trie.autoComplete('c')); // [ 'cat', 'can', 'canon' ]
console.log(trie.autoComplete('b')); // [ 'bat', 'bag', 'bow', 'bowl', 'baggage' ]
console.log(trie.autoComplete('bo')); // [ 'bow', 'bowl' ]
console.log(trie.autoComplete('ba')); // [ 'bat', 'bag', 'baggage' ]
console.log(trie.autoComplete('z')); // 자동 완성할 단어가 없습니다.
```
자동 완성된 단어들이 잘 출력되었다!  
레벨 순회 덕분에 단어의 길이가 짧은 것부터 출력되었음을 확인할 수 있다.  

### 3. UI 구현
멋진 기능을 만들었다면 사용해보아야 하지 않겠는가!  
위 기능을 적용하여 간단한 검색 필드를 구현하였다.  

<video width="70%" height="70%" controls="controls">
  <source src="https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/22_0326_4.mp4?raw=true" type="video/mp4">
</video>

검색 필드에 'input' 이벤트가 발생할 때마다, value 값을 autoComplete에 전달하여 호출하였다. 그 결과 값을 검색 필드에 5개까지 표시하였다. 한편, 자동완성된 단어를 클릭하면 value 값이 변하도록 설정하였다. 

```Javascript
import { trie } from "./trie.js";

[
  'string', 'str', 'stress', 'star', 'singer', 'sign', 'starcraft', 'starwars', 'starbucks', 'southkorea', 'south korea'
]
.forEach(word => {
  trie.insert(word);
});

const searchField = document.querySelector('.input');
const wordWrapper = document.querySelector('.word-wrapper');
const wordList = document.querySelector('.word-list');

searchField.addEventListener('input', e => {
  const { value } = e.target;
  wordList.innerHTML = '';

  if (value === '') {
    wordWrapper.classList.remove('show');
  } else {
    wordWrapper.classList.add('show');
    const words = trie.autoComplete(value).slice(0, 5);
    words.forEach(word => {
      wordList.innerHTML += `<li class="word-item">${word}</li>`;
    });
  }
});

wordList.addEventListener('click', e => {
  searchField.value = e.target.textContent;
  wordWrapper.classList.remove('show');
})
```

### 4. 마치며
트라이 자료구조를 사용하는 것이 조금 어려웠지만, 그래도 의도했던 기능을 잘 구현할 수 있어 뿌듯하다. 트리, 트라이, 큐 등 자료구조의 중요성을 다시 한 번 깨닫게 되었다.  

한편, 이번에 구현한 기능을 향후 프로젝트에서 활용할 수 있을 것이다. 검색 필드에 이 기능을 녹여낸다면 더 좋은 사용자 경험을 줄 수 있을 것이라고 기대한다.  

