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





