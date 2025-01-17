---
layout  : wiki
title   : 정규 표현식
summary : 작성중인 문서
date    : 2020-05-18 22:45:12 +0900
updated : 2020-06-29 23:26:32 +0900
tag     : regex
toc     : true
public  : true
parent  : [[index]]
latex   : true
---
* TOC
{:toc}

이 문서는 작성중입니다.

>
참고: 이 문서는 [[ag]] 명령을 사용합니다.

## 이스케이프

| Predefined Term     | Matches                                                                    |
|---------------------|----------------------------------------------------------------------------|
| `\t`                | 탭 (좌우 수평 방향)                                                        |
| `\b`                | 백 스페이스                                                                |
| `\v`                | 탭 (위아래 수직 방향)                                                      |
| `\f`                | 폼피드 (Form feed ; 다음 페이지)                                           |
| `\r`                | 개행 문자 (Carriage return)                                                |
| `\n`                | 줄바꿈 문자 (Newline)                                                      |
| `\cA` : `\cZ`       | 제어문자 (Control characters)                                              |
| `\x0000` : `\xFFFF` | 유니코드 16진수 (Unicode hexadecimal)                                      |
| `\x00` : `\xFF`     | 아스키 16진수 (ASCII hexadecimal)                                          |
| `\d`                | 0-9 사이의 10진수 숫자. `[0-9]` 와 같다.                                   |
| `\D`                | `\d` 의 여집합. `[^0-9]` 와 같다.                                          |
| `\w`                | `_` 를 포함한 모든 영어 알파벳과 숫자. `[A-Za-z0-9_]` 와 같다.             |
| `\W`                | `\w` 의 여집합. `[^A-Za-z0-9_]` 와 같다.                                   |
| `\s`                | 공백문자(스페이스, 탭, 폼피드 등). `[\t\n\f\r]`과 같다.                    |
| `\S`                | `\s` 의 여집합. 공백문제를 제외한 문자. `[^\t\n\f\r]`과 같다.              |
| `\b`                | 단어의 경계를 나타내는 문자. (A word boundary)                             |
| `\B`                | 단어 내에서 문자의 경계가 아닌 문자. (Not a word boundary (inside a word)) |

## 앵커(anchor)

| `^`  | 행 시작 지점       |
| `$`  | 행 끝나는 지점     |
| `\A` | 문자열 시작 지점   |
| `\Z` | 문자열 끝나는 지점 |
| `\b` | 단어의 경계 지점   |
| `\B` | `\b`가 아닌 지점   |

다음과 같은 내용을 가진 `hello.c` 라는 파일이 있다고 하자.

```c
#include <stdio.h>
main()
{
    printf("hello, world\n");
}
```

`\A`로 검색해보면 1번 라인이 출력된다. 즉, `\A`는 파일의(문자열의) 시작 지점에 매치되는 앵커이다.

```sh
$ ag '\A' hello.c
1:#include <stdio.h>
```

`\Z`는 파일의(문자열의) 끝 지점에 매치되는 앵커이다.

```sh
$ ag '\Z' hello.c
5:}
```

`\b`는 단어의 경계 지점에 매치된다.

![]( /resource/wiki/regex/b.jpg )

반대로 `\B`는 단어의 경계가 아닌 곳에 매치된다.

![]( /resource/wiki/regex/Bl.jpg )


## 수량자

- `*` - 패턴을 0회 이상 반복
- `?` - 패턴을 1회 이상 반복
- `+` - 0 ~ 1회

### 범위 수량자

- `{n}`: n 회 반복
- `{n,m}`: n ~ m 회 반복
- `{n,}`: n 회 이상 반복

범위 수량자로 `*`, `?`, `+`를 모두 대체할 수 있다.

## 욕심 수량자와 겸허 수량자

| 욕심(Greedy) | 겸허(Lazy, Non-greedy) |
|--------------|------------------------|
| `*`          | `*?`                   |
| `+`          | `+?`                   |
| `?`          | `??`                   |
| `{n,m}`      | `{n,m}?`               |

## 캡처 그룹

## 역참조

Backreferences

## 전방 탐색

- lookahead, positive lookahead 라고 부른다.

$$ (?= \text{pattern} ) $$

전방 탐색은 주어진 패턴보다 뒤에(왼쪽에) 있는 문자의 일치를 판별한다.

예를 들어 $$ b(?=c) $$ 와 같은 정규식이 있다면 `c` 왼쪽에 있는 `b`에 매치된다.

- `b(?=c)`
    - $$ a \color{red}b cdbe $$ &nbsp;
        - `echo abcdbe | ag 'b(?=c)'` (잘 모르겠다면 이 명령을 복사해서 터미널에서 실행해보자.)
    - $$ bb \color{red}b c $$ &nbsp;
        - `echo bbbc | ag 'b(?=c)'`
- `if(?=\()`
    - $$ \color{red}{if} ( $$ &nbsp;
        - `echo 'if(' | ag 'if(?=\()'`


## 부정 전방 탐색

$$ (?! \text{pattern}) $$

- negative lookahead 라고 부른다.

부정 전방 탐색은 전방 탐색의 반대이다.

- `2(?!3)` - 오른쪽에 `3`이 오지 않는 `2`.
    - $$ 123 \color{red}2 4 $$ &nbsp;
        - `echo 12324 | ag '2(?!3)'`
    - $$ \color{red}{222} 23 $$ &nbsp;
        - `echo 22223 | ag '2(?!3)'`

- `(?!1234)\d{4}` - `1234`가 아닌 숫자 4개.
    - $$ 1234 $$ &nbsp;
        - `echo 1234 | ag '(?!1234)\d{4}'`
    - $$ \color{red}{1235} $$ &nbsp;
        - `echo 1235 | ag '(?!1234)\d{4}'`

### 숫자 3자리마다 콤마를 삽입하기

- `(?<=\d)(?=(\d{3})+($|\D))`
    - 왼쪽의 숫자와 오른쪽의 3개 숫자 사이의 공간.
    - 이 정규식을 사용하면 3개 숫자마다 콤마를 삽입하는 코드를 쉽게 만들 수 있다.

```js
var regex = /(?<=\d)(?=(\d{3})+($|\D))/g

'1234567'.replace(regex, ',');      // '1,234,567'
'12345678'.replace(regex, ',');     // '12,345,678'
'123456789'.replace(regex, ',');    // '123,456,789'
'1234567890'.replace(regex, ',');   // '1,234,567,890'
```

- `\B(?=(\d{3})+(?!\d))`

```js
var regex = /\B(?=(\d{3})+(?!\d))/g

'1234567'.replace(regex, ',');      // '1,234,567'
'12345678'.replace(regex, ',');     // '12,345,678'
'123456789'.replace(regex, ',');    // '123,456,789'
'1234567890'.replace(regex, ',');   // '1,234,567,890'
```

### 완전 일치의 반대

다음 패턴의 부정 전방 탐색을 사용하면 완전 일치의 반대 패턴이 된다. 필요에 따라 `pattern` 부분만 바꿔주면 된다.

`^(?!pattern$j).*$`

## 후방 탐색

$$ (?<= pattern) $$

후방 탐색은 주어진 패턴보다 앞에(오른쪽에) 있는 패턴의 일치를 판별한다.

- `(?<=3)abc`
    - $$ 123 \color{red}{abc} $$ &nbsp;
        - `echo 123abc | ag '(?<=3)abc'`
    - $$ 1abc3 \color{red}{abc} 5abc $$ &nbsp;
        - `echo 1abc3abc5abc | ag '(?<=3)abc'`


## 부정 후방 탐색

$$ (?<! pattern) $$


- `(?<!2)abc`
    - $$ 1 \color{red}{abc} 2abc 3 \color{red}{abc} $$ &nbsp;
        - `echo 1abc2abc3abc | ag '(?<!2)abc'`

참고 사항

- Javascript는 부정 후방 탐색은 지원하지 않는다.

## 참고 예제
### URI

- [[URI]]

### 한글

$$[ㄱ-ㅎ|ㅏ-ㅣ|가-힣]$$

이 정규식은 모든 한글에 매치된다.

## 유용한 도구 모음

- [REGEXPER]( https://regexper.com/ ): 입력한 정규식을 기차 레일 다이어그램으로 표현해준다. 정규식을 처음 공부하는 사람에게 추천한다.
- [regex2nfa]( https://cyberzhg.github.io/toolbox/regex2nfa ): 입력한 정규식을 NFA 그래프로 표현해준다.

## 함께 읽기

- [[rans-cmd]]
