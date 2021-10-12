---
layout: post
title: Markdown 문법 정리
subtitle: 
categories: markdown
tags: [test]
---

Markdown 기본 문법을 정리하였다. 출처는 `http://markdowntutorial.com`이다.  
이 게시물도 Markdown 언어로 작성하고 있는데, 앞으로 자주 쓰게 될 것 같다.

### 1. italic: _  
   예: `_italic_`

### 2. bold: **  
   예: `**bold**`

### 3. Headers: `#`
![Header](https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/markdown3.png?raw=true)

Headers one과 six는 sparingly하게 사용하는 것이 좋다고 한다.

### 4. Links
 1. inline link: `[]()`
    ![Header](https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/markdown_4-1.png?raw=true)
 2. reference link
    >advantage of the reference link style is that multiple links to the same place only need to be updated once
    ![Header](https://github.com/gitul0515/gitul0515.github.io/blob/main/_posts/image/markdown_4-2.png?raw=true)




~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
