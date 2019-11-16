---
layout  : wiki
title   : 100층 건물과 2개의 계란 문제
summary : 
date    : 2019-03-13 21:07:50 +0900
updated : 2019-11-02 14:00:11 +0900
tag     : math
toc     : true
public  : true
parent  : problem
latex   : true
---
* TOC
{:toc}

## 문제 소개

* 구글, 마이크로소프트 개발자 면접에서 나왔다는 소문이 있는 유명한 문제이다.
* 그러나 그 소문이 진짜인지는 알 수 없다.

>
100층 짜리 건물에서 계란을 떨어뜨렸을 때 계란이 깨지지 않는 가장 낮은 층이 몇 층인지 알아내야 한다.
당신에게 계란이 딱 두 개 있다고 할 때, 계란을 몇 번이나 떨어뜨려야 정답을 알아낼 수 있을까?

**1층 창문에서 떨어뜨려도 계란은 깨질텐데 왜 이런 문제를 내죠?**

* 이런 생각을 할 수도 있겠지만 문제의 조건에서 언급하지 않은 것들이 많다는 것도 생각해야 한다.
* 가령 건물이 있는 곳이 달이거나, 계란이 떨어지는 바닥이 매우 특수한 재질로 되어 있을 수도 있다.
* 실험을 통해 새롭게 만들어낸 슈퍼 닭의 알을 실험하는 것일 수도 있다.


## 이진 탐색을 쓸 수 있을까?

이런 유형의 문제에 접근할 때 가장 떠올리기 쉬운 방법은 이진 탐색일 것이다.

$$\log_2 100 = 6.6438...$$이므로 이 방법을 사용하면 최대 7회 이내에 문제의 층 수를 찾아낼 수 있다.

하지만 이 문제는 계란 2개라는 제약이 있기 때문에 함부로 이진 탐색을 사용할 수가 없다.

문제를 아직 다 풀지도 못했는데 계란 두 개를 다 깨먹는 경우가 많기 때문이다.

(예를 들어 문제의 층이 19층이라면 이진 탐색의 첫번째/두번째 시도인 50층, 25층 테스트에서 계란을 다 깨버리게 되어 답을 알 수 없게 된다.)


## 계란을 아끼는 방법

이번에는 계란을 최대한 아끼는, 즉 가장 안전한 방법을 생각해 보자.

선형 탐색을 쓰면 될 것 같다.

1. $$n = 1$$ 로 설정한다.
2. $$n$$ 층으로 가서 계란을 하나 떨어뜨린다.
3. 계란이 깨졌다면 $$n-1$$ 층이 계란이 깨지지 않는 가장 낮은 층이다!
4. 계란이 안 깨졌다면 $$n$$ 에 1을 더한다.
5. `goto 2`.

이 방법을 사용하면 두 번째 계란은 필요가 없다. 하나만으로도 문제의 층을 찾아낼 수 있다.

하지만 이 방법은 최악의 경우 100 번이나 계란을 떨어뜨려 보아야 한다는 문제가 있다.

## 두 계란의 역할을 나누는 방법

위의 방법은 문제를 풀고 난 뒤에도 계란 하나가 남는다는 장점이 있지만 계란 하나만 사용한다는 점에서 비효율적이다.

따라서 다음과 같은 방법을 사용하면 위의 방법의 효율을 높일 수 있다.

* 50 층에 가서 1번 계란을 떨어뜨려 본다.
    * 깨졌다면 2번 계란을 써서 1 ~ 49 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 75 층에 가서 떨어뜨려 본다.
    * 깨졌다면 2번 계란을 써서 51 ~ 74 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 87 층에 가서 떨어뜨려 본다.
    * 깨졌다면 2번 계란을 써서 76 ~ 86 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* ...

즉, 1번 계란으로 대상 집합의 수를 확 줄이고 2번 계란으로 선형 탐색을 해서 알아내는 방법이다.

이 방법을 쓰면 문제의 층 수가 94층일 경우 다음과 같이 7회의 시도로 알아낼 수 있다.

1. 50 층에서 1번 계란이 안 깨짐.
2. 75 층에서 1번 계란이 안 깨짐.
3. 87 층에서 1번 계란이 안 깨짐.
4. 93 층에서 1번 계란이 안 깨짐.
5. 96 층에서 1번 계란이 깨짐.
6. 94 층에서 2번 계란이 안 깨짐.
7. 95 층에서 2번 계란이 깨짐. 따라서 문제의 층 수는 94층이다.

최악의 경우(49층인 경우)에는 50 번 떨어뜨려 보면 된다.

최선의 경우는 문제의 층수가 1층인 경우로, 다음과 같이 3 번으로 알아낼 수 있다.

1. 50 층에서 1번 계란이 깨짐.
2. 1층에서 2번 계란이 안 깨짐.
3. 2층에서 2번 계란이 깨져서 문제의 층 수가 1층이라는 것을 알아낸다.

문제의 층수가 0층인 것도 가능한다면, 0층이 최선의 경우가 된다.

1. 50 층에서 1번 계란이 깨짐.
2. 1층에서 2번 계란이 깨짐. 따라서 문제의 층 수는 0층이다.

## 최악의 경우를 조금 더 개선해 보자

바로 위의 방법은 최악의 경우 계란을 50 번 떨어뜨려야 했다. 이걸 좀 더 개선할 수 있을까?

위의 방법의 최악의 경우는 계란을 떨어뜨리는 횟수가 50 번이었다.
그리고 그 이유는 첫 시도에서 1번 계란을 50층에서 떨어뜨리기 때문이다.

그렇다면 첫 시도의 층수를 낮추는 방법을 생각해 볼 수 있을 것이다.

10층에서 시작한다면 어떨까?

* 10 층에 가서 1번 계란을 떨어뜨려 본다.
    * 깨졌다면 2번 계란을 써서 1 ~ 9 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 20층에 가서 떨어뜨려 본다.
    * 깨졌다면 2번 계란을 써서 11 ~ 19 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 30층에 가서 떨어뜨려 본다.
    * 깨졌다면 2번 계란을 써서 21 ~ 29 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* ...
    * ...
* 1번 계란이 안 깨졌다면 100층에 가서 떨어뜨려 본다.
    * 깨졌다면 2번 계란을 써서 91 ~ 99 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.

이 방법을 사용한다면 문제의 층 수가 42층일 경우 다음과 같이 8 회의 시도로 알아낼 수 있다.

1. 10 층에서 1번 계란이 안 깨짐.
2. 20 층에서 1번 계란이 안 깨짐.
3. 30 층에서 1번 계란이 안 깨짐.
4. 40 층에서 1번 계란이 안 깨짐.
5. 50 층에서 1번 계란이 깨짐.
6. 41 층에서 2번 계란이 안 깨짐.
7. 42 층에서 2번 계란이 안 깨짐.
8. 43 층에서 2번 계란이 깨짐. 따라서 문제의 층 수는 42층이다.

최악의 경우는 문제의 층 수가 99층인 경우이다. 다음과 같이 19 회의 시도로 알아낼 수 있다.

* $$1.$$ 10 층에서 1번 계란이 안 깨짐.
* $$2.$$ 20 층에서 1번 계란이 안 깨짐.
* ...
* $$9.$$ 90 층에서 1번 계란이 안 깨짐.
* $$10.$$ 100 층에서 1번 계란이 깨짐.
* $$11.$$ 91 층에서 2번 계란이 안 깨짐.
* $$12.$$ 92 층에서 2번 계란이 안 깨짐.
* ...
* $$19.$$ 99 층에서 2번 계란이 안 깨짐. 따라서 문제의 층 수는 99층이다.

최악의 경우 시도 횟수를 50회에서 19회로 줄일 수 있었다. 그러나 더 줄일 수 없을까?

## 횟수를 제한한다고 생각하는 방법

이번에는 바로 위의 방식에 대해 계란을 떨어뜨리는 횟수를 제한하면 건물의 몇 층까지 검사할 수 있는지를 생각해 보자.

예를 들어 횟수를 10 회로 제한한다면 다음과 같이 시도할 수 있을 것이다.

* 10 층에 가서 1번 계란을 떨어뜨려 본다. (남은 횟수 9)
    * 깨졌다면 2번 계란을 써서 1 ~ 9 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 19 층에 가서 떨어뜨려 본다. (남은 횟수 8)
    * 깨졌다면 2번 계란을 써서 11 ~ 18 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 27 층에 가서 떨어뜨려 본다. (남은 횟수 7)
    * 깨졌다면 2번 계란을 써서 20 ~ 26 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 34 층에 가서 떨어뜨려 본다. (남은 횟수 6)
    * 깨졌다면 2번 계란을 써서 28 ~ 33 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 40 층에 가서 떨어뜨려 본다. (남은 횟수 5)
    * 깨졌다면 2번 계란을 써서 35 ~ 39 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 45 층에 가서 떨어뜨려 본다. (남은 횟수 4)
    * 깨졌다면 2번 계란을 써서 41 ~ 44 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 49 층에 가서 떨어뜨려 본다. (남은 횟수 3)
    * 깨졌다면 2번 계란을 써서 46 ~ 48 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 52 층에 가서 떨어뜨려 본다. (남은 횟수 2)
    * 깨졌다면 2번 계란을 써서 50 ~ 51 층을 대상으로 한 층씩 올려가며 선형 탐색을 한다.
* 1번 계란이 안 깨졌다면 54 층에 가서 떨어뜨려 본다. (남은 횟수 1)
    * 깨졌다면 2번 계란을 써서 53 층에 가서 떨어뜨려 본다. (남은 횟수 0)
* 1번 계란이 안 깨졌다면 55 층에 가서 떨어뜨려 본다. (남은 횟수 0)
    * 횟수 제한 때문에 시도는 여기에서 끝낸다.

이렇게 나열해놓고 보니 패턴이 보인다.

$$
\begin{array}{l}
10 \\
10 + 9 = 19 \\
10 + 9 + 8 = 27 \\
10 + 9 + 8 + 7 = 34 \\
10 + 9 + 8 + 7 + 6 = 40 \\
... \\
10 + 9 + 8 + 7 + 6 + ... + 1 = 55 \\
\end{array}
$$

간단하게 요약하자면 $$ \sum_{k=1}^{10} k = 55 $$ 로 표현할 수 있다.

즉, 횟수를 10회로 제한한다면 55층까지만 테스트할 수 있다.

### 일반화

이제 횟수를 $$x$$로 바꾸고 일반화해보자.

이제 이 문제를 다음과 같이 표현할 수 있다.

$$
\begin{array}{l}
x \\
x + (x-1) \\
x + (x-1) + (x-2) \\
... \\
x + (x-1) + (x-2) + ... + 2 + 1 \\
\end{array}
$$

그냥 1부터 $$x$$까지의 합이라는 것을 알 수 있다.

$$\sum_{k=1}^{x} k = \frac{x(x+1)}{2} $$

그러므로 시도 횟수를 $$x$$ 회로 제한했을 경우 테스트 가능한 건물의 층 수는 최대 $$\frac{x(x+1)}{2}$$ 층이라고 할 수 있다.

그렇다면 이제 테스트 가능한 건물의 층 수를 100 층으로 맞추고 x를 계산하면 시도 횟수를 얻어낼 수 있다.

$$
\begin{align}
\frac{x(x+1)}{2} & = 100 \\
x(x+1) & = 200 \\
\end{align}
$$

$$x$$는 정수이고, 암산으로 $$\frac{x^2 + x}{2} = 100$$의 해를 구하는 것은 힘드니까 리미트가 없는 경우의 이진 탐색을 쓰자.

$$
\begin{align}
x = 1; & \quad \frac{1 \times 2}{2} = 1  \\
x = 2; & \quad \frac{2 \times 3}{2} = 3  \\
x = 4; & \quad \frac{4 \times 5}{2} = 10 \\
x = 8; & \quad \frac{8 \times 9}{2} = 36 \\
x = 16; & \quad \frac{16 \times 17}{2} = 136 \\
x = 12; & \quad \frac{12 \times 13}{2} = 78 \\
x = 14; & \quad \frac{14 \times 15}{2} = 105 \\
x = 13; & \quad \frac{13 \times 14}{2} = 91 \\
\end{align}
$$

* 횟수 제한이 $$x = 13$$ 인 경우, $$ \frac{13 \times 14}{2} = 91 $$ 층까지 테스트 가능.
* 횟수 제한이 $$x = 14$$ 인 경우, $$ \frac{14 \times 15}{2} = 105 $$ 층까지 테스트 가능.

그러므로, 100층 건물을 테스트하려면 최악의 경우 계란을 14 번만 던져보면 된다.

## Links

* [The Two Egg Problem](http://datagenetics.com/blog/july22012/index.html )
* [You have 2 eggs. You are on a 100 floor building...](https://www.quora.com/You-have-2-eggs-You-are-on-a-100-floor-building-You-drop-the-egg-from-a-particular-floor-It-breaks-or-survives-If-it-survives-you-can-throw-the-same-egg-from-a-higher-floor-How-many-attempts-do-you-need-to-identify-the-max-floor-at-which-the-egg-doesnt-break-when-thrown-down )
* [2 Eggs and 100 Floors](https://medium.com/@khopsickle/2-eggs-and-100-floors-a032beb77aaa )
