---
category: "algorithm"
tags: 
  - "algorithm"
  - "blog"
  - "python"
---

### 파이썬 print, format 사용법 일부

파이썬에서 형식을 지정하여 출력할 때, `%`, `format`, `f-string` 세 가지 방법이 있는데, 가장 편한 `f-string`의 경우 파이썬 3.52? 이상이어야 한다. 그리고 남은 두 가지 방법 중 `format`이 기능도 많고 강력하여 더 권장된다고 한다.

### 기본 사용 (문자열)

```py
str1=input()
print("{}".format(str1))
```

아래와 같이 키를 지정할 수도 있다. 키랑 매개변수 이름이 같을 때 javascript처럼 생략 가능하면 좋겠는데 그렇지 않다.
```py
str1=input()
print("{name}".format(name=str1))
```

```py
name=input()
print("{name}".format(name)) # ERROR
print("{name}".format({name})) # ERROR
print("{name}".format(name=name)) # OK
```


소수점 자릿수를 특정 자리까지 출력해야 하거나, 정수 출력의 앞부분에 0을 포함해야 하는 경우가 있다. 그런 경우 다음과 같이 쓰면 된다.

### 정수

```py
num=int(input())
print("{num1:04d}".format(num1=num))
```

`04d`에서 앞에 `0`이 있으면 남는 자릿수를 0으로 채우되, `4`자릿수가 기준이고, `d`정수 자료형이다.

### 실수

만약 키 없이 형태만 지정하고 싶으면 다음과 같이 콜론 앞을 비우면 된다. 실수이므로 `f`

```py
float1=float(input())
print("{:.11f}".format(float1))
```

관련문제: 
- [1029](https://codeup.kr/problem.php?id=1029) : [기초-데이터형] 실수 1개 입력받아 그대로 출력하기2(설명)



