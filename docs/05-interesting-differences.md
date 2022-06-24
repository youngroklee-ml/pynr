# 값을 다룰 때 흥미로운 차이점 {#interesting-differences}

## 큰 정수

매우 큰 정수를 정확한 값으로 사용해야 하는 경우, 파이썬에서는 기본 정수형을 그대로 사용할 수 있겠으나, R에서는 일정 크기 이상의 정수가 정확히 저장되지 않는 문제가 있다. 따라서, 매우 큰 정수값을 사용해야 하는 경우 R에서는 주의하도록 하자.

20자리 정수를 객체에 지정한 뒤, 그 값을 프린트하는 다음 파이썬 코드를 보자. 값이 정확하게 출력된다. 파이썬(보다 정확하게 파이썬3)의 경우, 정수값의 범위에 제한이 없다.


```python
py_int = 12345678901234567890
type(py_int)
```

```
## <class 'int'>
```

```python
print(py_int)
```

```
## 12345678901234567890
```


같은 숫자(마지막에 정수형임을 나타내는 `L`을 추가)를 R 객체에 입력하려 할 때, 그 값이 정확하게 저장되지 않음을 확인할 수 있다. R은 너무 큰 정수는 실수형(double)으로 받아들이고, 실수형에 적용되는 표현식을 통해 주어진 값에 가깝지만 정확히 같지는 않은 값으로 나타난다. `typeof()`의 결과가 `"integer"`가 아닌 `"double"`임에 주목하자.


```r
r_int <- 12345678901234567890L
typeof(r_int)
```

```
## [1] "double"
```

```r
format(r_int, digits = 20, scientific = FALSE)
```

```
## [1] "12345678901234567168"
```


이는 파이썬에서 값을 정수가 아닌 실수형(float)으로 입력하였을 때(마지막에 실수형임을 나타내는 `.` 추가) 나타나는 결과와 동일하다.


```python
py_float = 12345678901234567890.
type(py_float)
```

```
## <class 'float'>
```

```python
print(f'{py_float:20.0f}')
```

```
## 12345678901234567168
```


## 결측값

결측값에 대해서는 R을 먼저 살펴보자.

R에서는 `NA`가 결측값을 나타낸다. 일반적으로는 데이터의 형태와 상관없이 `NA`를 사용하지만, 이는 사실 논리형에 대한 결측값이며, 다른 형태, 즉 정수형(`NA_integer_`), 실수형(`NA_real_`), 문자형(`NA_character_`)의 결측값도 각각 존재한다.


```r
typeof(NA)
```

```
## [1] "logical"
```

```r
typeof(NA_integer_)
```

```
## [1] "integer"
```

```r
typeof(NA_real_)
```

```
## [1] "double"
```

```r
typeof(NA_character_)
```

```
## [1] "character"
```

데이터 형태와 상관없이 `NA`를 써도, vector에 있는 다른 관측값들의 형태와 일관된 결측값으로 자동으로 적용된다.


```r
str(NA)
```

```
##  logi NA
```

```r
str(NA_integer_)
```

```
##  int NA
```

```r
str(c(5L, NA)[2])
```

```
##  int NA
```


파이썬에서는 기본적으로 `None`이 결측값을 표현하는데 사용된다.


```python
None
type(None)
```

```
## <class 'NoneType'>
```

이 결측값은 정수형, 실수형, 문자형 array.array에 원소로 추가할 수가 없는데, 이는 array.array가 type coercion을 지원하지 않기 때문일 것이다.


```python
from array import array
array('l', [5, None])
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: an integer is required (got type NoneType)
```

```python
array('f', [5., None])
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: must be real number, not NoneType
```

```python
array('u', ['5', None])
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: array item must be unicode character
```

결측값을 NumPy array에 입력해 보자. NumPy는 `np.nan`이라는 실수형 결측값을 지원하며, 이를 사용하는 것이 흔한 경우다. 이때 array의 다른 관측값의 형태에 따라 다소 예상치 않은 결과를 얻을 수 있으니 조심하도록 하자.


```python
import numpy as np
type(np.nan)
```

```
## <class 'float'>
```

실수형 array에 이 결측값을 추가하는 경우에는 여전히 실수형 array로 나타날 것이다.


```python
np.array([5., np.nan])
```

```
## array([ 5., nan])
```

정수형 array에 결측값을 추가했을 때는 이 array 자체가 실수형으로 변하는데, 이는 데이터 형태에서 실수형인 결측값이 정수형인 관측값들보다 데이터 형태 결정 시 우선순위에 있기 때문으로 보인다.


```python
np.array([5, np.nan])
```

```
## array([ 5., nan])
```

```python
np.array([5, np.nan])[0]
```

```
## 5.0
```


문자형 array에 결측값을 추가했을 때는 결측값이 문자열 `'nan'`으로 변한다. Type coercion상 문자형이 실수형보다 우위에 있기 때문으로 보인다.


```python
np.array(['5', np.nan])
```

```
## array(['5', 'nan'], dtype='<U32')
```

```python
type(np.array(['5', np.nan])[1])
```

```
## <class 'numpy.str_'>
```




## 문자열

파이썬과 R이 문자열을 다루는 방식을 조금 들여다보자. 우선 파이썬으로 문자열 객체를 만들어 보자. 객체 형태가 `str`로 나타난다. 이는 문자의 시퀀스이며, immutable object이다.


```python
py_string = "Hello World!"
type(py_string)
```

```
## <class 'str'>
```

시퀀스이기 때문에, slicing 등이 지원된다.


```python
py_string[0:5]
```

```
## 'Hello'
```

파이썬에서 이 특정 문자열을 위한 메모리 주소와 할당량을 보자.


```python
id(py_string)
```

```
## 4701737904
```

```python
sys.getsizeof(py_string)
```

```
## 61
```

이제, 파이썬 리스트를 만들고, 각 원소에 동일한 문자열을 저장해 보자. 이 때, 같은 문자열 객체를 재사용하는 것이 아니라, 매번 새롭게 동일한 문자열 값을 저장해 보자.

만약 for문을 사용한다면, 파이썬은 모든 같은 문자열을 같은 메모리에 저장하고, list의 모든 원소가 같은 메모리 주소를 참조하도록 할 것이다. 파이썬에서 for문이 메모리를 좀 더 효율적으로 사용하기 위한 어떤 처리를 하는 것으로 보인다.


```python
py_list = list([None] * 5)
for i in range(5):
  py_list[i] = "Hello Python!"
[id(x) for x in py_list]
```

```
## [4701753136, 4701753136, 4701753136, 4701753136, 4701753136]
```

하지만, 만약 for문을 사용하지 않고 매번 새롭게 원소를 추가하는 경우라면, 파이썬은 같은 문자열을 다른 메모리 주소에 저장할 것이다. 


```python
py_list = list([None] * 5)
py_list[0] = "Hello Python!"
py_list[1] = "Hello Python!"
py_list[2] = "Hello Python!"
py_list[3] = "Hello Python!"
py_list[4] = "Hello Python!"
[id(x) for x in py_list]
```

```
## [4701761968, 4701762160, 4701761776, 4701762224, 4701762416]
```

이와 같이 같은 값의 immutable 문자열을 메모리의 여러 곳에 중복하여 저장하는 것은 경우에 따라 메모리의 사용이 다소 비효율적일 수 있음을 보인다.



다음으로 R의 경우를 살펴보자. R은 문자열을 "character"라는 형태의 데이터로 저장한다. 이는 R 내에서는 vector와 같은 시퀀스가 아니라 하나의 독립적인 값으로 다루어지기 때문에, `[]` operator를 사용하여 문자열의 일부를 slicing할 수 없고, 별도의 함수들을 사용하여 추출해야 한다.

예를 들어, 다음 코드의 결과는 아마 기대와 다를 것이다. 무슨 일이 일어나고 있는지 잠시 생각해 보면, `r_string`은 character 형태의 길이가 1인 vector이고, 따라서 `r_string[1]`은 입력한 문자열을 반환하고, `r_string[2:5]`는 해당 원소가 존재하지 않으므로 `NA`를 반환한다.


```r
r_string <- "Hello R!"
typeof(r_string)
```

```
## [1] "character"
```

```r
r_string[1:5]
```

```
## [1] "Hello R!" NA         NA         NA         NA
```

이 경우, `r_string`에 입력한 문자열의 첫 다섯 개의 문자를 가져오기 위해서는 문자열을 다루는 함수를 사용해야 할 것이다.


```r
substr(r_string, 1, 5)
```

```
## [1] "Hello"
```

`r_string`에 할당된 메모리 주소 정보를 살펴보자.


```r
pryr::inspect(r_string)
```

```
## <STRSXP 0x7fe2f0bf9cf0>
##   <CHARSXP 0x7fe2e0347148>
##   attributes: 
##     <CHARSXP 0x7fe307135340>
```

여기에서 최상위 STRSXP는 vector를 나타내며, CHARSXP는 vector의 원소인 문자열을 나타낸다.

파이썬의 경우와 마찬가지로 시퀀스에 문자열을 입력해 보자. 모든 원소가 문자열이기 때문에 list 대신 vector를 사용해서 보도록 하자. 파이썬에서 다소 비효율적으로 보였던 메모리 관리와 비교하기 위해, R에서도 for문을 사용하지 않고 원소 하나 하나에 값을 따로 할당하도록 하자.


```r
r_vector <- vector("character", 5L)
r_vector[[1]] <- "Hello R!"
r_vector[[2]] <- "Hello R!"
r_vector[[3]] <- "Hello R!"
r_vector[[4]] <- "Hello R!"
r_vector[[5]] <- "Hello R!"
```

`pryr::inspect()`를 이용하여 이 vector 객체의 메모리 사용을 살펴보면, 다섯 개 문자열 원소 모두 동일한 메모리 주소를 참조하는 것을 볼 수 있다. 이에 더하여, 그 메모리 주소가 앞서 생성했던 `r_string`의 원소인 문자열의 메모리 주소와 동일함을 확인할 수 있다. R에서는 하나의 R 세션 내에서 각 고유한 문자열값이 메모리 한 곳에만 저장되며, 나중에 동일한 문자열값을 다시 생성하려 하면, 중복하여 다른 위치에 생성하는 대신, 기존에 저장된 메모리를 참조한다. 따라서 매우 긴 동일한 문자열이 여러 이름 혹은 시퀀스의 여러 위치에 중복되는 경우, R은 효율적으로 메모리를 관리하기 위해 노력한다.


```r
pryr::inspect(r_vector)
```

```
## <STRSXP 0x7fe2e03b2e48>
##   <CHARSXP 0x7fe2e0347148>
##   attributes: 
##     <CHARSXP 0x7fe307135340>
##   [CHARSXP 0x7fe2e0347148]
##   [CHARSXP 0x7fe2e0347148]
##   [CHARSXP 0x7fe2e0347148]
##   [CHARSXP 0x7fe2e0347148]
```






