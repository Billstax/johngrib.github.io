---
layout  : wiki
title   : 행렬
summary : Matrices
date    : 2019-02-02 18:38:20 +0900
updated : 2019-02-02 23:51:09 +0900
tags    : math
toc     : true
public  : true
parent  : study-discrete-mathematics
latex   : true
---
* TOC
{:toc}

# 정의

>
A matrix is a rectangular array of numbers.
A matrix with m rows and n columns is called an $$m \times n$$ matrix.
The plural of matrix is matrices.
A matrix with the same number of rows as columns is called square.
Two matrices are equal
if they have the same number of rows and the same number of columns
and the corresponding entries in every position are equal.

* 행렬은 숫자를 사각형으로 배열한 것이다.
* 행렬의 영문명 matrix 는 단수형이고 matrices 는 복수형이다.
* $$ m $$ 개의 행과 $$ n $$개의 열을 갖는 행렬을 $$ m \times n $$ 행렬이라 한다.
    * 예: $$ \begin{bmatrix} 1 & 2 \\ 3 & 4 \\ 5 & 6 \end{bmatrix} $$은 $$ 3 \times 2 $$ 행렬이다.

>
Let m and n be positive integers and let  
$$
A =
\begin{bmatrix}
a_{11} & a_{12} & \cdots & a_{1n} \\
a_{21} & a_{22} & \cdots & a_{2n} \\
\vdots & \vdots &        & a_{mn} \\
\end{bmatrix}
$$  
The $$i$$th row of A is the $$1 \times n$$ matrix $$[a_{i1},a_{i2}, ... ,a_{in}]$$.  
The $$j$$th column of A is the $$m \times 1$$ matrix
$$
\begin{bmatrix}
a_{1j} \\
a_{2j} \\
\vdots \\
a_{mj} \\
\end{bmatrix}
$$  
The $$(i, j)$$th element or entry of A is the element $$a_{ij}$$,
that is, the number in the $$i$$th row and $$j$$th column of A.
A convenient shorthand notation for expressing the matrix A is to write $$A = [a_{ij}]$$, which indicates that A is the matrix with its $$(i, j)$$th element equal to $$a_{ij}$$.

* 위의 행렬 A에서,
    * A의 $$ i $$ 번째 **행(row)**은 $$[a_{i1},a_{i2}, ... ,a_{in}]$$ 이다.
    * A의 $$ j $$ 번째 **열(column)**은 $$ \begin{bmatrix} a_{1j} \\ a_{2j} \\ \vdots \\ a_{mj} \\ \end{bmatrix} $$ 이다.
    * A의 $$(i,j)$$ 번째 **원소(element)**는 원소 $$a_{ij}$$ 이다.
    * 행렬 A를 $$ A = [a_{ij}] $$ 로 짧게 표현하기도 한다.

## 행렬의 합

>
Let $$A = [a_{ij}]$$ and $$B = [b_{ij}]$$ be $$m \times n$$ matrices.
The sum of A and B,
denoted by $$A + B$$, is the $$m \times n$$ matrix that has $$a_{ij} + b_{ij}$$ as its $$(i,j)$$th element.
In other words, $$A+B=[a_{ij} + b_{ij}]$$.

* $$ A + B = [ a_[ij] + b_{ij} ] $$.
* 크기가 다른(모양이 다른) 두 행렬의 합은 구할 수 없다.

## 행렬의 곱

>
Let A be an $$m \times k$$ matrix and B be a $$k \times n$$ matrix.
The product of A and B, denoted by AB, is the $$m \times n$$ matrix with its $$(i, j)$$th entry equal to the sum of the products of the corresponding elements from the ith row of A and the j th column of B. In other words, if $$AB = [c_{ij}]$$, then  
$$c_{ij} =a_{i1} \times b_{1j} + a_{i2} \times b_{2j} + ··· + a_{ik} \times b_{kj}$$.

* $$m \times k$$ 행렬과 $$k \times n$$ 행렬의 곱은 $$m \times n$$ 행렬이다.
* 행렬의 곱은 교환법칙이 성립하지 않는다(Matrix multiplication is not commutative).
    * $$ A \times B \ne B \times A $$이므로 주의할 것.


# 용어 정리

| English       | 한국어      | 예/설명                                  |
|---------------|-------------|------------------------------------------|
| matrix        | 행렬        |                                          |
| matrices      | 행렬(들)    |                                          |
| square matrix | 정방 행렬   | 행과 열의 수가 같은 정사각형 모양의 행렬 |
| row           | 행          |                                          |
| column        | 열          |                                          |
| element       | 행렬의 원소 |                                          |

# 참고문헌

* Rosen의 이산수학 / Kenneth H. Rosen 저 / 공은배 등저 / 한국맥그로힐(McGraw-Hill KOREA) / 2017년 01월 06일
