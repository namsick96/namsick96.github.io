---
title: "lambda를 이용한 sort 함수 사용 (파이썬)"
excerpt: "python"

categories:
    - python
tags:
    - python
last_modified_at: 2021-01-29T08:06:00-05:00
sitemap :
  changefreq : daily
  priority : 1.0
---
<br>

# Lambda란 무엇인가?

lambda는 파이썬에서 익명 함수이다.
우리는 보통 함수를 이렇게 정의한다.

```python
def sample(x,y):
  ans = x+y
  return ans
```
그런데 람다를 쓰면 이런 작업을 한줄에 끝낼 수 있다.

```python
sample= lambda x,y :x+y
a=sample(1,2) # a는 1+2 인 3의 값을 가진다.
```
여기서 lambda 뒤에 나오는 x,y는 arguments 부분이다. : 뒤에 나오는 부분은 return 값을 나타내는 부분이다.
즉 lamda 함수는
<span style="color:purple; font-size:2em">lamda [arguments] : [return 되는 expression 부분]</span>
이렇게 나뉜다.
그리고 이렇게 정의된 람다 함수는 보통 변수에 저장된다. 위에서는 sample에 저장되어있다.


이것 뿐만 아니라 다양한 활용이 가능하다.
iterable 한 객체에도 lambda는 적용이 가능하다.

```python
sample= lambda x=x**2
a=sample([1,2,3,4]) # a는 각 원소의 제곱값 들을 원소로 가진다.
```

참 쉽죠? 이걸로 python iteable한 객체의 sort에 적용 할 수 있다.

<br>
<br>

# sort key로 lambda를 주는 방법

[파이썬 sort 문서](https://docs.python.org/ko/3/howto/sorting.html#sortinghowto)


sort 할때 key 인자는 sort를 진행할 기준을 설정해 준다.

즉 sort를 하기 전에 각 리스트 요소에 key에서 지정한 함수로 원소를 변경한 후 sort하는것이 가능하다.

여기서 lambda가 사용 될 수 있다.

```python
>>> sample= lambda x :x**2
>>> a=[-3,5,1,2]
>>> a.sort(key=sample)
>>> a
[1, 2, -3, 5]
```
이렇게 사용 될 수 있다.

이걸 더 활용해서 sort를 두번 진행 할 수 있다.

```python
>>> a=[[1,6],[4,8],[2,10],[14,2]]
>>> sample= lambda x :(x[1],x[0])
>>> a.sort(key =sample)
>>> a
[[14, 2], [1, 6], [4, 8], [2, 10]]
```
이렇게 원소가 2개인 리스트 안의 리스트 들을 정렬할 때 유용하게 쓰일 수 있다.

2개의 원소 중 [1]번째 리스트 원소를 기준으로 외부 리스트를 정렬한 다음, [0] 번째 리스트를 기준으로 또 정렬하는 작업을 해주는 것이다.

이렇게 정렬을 해야하는 문제에서 유용하게 사용 될 수 있다.

