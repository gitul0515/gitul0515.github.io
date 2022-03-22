---
layout: post
title: "Proxify Type을 사용해 변수 캡슐화하기"
date: 2022-03-15 12:12:52 +0900
categories: 자바스크립트
tags: [자바스크립트, 타입스크립트]
---

### 맵드 타입 
타입스크립트 공식 문서에서 **맵드 타입(Mapped Type)** 글을 읽어보던 중, 재미있는 예제를 발견해서 글을 써본다. 참고로, 맵드 타입이란 **기존에 정의되어 있는 타입을 새로운 타입으로 변환**해 주는 문법이다. 마치 자바스크립트의 map() 메서드를 타입에 적용한 것과 유사하다. 다음은 맵드 타입을 활용하여, 기존 타입을 readonly를 적용한 타입으로 바꾸는 간단한 코드다. 

```Typescript
  type ReadOnly<T> = {
    readonly [P in keyof T]: T[P];
  };
```

### Proxify Type
본론으로 돌아와서, 타입스크립트 공식 문서에서 본 예제는 Proxify Type라는 녀석이다. 
기본형 변수 타입을, **<U>변수로의 접근(get)과 갱신(set)을 전담하는 메소드의 타입으로 변환</U>**하는 것이다. 
```Typescript
  type Proxy<T> = {
    get(): T;
    set(value: T): void;
  }

  type Proxify<T> = {
    [P in keyof T]: Proxy<T[P]>;
  }
```
Proxify Type을 예제를 통해 알아보자. 
```Typescript
  type Person = {
    name: string;
  };

  const person:Person = {
    name: 'Billy',
  };
```
person['name']의 타입은 string이다. 이는 기본형 변수 타입이며, 캡슐화가 되지 않은 상태이다. 이 변수로의 접근과 갱신을 독점하는 메소드를 만들 필요가 있다(캡슐화). **이 때 Proxify Type이 사용될 수 있다**. `Proxify<Person>`, 바로 이렇게 말이다.  

Proxify의 제네릭 T로 Person 타입을 넘겨주었다. 그렇다면...  
* key에 저장할 값  
  Person 타입 프로퍼티의 key인 'name'
* value에 저장할 값  
`Proxy<T[P]>`라고 되어 있다. 이는 `Proxy[string]`과 동일하다.  
  따라서 **get(): string과 set(value: string)을 메서드로 갖는 객체**를 저장해야 한다.  

  이를 반영하여, `Proxify<Person>`을 적절히 구현해보았다. 

```Typescript
  const person:Proxify<Person> = {
    name: {
      get(): string {
        return 'Billy';
      },
      set(value: string) {
        this.get = () => value;
      }
    }
  }
  console.log(person.name.get()); // 'Billy'
  person.name.set('James');
  console.log(person.name.get()); // 'James'  
```
person.name 변수로의 접근과 갱신이 잘 이루어진다. 이처럼 Proxify Type을 활용하면 변수 캡슐화를 간단하게 완성할 수 있다.  

(학습 중 작성한 글이며, 내용이 잘못되었을 수 있습니다. 참고만 하시기를 권장드립니다.)

### 참고자료
https://www.typescriptlang.org/docs/handbook/advanced-types.html
