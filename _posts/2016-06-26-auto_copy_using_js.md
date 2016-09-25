---
layout: post
title: "자바스크립트를 이용한 자동복사하기"
date: 2016-06-26 22:01:30 +0900
categories: [languages]
tags: [javascript]
---
종종 어떤 사이트에 접속해보면 버튼을 누르면 클립보드에 그 내용이 복사되는 경우가 있다.

상당히 편리한 기능인데, 아쉽게도 이 기능은 브라우저에 따라 지원이 되기도 하고 되지 않기도 한다.

이 기능은 `JavaScript`를 이용하여 구현하는데 소스는 아래와 같다.

{% highlight javascript %}
function copy(targetId) {
   var elem = document.getElementById(targetId);
   var isInput = elem.tagName === "INPUT" || elem.tagName === "TEXTAREA";
   var origSelectionStart, origSelectionEnd;
   if (isInput) {
       target = elem;
       origSelectionStart = elem.selectionStart;
       origSelectionEnd = elem.selectionEnd;
   } else {
       target = document.getElementById(targetId);
       if (!target) {
           var target = document.createElement("textarea");
           target.style.position = "absolute";
           target.style.left = "-9999px";
           target.style.top = "0";
           target.id = targetId;
           document.body.appendChild(target);
       }
       target.textContent = elem.textContent;
   }
   var currentFocus = document.activeElement;
   target.focus();
   target.setSelectionRange(0, target.value.length);

   var succeed;
   try {
       succeed = document.execCommand("copy");
   } catch(e) {
       succeed = false;
   }
   if(isIE()&&!succeed) succeed = window.clipboardData.setData('TEXT', target.value);
   if (currentFocus && typeof currentFocus.focus === "function") {
       currentFocus.focus();
   }

   if (isInput) {
       elem.setSelectionRange(origSelectionStart, origSelectionEnd);
   } else {
       target.textContent = "";
   }
   return succeed;
}
{% endhighlight %}

위의 function을 그대로 실행하면 에러가 발생할 것이다.

그래서 아래의 function을 추가하여 같이 선언해야 한다.

{% highlight javascript %}
function isIE() {
	return document.all ? true : false;
}
{% endhighlight %}

추가된 위 function은 IE를 체크하는 것이다.

IE는 처음에 copy function에서 사용하는 Document.execCommand()를 지원하지 않기 때문에

IE는 별도로 window Object에 있는 clipboardData의 setData() function을 이용하여야 된다.

위 Function을 지원하는 Browser는 아래와 같다.

![일반 Desktop용 Browser](/files/js_auto_copy_2.png)

![Mobile용 Browser](/files/js_auto_copy_2.png)


위 표를 보면 알겠지만  Safari는 모두 지원하지 않는다.

Safari에서 사용하려면 OS의 ClipBoard를 이용하여야 한다.

위의 표에서 언급된 숫자는 버전이며 그 버전보다 높으면 위 function을 이용하여

자동복사를 사용할 수 있다.
