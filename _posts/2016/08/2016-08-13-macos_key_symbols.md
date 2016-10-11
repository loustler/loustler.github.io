---
layout: post
title: "macOS tip. Symbolic HTML"
date: 2016-08-13 00:23:00 +0900
categories: [os]
tags: [macos]
---

macOS 계열 컴퓨터가 가지는 독특한 Key Symbolic 이 있다.

이를테면 ⌘(command)이라든지 ⌃(control) 라든지..

이것을 HTML에서는 어떻게 표현하고 있을까?
<!--more-->
다음과 같이 입력하면 macOS의 key symbolic key를 표현할 수 있다.

```
HTML            Symbolic           Name

&#63743;              Apple

&#8984;         ⌘      Command, Cmd, Clover, (formerly) Apple

&#8963;         ⌃      Control, Ctl, Ctrl

&#8997;         ⌥      Option, Opt, (Windows) Alt

&#8679;         ⇧      Shift

&#8682;         ⇪      Caps lock

&#9167;         ⏏      Eject

&#8617;         ↩      Return, Carriage Return

&#8629; &crarr; ↵      Return, Carriage Return

&#9166;         ⏎      Return, Carriage Return

&#8996;         ⌤      Enter

&#9003;         ⌫      Delete, Backspace

&#8998;         ⌦      Forward Delete

&#9099;         ⎋      Escape, Esc

&#8594; &rarr;  →      Right arrow

&#8592; &larr;  ←      Left arrow

&#8593; &uarr;  ↑      Up arrow

&#8595; &darr;  ↓      Down arrow

&#8670;         ⇞      Page Up, PgUp

&#8671;         ⇟      Page Down, PgDn

&#8598;         ↖      Home

&#8600;         ↘      End

&#8999;         ⌧      Clear

&#8677;         ⇥      Tab, Tab Right, Horizontal Tab

&#8676;         ⇤      Shift Tab, Tab Left, Back-tab

&#9250;         ␢      Space, Blank

&#9251;         **␣**  Space, Blank
```

위와 같이 입력하면 Symbolic 과 같이 표현해준다.
