# 파이썬의 array, 그리고 R의 vector {#array-vector}

모든 원소가 동일한 형태인 시퀀스 자료 구조를 생각해 보자. 파이썬에서 tuple이나 list는 각 원소의 형태가 다름을 허용하지만, array(보다 정확하게는 `array.array`)는 모든 원소의 형태가 동일해야 한다. 이 때, 원소의 형태는 C에서 제공하는 기본 형태인 정수, 실수, 문자 등이다. 파이썬의 array는 mutable 객체로, modify-in-place를 지원한다.

필자는 파이썬의 array와 가장 흡사한 R의 자료 구조는 vector(atomic vector)라고 생각한다. R의 vector에서도 모든 원소가 하나의 자료 형태여야 한다. 이 때, 원소의 대표적인 자료 형태는 R에서 기본적으로 제공하는 logical, 정수, 실수, 문자열 등이다. R의 vector는 R의 list와 마찬가지 이유로 mutable 객체라 할 수 있다.

하지만, 파이썬의 array와 R의 vector 간에는 몇 가지 중요한 차이점도 존재한다. 예를 들어,

- 현재 시퀀스의 원소 형태와 다른 원소값을 입력하려 하는 경우, 파이썬의 array는 오류를 반환하는 반면, R은 자동으로 type coercion을 수행한다.
- 원소가 문자 형태인 경우, 파이썬의 array는 원소 당 하나의 문자만 지닐 수 있는 반면, R의 vector는 문자열을 원소로 지닐 수 있다.

다음에서 예시를 통해 보다 자세히 살펴보기로 하자.


## 파이썬의 array

파이썬의 array는 파이썬의 standard library에 속한 `array` 모듈을 사용하여 생성한다.


```python
from array import array
py_array = array('l', range(1, 11))
py_array
```

```
## array('l', [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
```


### Mutability

이 때, 원소의 값을 수정하더라도 array의 메모리 주소가 변하지 않음을 확인하자.


```python
id(py_array)
```

```
## 4639248880
```

```python
py_array[0] = 0
id(py_array)
```

```
## 4639248880
```


### 다른 타입의 원소 입력하기

앞에서 `py_array`는 정수(`'l'`) 형태의 원소를 지닌 array로 생성하였다. 여기에 정수가 아닌 원소를 입력하려 하면 어떻게 될까? 실험을 위해 첫 번째 원소에 실수(`'f'`) 형태의 값인 `1.0`을 입력하려 시도하면 오류가 발생할 것이다.


```python
py_array[0] = 1.0
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: integer argument expected, got float
```


### 원소 추가하기

파이썬의 list와 마찬가지로, 파이썬의 array 또한 필요보다 많은 메모리를 미리 할당하여, 원소가 추가될 때마다 매번 메모리 할당에 컴퓨팅을 소요하지 않도록 하였다.


```python
print(sys.getsizeof(py_array))
```

```
## 192
```

```python
for x in range(11, 31):
  py_array.append(x)
  print(f'Number of elements: {len(py_array)}, memory size: {sys.getsizeof(py_array)}')
```

```
## Number of elements: 11, memory size: 192
## Number of elements: 12, memory size: 192
## Number of elements: 13, memory size: 192
## Number of elements: 14, memory size: 192
## Number of elements: 15, memory size: 192
## Number of elements: 16, memory size: 192
## Number of elements: 17, memory size: 264
## Number of elements: 18, memory size: 264
## Number of elements: 19, memory size: 264
## Number of elements: 20, memory size: 264
## Number of elements: 21, memory size: 264
## Number of elements: 22, memory size: 264
## Number of elements: 23, memory size: 264
## Number of elements: 24, memory size: 264
## Number of elements: 25, memory size: 264
## Number of elements: 26, memory size: 336
## Number of elements: 27, memory size: 336
## Number of elements: 28, memory size: 336
## Number of elements: 29, memory size: 336
## Number of elements: 30, memory size: 336
```

앞의 예에서, 원소들이 추가된 이후에도 `py_array`의 메모리 주소는 변하지 않는다.


```python
id(py_array)
```

```
## 4639248880
```


### 문자열 시퀀스

파이썬에서 각 원소가 하나의 문자를 지닌 array는 다음과 같이 생성할 수 있다.


```python
array('u', ['a', 'b', 'c'])
```

```
## array('u', 'abc')
```

하지만, 각 원소가 문자열인 array는 생성할 수 없다.


```python
array('u', ['abc', 'def'])
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: array item must be unicode character
```



## R의 vector

R에서 atomic vector는 흔히 `c()` 함수를 사용하여 생성하지만, 여기에서는 보다 명시적으로 데이터 타입을 보여주기 위해서 `as.vector()`를 사용하자.


```r
r_vector <- as.vector(1:10, mode = "integer")
typeof(r_vector)
```

```
## [1] "integer"
```

```r
print(r_vector)
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
```

`pryr::inspect()`를 통해 vector의 메모리 구조를 살펴보자.


```r
pryr::inspect(r_vector)
```

```
## <INTSXP 0x7f8b8f6d20e0>
```

List와는 달리, vector에서는 각 원소가 다른 메모리 영역을 참조하는 것이 아니라 실제 원소값을 지니기 때문에, 각 원소값에 해당하는 메모리 주소가 따로 출력되지 않는 것을 볼 수 있다.



### Mutability

이 vector가 저장된 메모리 주소를 `pryr::address()`로 출력해 보자. 또한, 동일한 vector 객체가 또 다른 이름으로 참조되고 있지 않은지를 보기 위해 `pryr::refs()`를 함께 호출해 보자. 이 값이 1인 경우(single binding)에는 modify-in-place 방식이 작동하여 원소값 변경 시 vector의 메모리 주소가 변경되지 않지만, 2 이상인 경우에는 copy-on-modify 방식이 작동하여 vector가 다른 곳에 복사된 뒤 값이 변경된다. 


```r
c(pryr::address(r_vector), pryr::refs(r_vector))
```

```
## [1] "0x7f8b8f6d20e0" "65535"
```


이후, 첫 번째 원소의 값을 변경하려 시도해 보자. 이 때, 만약 앞에서 `pryr::refs(r_vector)`의 값이 1보다 큰 수였다면, 메모리 주소가 변경될 것이다.


```r
r_vector[[1]] <- 0L
r_vector[[1]]
```

```
## [1] 0
```

```r
c(pryr::address(r_vector), pryr::refs(r_vector))
```

```
## [1] "0x7f8b9a885c38" "1"
```

앞의 결과에서 원소값 변경 이후 `pryr::refs(r_vector)`의 값이 1이었다면, 다시금 첫 번째 원소의 값(혹은 어떤 원소의 값이든)을 변경할 때 `r_vector`의 메모리 주소는 동일하게 유지될 것이다.


```r
r_vector[[1]] <- 1L
r_vector[[1]]
```

```
## [1] 1
```

```r
c(pryr::address(r_vector), pryr::refs(r_vector))
```

```
## [1] "0x7f8b9a885c38" "1"
```

이는 R의 vector 또한 modify-in-place를 지원하는 mutable object라는 것을 보여준다.

**주의: RStudio IDE 상에서 원소값을 변경할 때는 list와 마찬가지로 추가적으로 발생하는 참조로 인해 `pryr::refs()` 값이 항상 1보다 클 수 있기 때문에, mutability는 인터랙티브 개발 환경으로부터 독립된 프로세스에서 더 잘 확인할 수 있을 것이다.**


### 다른 타입의 원소 입력하기

앞서 파이썬의 경우와는 달리, R의 vector는 현재 지정된 형태와 다른 원소가 입력되었을 때 자동으로 type coercion을 수행한다. 예를 들어, 첫 번째 원소의 값으로 정수 `1L`이 아닌 실수 `1.0`을 입력할 때, R에서 미리 지정된 type coercion logic에 따라 vector의 모든 원소가 실수(double)로 변경될 것이다.


```r
r_vector[[1]] <- 1.0
typeof(r_vector)
```

```
## [1] "double"
```

이 경우에는 vector 전체가 새로운 메모리 주소에 할당될 것이다.


```r
c(pryr::address(r_vector), pryr::refs(r_vector))
```

```
## [1] "0x7f8b8ab05628" "1"
```

또한 `pryr::inspect()`를 호출하였을 때 C 객체 타입이 앞서 INTSXP에서 REALSXP로 변경되었음을 확인할 수 있다.


```r
pryr::inspect(r_vector)
```

```
## <REALSXP 0x7f8b8ab05628>
```



### 원소 추가하기

R의 list과 마찬가지로, R의 vector는 기존에 존재하지 않는 position에 원소를 추가하는 것처럼 보이게 코드를 작성할 수 있지만, 실제로는 새로운 메모리 주소에 vector를 생성하는 작업이 진행된다. 다음과 같이 원소를 하나씩 추가하며 메모리 주소와 할당된 메모리 크기가 어떻게 바뀌는지 살펴보자.


```r
for (x in 11:30) {
  r_vector[[x]] <- x
  print(
    glue::glue(
      "Number of elements: {length(r_vector)}",
      "memory address: {pryr::address(r_vector)}",
      "memory size: {pryr::object_size(r_vector)}",
      .sep = ", "
    )
  )
}
```

```
## Number of elements: 11, memory address: 0x7f8b8c4fc808, memory size: 176
## Number of elements: 12, memory address: 0x7f8b8aaedf78, memory size: 176
## Number of elements: 13, memory address: 0x7f8b8aaed948, memory size: 176
## Number of elements: 14, memory address: 0x7f8b8aaed5d8, memory size: 176
## Number of elements: 15, memory address: 0x7f8b8aaecc38, memory size: 176
## Number of elements: 16, memory address: 0x7f8b8aaec608, memory size: 176
## Number of elements: 17, memory address: 0x600003dc6d00, memory size: 184
## Number of elements: 18, memory address: 0x600003dc6b80, memory size: 192
## Number of elements: 19, memory address: 0x6000033ce560, memory size: 200
## Number of elements: 20, memory address: 0x6000031a72c0, memory size: 208
## Number of elements: 21, memory address: 0x6000031a71e0, memory size: 216
## Number of elements: 22, memory address: 0x6000037aa490, memory size: 224
## Number of elements: 23, memory address: 0x6000037aa670, memory size: 232
## Number of elements: 24, memory address: 0x6000034a8d00, memory size: 240
## Number of elements: 25, memory address: 0x6000034a9000, memory size: 248
## Number of elements: 26, memory address: 0x7f8ba9fec5a0, memory size: 256
## Number of elements: 27, memory address: 0x7f8ba9ff4ae0, memory size: 264
## Number of elements: 28, memory address: 0x7f8b8c94cd90, memory size: 272
## Number of elements: 29, memory address: 0x7f8b8c94a8e0, memory size: 280
## Number of elements: 30, memory address: 0x7f8b8c94ba60, memory size: 288
```

원소 개수가 하나씩 증가할 때마다 메모리 주소가 변경되며 메모리 크기가 8 byte(double 형태의 데이터의 크기)씩 증가함을 확인할 수 있을 것이다. 매번 메모리 재할당 및 복사 작업으로 인해, R vector의 원소를 추가할 때마다 필요한 작업량이 파이썬의 array를 사용할 때보다 더 많을 것이며, vector의 길이가 길수록 그 차이가 더 커지게 될 것이다.


### 문자열 시퀀스

R에서는 각 원소가 문자열인 vector를 각 원소가 하나의 문자인 vector를 생성할 때와 마찬가지 방식으로 생성할 수 있다.


```r
r_char_vec <- c("a", "b", "c")
typeof(r_char_vec)
```

```
## [1] "character"
```

```r
r_char_vec
```

```
## [1] "a" "b" "c"
```


```r
r_str_vec <- c("abc", "def")
typeof(r_str_vec)
```

```
## [1] "character"
```

```r
r_str_vec
```

```
## [1] "abc" "def"
```

`typeof()` 결과에서 보이듯이, 두 가지 경우 모두 R에서는 "character" 형태의 원소를 지닌 vector로 인식한다. 다시 말해, R에서는 문자열의 길이와 상관이 없이 문자형의 데이터를 "character" 형태라고 한다.

이 두 객체에 대해 `pryr::inspect()`를 호출하면, list에서와 같이 각 원소에 해당하는 메모리 주소가 출력되는 것을 볼 수 있다. 이는 각 원소에 실제 원소값을 저장한 다른 vector와는 달리, 문자열 vector는 list와 마찬가지로 각 원소에 실제 원소값에 대한 주소값을 지니며, 다만 list와는 달리 그 원소값의 형태가 문자열(C에서는 CHARSXP 객체)로 제한된다는 점을 의미한다고 하겠다. 


```r
pryr::inspect(r_char_vec)
```

```
## <STRSXP 0x7f8b8af59c08>
##   <CHARSXP 0x7f8bba2d1148>
##   <CHARSXP 0x7f8bba683ae8>
##   <CHARSXP 0x7f8bba00e0c0>
```

```r
pryr::inspect(r_str_vec)
```

```
## <STRSXP 0x7f8b98085bc8>
##   <CHARSXP 0x7f8b9877b438>
##   <CHARSXP 0x7f8bba9428a8>
```

문자열에 대해서는 추후 기회가 되면 별도로 다시 다루기로 하자.



## 파이썬 NumPy

파이썬의 array와 가장 유사한 R의 자료 구조는 vector라 하겠지만, R의 vector와 가장 유사한 파이썬의 자료 구조는 array.array보다는 numpy.array라 하는 것이 더 적합할 것 같다. NumPy는 파이썬의 기본 라이브러리가 아니라 추가로 설치해야 하는 라이브러리이지만, 대부분의 코딩에서는 연산을 위해 numpy.array를 더 많이 사용할 것이다. 이에 대해서는 추후 기회가 되면 다시 살펴보기로 하자.

