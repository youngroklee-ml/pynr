# 날짜 및 시간 {#date-time}

날짜와 시간이 파이썬과 R에서 어떻게 저장되는지 알아보자. 날짜와 시간은 다루기 까다로운 부분들이 존재하기 때문에 이를 위한 패키지(예: R의 lubridate)들이 개발되어 있다. 하지만, 이 책에서는 사용자의 추가적인 설치 없이 기본적으로 제공되는 패키지/모듈에 기반하여 살펴보기로 하자. 따라서, 파이썬에서는 datetime 모듈에 기반하여 살펴볼 것이며, R에서는 base 패키지에 기반하여 살펴볼 것이다.

## 날짜

### 파이썬

파이썬에서는 datetime 모듈의 date 클래스 객체로 날짜를 저장한다. 객체를 생성하는 가장 기본적인 방법으로는 year, month, day를 순서대로 생성자의 인자로 입력하는 방법이 있다.


```python
from datetime import date
py_date = date(2022, 7, 5)
py_date
```

```
## datetime.date(2022, 7, 5)
```

```python
type(py_date)
```

```
## <class 'datetime.date'>
```

다른 방법으로는 클래스 메서드 `date.fromisoformat()`를 사용하여 ISO 표준 형식의 날짜를 문자열로 입력하는 방법이 있다.


```python
date.fromisoformat('2022-07-05')
```

```
## datetime.date(2022, 7, 5)
```

파이썬의 date 클래스의 객체는 연, 월, 일의 세 가지 속성값을 지닌다.


```python
py_date.year
```

```
## 2022
```

```python
py_date.month
```

```
## 7
```

```python
py_date.day
```

```
## 5
```


#### Day differences

두 날짜를 `-` 연산자를 이용하여 빼면 timedelta 클래스의 객체를 반환한다. 


```python
date.fromisoformat('2022-07-06') - py_date
```

```
## datetime.timedelta(days=1)
```

```python
date.fromisoformat('2022-07-04') - py_date
```

```
## datetime.timedelta(days=-1)
```

```python
date.fromisoformat('2023-01-01') - py_date
```

```
## datetime.timedelta(days=180)
```

timedelta 객체는 두 날짜의 차이를 `days` 속성값으로 지닌다.


```python
(date.fromisoformat('2023-01-01') - py_date).days
```

```
## 180
```


#### Offset

기존 날짜에 어떠한 기간을 더하거나 뺀 날짜를 얻고자 할 때, 단순히 숫자를 더하거나 빼려고 시도하면 오류를 얻게 된다.


```python
py_date + 180
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: unsupported operand type(s) for +: 'datetime.date' and 'int'
```

대신, 앞에서 보았던 timedelta 객체를 이용한다.


```python
from datetime import timedelta
py_date + timedelta(days=180)
```

```
## datetime.date(2023, 1, 1)
```


### R

R에서는 `as.Date()` 함수에 ISO 표준 형식의 날짜를 문자열로 입력하여 Date 클래스 객체를 생성할 수 있다.


```r
r_date <- as.Date("2022-07-05")
r_date
```

```
## [1] "2022-07-05"
```

```r
class(r_date)
```

```
## [1] "Date"
```

입력 문자열이 ISO 표준 형식이 아닌 경우, `format` 인자에 입력 문자열의 형식을 명시함으로써 값을 올바르게 읽을 수 있다.


```r
as.Date("05/07/2022", format = "%d/%m/%Y")
```

```
## [1] "2022-07-05"
```

```r
as.Date("Jul 5, 2022", format = "%b %d, %Y")
```

```
## [1] "2022-07-05"
```

R의 Date 클래스를 사용할 때 중요하게 이해할 점은, 이 클래스의 기반에 존재하는 값이 숫자형(double)이라는 점이다.


```r
typeof(r_date)
```

```
## [1] "double"
```

클래스 속성을 제거하여, 생성한 날짜 객체의 기반에 있는 숫자값을 출력해 보자.


```r
unclass(r_date)
```

```
## [1] 19178
```

이 숫자는 `1970-01-01`로부터 날이 얼마나 지났는지를 표현한다. 즉, `1970-01-01`은 `0`, `1970-01-02`는 `1`, `1969-12-31`은 `-1`의 값을 지닌다.


```r
unclass(as.Date("1970-01-01"))
```

```
## [1] 0
```

```r
unclass(as.Date("1970-01-02"))
```

```
## [1] 1
```

```r
unclass(as.Date("1969-12-31"))
```

```
## [1] -1
```


#### Day differences

두 날짜 사이의 기간을 `-` 연산자를 사용하여 계산해 보자.


```r
r_diff <- as.Date("2023-01-01") - r_date
r_diff
```

```
## Time difference of 180 days
```

이 객체의 클래스는 difftime이며, 그 기저에 있는 값의 형태는 숫자형(double)이다.


```r
class(r_diff)
```

```
## [1] "difftime"
```

```r
typeof(r_diff)
```

```
## [1] "double"
```

클래스 속성을 제거하여 값을 확인해 보자.


```r
unclass(r_diff)
```

```
## [1] 180
## attr(,"units")
## [1] "days"
```



#### Offset

R의 Date 객체는 기본적으로 숫자형 데이터이므로, 여기에 숫자를 더하거나 빼서 편리하게 새로운 날짜를 얻을 수 있다.


```r
r_date + 180
```

```
## [1] "2023-01-01"
```

물론, 명시적으로 difftime 객체를 생성하여 수행할 수도 있다.


```r
r_date + as.difftime(180, units = "days")
```

```
## [1] "2023-01-01"
```




## 날짜/시간

날짜 뿐만 아니라 시간을 함께 저장하는 데이터 종류를 살펴보자.


### 파이썬

datatime 모듈의 datetime 클래스가 이를 지원한다. 객체를 만들 때 생성자에 앞에서 살펴보았던 연, 월, 일 외에 추가로 시간, 분, 초, 마이크로초(0 ~ 999999)를 입력하여 생성할 수 있다.


```python
from datetime import datetime
py_datetime = datetime(2022, 7, 5, 12, 34, 56, 987654)
py_datetime
```

```
## datetime.datetime(2022, 7, 5, 12, 34, 56, 987654)
```

```python
type(py_datetime)
```

```
## <class 'datetime.datetime'>
```

이 때, 마이크로초는 정수여야 하며, 더 작은 단위의 시간을 입력하려 실수형을 입력하면 오류가 발생한다.


```python
datetime(2022, 7, 5, 12, 34, 56, 987654.3)
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: integer argument expected, got float
```

클래스 메서드 `fromisoformat()`을 이용하여 문자열 형태의 표현으로부터 datetime 클래스 객체를 생성할 수 있다. **주의: 초를 소수점 이하 세 자리, 즉, 밀리초까지만 입력할 수 있으며, 네 자리 이상 입력하려 시도하면 오류가 발생한다.**


```python
datetime.fromisoformat('2022-07-05T12:34:56.987')
```

```
## datetime.datetime(2022, 7, 5, 12, 34, 56, 987000)
```

클래스 메서드 `strptime()`는 포맷에 더 유연하며, 마이크로초 단위까지 입력받을 수 있다.


```python
datetime.strptime('2022-07-05 12:34:56.987654', '%Y-%m-%d %H:%M:%S.%f')
```

```
## datetime.datetime(2022, 7, 5, 12, 34, 56, 987654)
```



#### Datietime differences

두 날짜/시간 값을 `-` 연산자를 이용하여 빼면 timedelta 클래스의 객체를 반환한다.


```python
py_timedelta = datetime.fromisoformat('2023-01-01') - py_datetime
py_timedelta
```

```
## datetime.timedelta(days=179, seconds=41103, microseconds=12346)
```

이 때, `-` 연산자 앞의 객체를 생성할 때 날짜만 입력하였음을 주목하자. datetime 객체를 생성할 때, 시간 이하의 인자는 생략될 경우 0을 기본값으로 사용한다.

또한, timedelta 객체가 `days`, `seconds`, `microseconds`의 속성값을 지니는 것에 주목하자. 시간과 분 단위의 차이는 초 단위의 차이로 변환된다.


다음과 같이 date객체와 datetime 객체간의 시간 차이를 계산하려 하면 오류가 발생한다. (다음에서 `datetime.fromisoformat()`이 아닌 `date.fromisoformat()`을 사용하려 하였음을 주목하자.)


```python
date.fromisoformat('2023-01-01') - py_datetime
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: unsupported operand type(s) for -: 'datetime.date' and 'datetime.datetime'
```



### R

R의 base 패키지에서 제공하는 날짜/시간 클래스로는 POSIXct와 POSIXlt가 있다. 이 두 클래스가 표현하는 값은 기본적으로 같으나, 내부적으로 저장되는 방법이 서로 다르다.

마이크로초 단위의 값을 출력하기 위해, R 세션의 `digits` 옵션을 변경하자.


```r
options(digits = 20)
```


#### POSIXct

`as.POSIXct()` 함수를 사용하여 문자열로부터 POSIXct 클래스 객체를 생성하자. `format` 인자를 사용하여 다양한 포맷의 문자열로부터 값을 읽을 수 있으나, 본 장에서는 기본 포맷을 사용하기로 하자.


```r
r_datetime_ct <- as.POSIXct("2022-07-05 12:34:56.987654")
r_datetime_ct
```

```
## [1] "2022-07-05 12:34:56 EDT"
```

```r
class(r_datetime_ct)
```

```
## [1] "POSIXct" "POSIXt"
```

여기에 출력된 값에는 초의 소숫점 이하는 생략되어 있으며, 마지막에 시간대(timezone) 정보가 추가되어 출력된다. 우선, 시간대 정보는 신경쓰지 말자. 

출력된 값의 기저에 실수형 값이 저장되어 있음을 `typeof()`를 통해 알 수 있다.


```r
typeof(r_datetime_ct)
```

```
## [1] "double"
```

정확히 어떠한 값이 저장되어 있는지를 알아보기 위해 `unclass()`를 실행해 보자.


```r
unclass(r_datetime_ct)
```

```
## [1] 1657038896.9876539707
## attr(,"tzone")
## [1] ""
```

이 실수형 값은 정수 부분과 소숫점 이하의 부분으로 나뉜다. 

##### 소숫점 이하 부분

우선 소숫점 이하 부분부터 살펴보자. 실수형 값에서 우선 소숫점 이하의 값이 입력된 마이크로초 정보와 매우 근사하지만 정확히 같이 않음을 볼 수 있다. 입력된 정보와 정확히 같기 위해서는 `.9876540000`이어야 하겠지만, 실제 저장된 값을 출력해 보면 `.9876539707`과 같이 미세하게 다르다. 실수형 데이터는 정확한 값이 아니라 그 값에 근사한 값을 표현하는 형태로 저장되기 때문에 이러한 차이가 발생한다. 이는 R과 파이썬이 다른 부분이다.

- R은 실수형으로 저장하기 때문에 마이크로초 이하 단위의 시간도 표현 가능한 반면, 저장하고자 했던 정확한 값을 저장하지 못한다.
- 파이썬은 마이크로초 정보를 따로 정수형으로 저장하기 때문에, 저장하고자 했던 정확한 값을 저장하는 반면, 만약 애초에 저장하고자 했던 정보가 마이크로초 이하라면, 그 시간 정보를 저장할 수 없다.


##### 정수 부분

다음으로, 정수 부분을 살펴보자. 정수 부분은 초 단위까지 표현하는 부분이다.


```r
unclass(as.POSIXct("2022-07-05 12:34:56"))
```

```
## [1] 1657038896
## attr(,"tzone")
## [1] ""
```

좀 더 정확하게는, 협정 세계시(Coordinated Universal Time; UTC) 기준 1970년 1월 1일 0시 0분 0초부터 소요된 초이다. 다시 말하면, 다음 코드는 0을 반환한다. 


```r
unclass(as.POSIXct("1970-01-01 00:00:00", tz = "UTC"))
```

```
## [1] 0
## attr(,"tzone")
## [1] "UTC"
```

다음 코드는 86,400을 반환할텐데, 이는 하루가 86,400초이기 때문이다. 


```r
unclass(as.POSIXct("1970-01-02 00:00:00", tz = "UTC"))
```

```
## [1] 86400
## attr(,"tzone")
## [1] "UTC"
```

여기서 `tz = "UTC"` 인자를 사용했음에 주목하자. 이를 명시하지 않으면, 각자의 R 세션에 설정된 시간대를 기본값으로 사용하며, 이는 `"UTC"`를 사용할 때와는 다른 값을 반환한다. 다음의 예를 보자.


```r
unclass(as.POSIXct("1970-01-02 00:00:00"))
```

```
## [1] 104400
## attr(,"tzone")
## [1] ""
```

```r
unclass(as.POSIXct("1970-01-02 00:00:00", tz = Sys.timezone()))
```

```
## [1] 104400
## attr(,"tzone")
## [1] "America/New_York"
```

날짜/시간 객체가 표현하는 시간대는 `tz` 인자를 통해 설정할 수 있지만, 그 기저의 값은 항상 협정 세계시(Coordinated Universal Time; UTC) 기준 1970년 1월 1일 0시 0분 0초부터 얼마나 지났는지를 나타낸다.


#### POSIXlt

다음으로 POSIXlt 클래스 객체를 생성해 보자. `as.POSIXlt()` 함수를 사용하며, 함수 사용 방법은 `as.POSIXct()`와 마찬가지다.


```r
r_datetime_lt <- as.POSIXlt("2022-07-05 12:34:56.987654")
r_datetime_lt
```

```
## [1] "2022-07-05 12:34:56 EDT"
```

```r
class(r_datetime_lt)
```

```
## [1] "POSIXlt" "POSIXt"
```

이는 리스트로 저장되어 있는 보다 구조적인 데이터다.


```r
typeof(r_datetime_lt)
```

```
## [1] "list"
```

정확히 어떠한 값이 저장되어 있는지를 알아보기 위해 `unclass()`를 실행해 보자.


```r
unclass(r_datetime_lt)
```

```
## $sec
## [1] 56.987653999999999144
## 
## $min
## [1] 34
## 
## $hour
## [1] 12
## 
## $mday
## [1] 5
## 
## $mon
## [1] 6
## 
## $year
## [1] 122
## 
## $wday
## [1] 2
## 
## $yday
## [1] 185
## 
## $isdst
## [1] 1
## 
## $zone
## [1] "EDT"
## 
## $gmtoff
## [1] NA
```

여기에서 주목할 두 가지 부분이 있다.

- POSIXct에서 정수 부분에 함께 포함되어 있던 여러가지 정보가 POSIXlt에서는 여러 원소로 나뉘어 저장되어 있다. 시간대 정보 또한 `zone` 원소로 저장되어 있다.
- 초 단위 이하의 정보는 `sec` 원소로 저장되어 있는데, 여기에서 소숫점 이하의 값이 앞에서 POSIXct에서 봤던 값과 다소 다를 수 있다. 이는 다시금 실수값의 저장 방식(근사치)과 관련이 있을 것이다. POSIXct에서는 실수값에서 정수 부분의 유효숫자가 매우 많았던 반면, POSIXlt에서는 초 단위 이하만 따로 `sec`에서 관리하므로 정수 부분의 유효숫자가 적고(두 자리), 따라서 소숫점 이하의 초 단위 정보를 보다 가까운 근사치로 표현할 수 있다.


#### Datietime differences

두 날짜/시간 값을 `-` 연산자를 이용하여 빼면 timediff 클래스의 객체를 반환한다.


```r
r_timediff <- as.POSIXct("2023-01-01") - r_datetime_lt
r_timediff
```

```
## Time difference of 179.51739597622719202 days
```

```r
class(r_timediff)
```

```
## [1] "difftime"
```

이 기저의 값은 실수형(double)이며, 기본으로 일 단위로 나타난다.


```r
unclass(r_timediff)
```

```
## [1] 179.51739597622719202
## attr(,"units")
## [1] "days"
```

다른 단위로 그 차이를 알고 싶다면 `as.double()` 호출 시 `units` 인자를 제공할 수 있다.


```r
as.double(r_timediff, units = "secs")
```

```
## [1] 15510303.012346029282
```

```r
as.double(r_timediff, units = "hours")
```

```
## [1] 4308.4175034294530633
```

timediff 객체 자체에 저장된 단위를 변경하고 싶다면 `units<-` 함수를 사용하면 된다.

하지만, 파이썬과 같이 각 단위를 분리하여 좀 더 표현하기 쉽게 보여주고 저장하지는 못한다.


파이썬과 마찬가지로, 날짜값에서 날짜/시간값을 빼는 연산을 수행할 수 없다. 그 결과는 POSIXct를 사용할 때와 POSIXlt를 사용할 때가 다르다.

POSIXct를 사용할 때는 기저의 값이 실수형이므로, 마찬가지로 기저의 값이 실수형인 Date와의 연산이 특별한 구현 없이도 R에서 기술적으로 가능하다. 하지만, 그 값의 단위가 다르기 때문에 (POSIXct는 초, Date는 일), 의미가 결과값이 나타나게 될 것이다. 이때, R은 warning 메시지를 출력하며, 결과의 클래스는 `-` 연산자 앞에 놓인 객체의 클래스에 따라 생성된다.


```r
as.Date("2023-01-01") - r_datetime_ct
```

```
## Warning: Incompatible methods ("-.Date", "-.POSIXt") for "-"
```

```
## [1] "-4534796-08-05"
```

```r
r_datetime_ct - as.Date("2023-01-01")
```

```
## Warning: Incompatible methods ("-.POSIXt", "-.Date") for "-"
```

```
## [1] "2022-07-05 07:12:18 EDT"
```

반면, POSIXlt는 기저의 자료 형태가 리스트이므로, 기저의 값이 실수형인 Date와 연산이 특별한 구현 없이는 기본적으로 정의되지 않는다. 따라서 이는 오류를 발생시킨다.


```r
as.Date("2023-01-01") - r_datetime_lt
```

```
## Warning: Incompatible methods ("-.Date", "-.POSIXt") for "-"
```

```
## Error in as.Date("2023-01-01") - r_datetime_lt: non-numeric argument to binary operator
```



## 시간

날짜와 상관 없이 시간만 중요한 경우를 가정해 보자. 이 때, 날짜는 저장하지 않고 시간만 따로 저장하고 싶을 것이다.

### 파이썬

파이썬은 datetime 모듈의 time 클래스를 사용하여 시간만 따로 저장할 수 있도록 한다. 생성자 `time()`에 시간, 분, 초, 마이크로초(0 ~ 999999)를 입력하여 생성할 수 있다.


```python
from datetime import time
py_time = time(12, 34, 56, 987654)
py_time
```

```
## datetime.time(12, 34, 56, 987654)
```

```python
type(py_time)
```

```
## <class 'datetime.time'>
```

이 때, datetime과 마찬가지로 마이크로초는 정수여야 하며, 더 작은 단위의 시간을 입력하려 실수형을 입력하면 오류가 발생한다.


```python
time(12, 34, 56, 98765.4)
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: integer argument expected, got float
```

#### Time differences

파이썬에서 time 객체간 차이를 `-` 연산자를 사용하여 직접 계산할 수는 없다.


```python
py_time - time(12, 0, 0)
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: unsupported operand type(s) for -: 'datetime.time' and 'datetime.time'
```

이를 위해서는 datetime 객체를 생성한 뒤에 차이를 구해야 한다.


```python
datetime.combine(date = date.min, time = py_time) -\
  datetime.combine(date = date.min, time = time(12, 0, 0))
```

```
## datetime.timedelta(seconds=2096, microseconds=987654)
```



### R

기본적으로 R에는 날짜와 분리된 별도의 시간 자료 형태가 없다. 따라서 (별도의 추가적인 패키지를 사용하지 않는 한) 앞에서 살펴본 날짜/시간 클래스를 적절히 사용해야 한다.


```r
r_time <- as.POSIXlt("12:34:56.987654", format = "%H:%M:%OS")
r_time
```

```
## [1] "2022-07-04 12:34:56 EDT"
```

```r
class(r_time)
```

```
## [1] "POSIXlt" "POSIXt"
```


```r
r_time - as.POSIXlt("12:00:00", format = "%H:%M:%S")
```

```
## Time difference of 34.949794232845306396 mins
```


