# Scalar

여러 개의 값의 시퀀스가 아니라, 오직 하나의 값만을 지니는 객체를 생각해 보자. 이러한 객체를 생성하는 데는 두 가지 방법을 생각해 볼 수 있다. 별도의 자료구조를 사용하는 방법과, 단순히 길이 1인 시퀀스를 생성하는 방법이다. 전자의 경우 시퀀스의 특성을 제거한 자료구조를 상상해 볼 수 있을 것이며, 후자의 경우 시퀀스 특성이 객체에 여전히 존재할 것이라 상상해 볼 수 있을 것이다.

파이썬에서는 전자의 경우에 해당하는 자료구조가 존재하는 반면, R에서는 후자의 방법을 따른다. 보다 자세한 내용은 예제와 함께 보도록 하자.


## 파이썬

파이썬에서는 논리형, 정수형, 실수형 값 각각은 별도의 class 객체이며, 시퀀스가 아니다. 즉, 길이 1의 array가 아니라, 별개의 scalar 객체이다.

우선 논리형을 살펴보자.


```python
py_bool = True
type(py_bool)
```

```
## <class 'bool'>
```

여기에서 `py_bool`이 시퀀스 객체가 아님을 간단하게 확인하기 위해 함수 `len()`을 호출해 보자. 


```python
len(py_bool)
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: object of type 'bool' has no len()
```

이 호출은 오류를 반환할텐데, 왜냐하면 bool class에는 시퀀스 클래스들과는 달리 special method `__len__()`가 정의되어 있지 않기 때문이다.

다음으로 정수형을 살펴보자.


```python
py_int = 7
type(py_int)
```

```
## <class 'int'>
```

정수형 int class 역시 `__len__()` method를 지니지 않는다.


```python
len(py_int)
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: object of type 'int' has no len()
```

또한, 다음과 같은 indexing도 적용되지 않는다. 물론, scalar에 이러한 indexing은 효용성이 없지만, 오류가 반환된다는 점이 중요하다.


```python
py_int[0]
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: 'int' object is not subscriptable
```


이와 달리, 길이가 1인 정수형 array를 생성해 보면, 이는 array class 객체이며 `len()`를 호출할 때 길이 1을 반환한다. 또한, indexing이 지원된다.


```python
from array import array
py_int_array = array('l', [7])
type(py_int_array)
```

```
## <class 'array.array'>
```

```python
len(py_int_array)
```

```
## 1
```

```python
py_int_array[0]
```

```
## 7
```

따라서, Python을 사용할 때 각 논리형, 정수형, 실수형 값은 길이 1인 array가 아니라 array의 특성을 지니지 않은 개별 값임을 기억하자.


## R

R에서 논리형, 정수형, 실수형 값 각각은 길이 1인 vector로 표현된다. 따라서, `is.vector()`에 인자로 넘기면 TRUE를 반환할 것이며, `length()` 함수를 호출하면 길이 1을 반환할 것이다.

우선 논리형에 대해 확인해 보자.


```r
r_bool <- TRUE
is.vector(r_bool)
```

```
## [1] TRUE
```

```r
length(r_bool)
```

```
## [1] 1
```

다음으로 정수형에 대해 확인해 보자.


```r
r_int <- 7L
is.vector(r_int)
```

```
## [1] TRUE
```

```r
length(r_int)
```

```
## [1] 1
```

따라서, R의 "scalar"는 파이썬의 scalar형 객체가 아닌 파이썬의 array(길이 1인)와 유사하다고 생각할 수 있다.  이는 앞 장에서 파이썬의 array와 R의 vector의 유사성을 살펴보았던 것과 일관된다.


