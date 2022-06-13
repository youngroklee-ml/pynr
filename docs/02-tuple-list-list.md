# 파이썬의 tuple과 list, 그리고 R의 list {#tuple-list-list}

파이썬에 내장된 대표적인 시퀀스(sequence)형 자료 형태로 tuple(튜플, 투플, 터플)과 list(리스트)를 들 수 있다. 두 자료 형태 모두 위치(position)를 사용하여 원소를 읽을 수 있으며, 각 원소가 서로 다른 타입일 수 있다. 예를 들어, 첫 번째 원소는 숫자, 두 번째 원소는 문자열 등으로 구성될 수 있다.

Tuple은 흔히 immutable list로 설명되기도 하는데, 일반적으로 그 원소의 값을 modify-in-place로 변경할 수 없기 때문이다. 반면 list는 mutable object로, 기존에 만들어진 list 내에서 원소의 값을 변경하는 것이 가능하다. 보다 자세한 내용은 아래에서 살펴보기로 하자.

기존에 존재하는 원소의 변경 여부 외에, 새로운 원소를 추가할 수 있는지, 즉 기존 `n`개의 원소를 지닌 시퀀스에 `n + 1`번째 원소를 추가할 수 있는지에 대한 차이도 존재한다. Tuple의 경우 길이가 고정된 시퀀스로써 새로운 원소를 추가할 수 없지만, list의 경우에는 기존 list에 원소를 추가한다던가 기존 원소를 제거하는 등 시퀀스의 길이를 변경하게 되는 작업을 수행할 수 있다. 하지만 이것이 list가 mutable 객체라고 주장하는 근거가 되기에는 약간 한계가 있는 부분이 있는데, 이는 본문에서 예와 함께 설명하기로 한다.

필자는 이 두 가지 파이썬 자료 형태 중 list가 R의 list와 공통점이 더 많다고 생각한다. 가장 중요한 공통점은 mutable 객체라는 점이다. 즉, 파이썬의 list와 R의 list 모두 modify-in-place를 지원한다. 단, 이 때 R의 경우 single binding을 가정하자. 보다 자세한 내용은 본문에서 다루기로 하자.


## Tuple vs list in Python

파이썬에서 tuple과 list의 차이를 간단한 예를 통해 살펴보자.

### Immutable vs mutable

숫자 1부터 10까지를 각 원소로 지니는 길이 10짜리 tuple과 list를 생성해 보자.


```python
py_tuple = tuple(x for x in range(1, 11))
py_tuple
```

```
## (1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

```python
py_list = [x for x in range(1, 11)]
py_list
```

```
## [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

이후, `py_tuple`의 첫 번째 원소의 값을 변경하려 시도한다면, 오류를 얻게 된다. 이는 tuple이 한 번 생성된 후에는 원소의 값을 변경할 수 없는 immutable 객체이기 때문이다.


```python
py_tuple[0] = 0
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: 'tuple' object does not support item assignment
```

반면, list의 경우에는 첫 번째 원소(뿐만 아니라 어느 위치의 원소이든)의 값을 변경할 수 있다. 이 때, 내부적으로 새로운 list를 생성하는 것이 아니라, 기존 list의 원소값만 변경하게 된다. 이를 확인하기 위해 원소값 변경 전과 후의 `py_list`의 메모리 위치를 `id(py_list)`로 출력해 보자.


```python
py_list[0]
```

```
## 1
```

```python
id(py_list)
```

```
## 4583487680
```

```python
py_list[0] = 0
py_list[0]
```

```
## 0
```

```python
id(py_list)
```

```
## 4583487680
```

위 결과와 같이, 원소의 값을 변경하더라도 list 객체의 메모리 위치는 변함이 없는 modify-in-place 방식을 지원한다.


#### Mutable element of tuple

여기에서 tuple의 immutability를 얘기할 때 조심해야 할 부분이 있다. 만약 tuple의 어떤 원소가 list와 같은 mutable 객체라면, 이 mutable 원소의 원소값을 수정할 수 있다. 다음 예에서, tuple `a`의 두 번째 원소인 list의 첫 번째 원소를 변경할 수 있다.


```python
a = (0, [1, 2])
a
```

```
## (0, [1, 2])
```

```python
a[1][0] = 0
a
```

```
## (0, [0, 2])
```

이는 tuple이 원소의 값 자체가 아니라, 원소의 값이 저장된 위치를 참조하는 reference를 지니기 때문이다. Tuple의 각 원소의 값이 저장된 위치를 변경할 수는 없지만, 그 원소가 mutable 객체라면 그 원소의 값 자체를 변경할 수는 있는 것이다.

이 부분은 본 장에서 핵심적인 부분은 아니기 때문에 여기까지만 설명하기로 하자.


### Append items

시퀀스에 새로운 11번째 원소를 추가하고 싶다고 생각해 보자. 

Tuple의 경우 길이가 정해진 시퀀스이기 때문에 새로운 원소를 이후에 추가할 수가 없다. 만약 꼭 추가해야 한다면, 새로운 tuple을 생성하는 방법 밖에는 없다. 이 경우, 새로운 tuple이 생성되었음을 `id()` 함수를 통해 확인할 수 있다.


```python
id(py_tuple)
```

```
## 4583468736
```

```python
py_tuple = py_tuple + (11, )
py_tuple[10]
```

```
## 11
```

```python
id(py_tuple)
```

```
## 4583642816
```

반면, list의 경우 `append()`를 사용하여 기존의 list에 원소를 추가할 수 있다.


```python
id(py_list)
```

```
## 4583487680
```

```python
py_list.append(11)
py_list[10]
```

```
## 11
```

```python
id(py_list)
```

```
## 4583487680
```

이 예에서 `py_list`의 메모리 주소는 변하지 않았다. 이는 마치 list가 mutable 객체이기 때문인 것으로 보이지만, 필자는 이는 절반만 맞는 설명이라 하겠다. 실제로는, 파이썬은 list를 생성할 때, 이후에 추가될 수 있는 원소를 위한 공간을 미리 어느 정도 남겨둔다. 따라서, 그 남겨둔 공간까지는 원소를 추가하더라도 list에 할당된 메모리 공간이 변하지 않지만, 그 범위를 넘어서는 순간 list에 메모리 공간이 재할당된다. 이 재할당 작업을 너무 자주 수행하지 않기 위해 미리 당장 필요하지 않은 메모리를 확보해 두는 것이다. 다음 예는 list의 메모리 공간이 매 원소 추가 시마다 증가하는 것이 아니라 이따금 한 번에 증가한다는 것을 보여준다. 


```python
print(sys.getsizeof(py_list))
```

```
## 184
```

```python
for x in range(12, 31):
  py_list.append(x)
  print(f'Number of elements: {len(py_list)}, memory size: {sys.getsizeof(py_list)}')
```

```
## Number of elements: 12, memory size: 184
## Number of elements: 13, memory size: 184
## Number of elements: 14, memory size: 184
## Number of elements: 15, memory size: 184
## Number of elements: 16, memory size: 184
## Number of elements: 17, memory size: 256
## Number of elements: 18, memory size: 256
## Number of elements: 19, memory size: 256
## Number of elements: 20, memory size: 256
## Number of elements: 21, memory size: 256
## Number of elements: 22, memory size: 256
## Number of elements: 23, memory size: 256
## Number of elements: 24, memory size: 256
## Number of elements: 25, memory size: 256
## Number of elements: 26, memory size: 336
## Number of elements: 27, memory size: 336
## Number of elements: 28, memory size: 336
## Number of elements: 29, memory size: 336
## Number of elements: 30, memory size: 336
```

**주의: `sys.getsizeof()`는 리스트의 각 원소가 참조하는 값에 할당된 메모리를 포함하지 않고, 단지 리스트 자체(각 원소의 주소값을 저장하는 시퀀스)에 할당된 메모리 크기만 나타낸다.**

이 예에서 `py_list`의 메모리 주소는 이전의 메모리 주소와 여전히 동일하다.


```python
id(py_list)
```

```
## 4583487680
```


## List in R

R에 내장된 대표적인 시퀀스(sequence)형 자료 형태로는 vector와 list가 있는데, 이 중 파이썬의 tuple이나 list와 같이 각 원소의 형태가 다른 시퀀스를 제공하는 R의 시퀀스는 list이다.

위 파이썬의 예와 마찬가지로, 1에서 10까지의 원소값을 지니는 길이 10의 list를 생성해 보자.


```r
r_list <- as.list(1:10)
r_list
```

```
## [[1]]
## [1] 1
## 
## [[2]]
## [1] 2
## 
## [[3]]
## [1] 3
## 
## [[4]]
## [1] 4
## 
## [[5]]
## [1] 5
## 
## [[6]]
## [1] 6
## 
## [[7]]
## [1] 7
## 
## [[8]]
## [1] 8
## 
## [[9]]
## [1] 9
## 
## [[10]]
## [1] 10
```


### Mutable object

이 list가 저장된 메모리 주소를 `pryr::address()`로 출력해 보자. 또한, 동일한 list 객체가 또 다른 이름으로 참조되고 있지 않은지를 보기 위해 `pryr::refs()`를 함께 호출해 보자. 이 값이 1인 경우(single binding)에는 modify-in-place 방식이 작동하여 원소값 변경 전/후의 list의 메모리 주소가 변경되지 않지만, 2 이상인 경우에는 copy-on-modify 방식이 작동하여 원소값 변경 시 list가 항상 다른 곳에 복사된 뒤 값이 변경된다. 


```r
c(pryr::address(r_list), pryr::refs(r_list))
```

```
## [1] "0x7fd0c48ad828" "5"
```


이후, 첫 번째 원소의 값을 변경하려 시도해 보자. 이 때, 만약 앞에서 `pryr::refs(r_list)`의 값이 1보다 큰 수였다면, 메모리 주소가 변경될 것이다.


```r
r_list[[1]] <- 0L
r_list[[1]]
```

```
## [1] 0
```

```r
c(pryr::address(r_list), pryr::refs(r_list))
```

```
## [1] "0x7fd0c4846178" "1"
```

앞의 결과에서 원소값 변경 이후 `pryr::refs(r_list)`의 값이 1이었다면, 다시금 첫 번째 원소의 값(혹은 어떤 원소의 값이든)을 변경할 때 `r_list`의 메모리 주소는 동일하게 유지될 것이다.


```r
r_list[[1]] <- 1L
r_list[[1]]
```

```
## [1] 1
```

```r
c(pryr::address(r_list), pryr::refs(r_list))
```

```
## [1] "0x7fd0c4846178" "1"
```

이는 R의 list가 파이썬의 list처럼 modify-in-place를 지원하는 mutable object라는 것을 보여준다.

**주의: RStudio IDE 상에서 원소값을 변경할 때는 항상 copy-on-modify 방식이 작동하는데, 이는 RStudio IDE 내의 Environment 창에서 해당 list 객체를 추가로 참조하여 `pryr::refs()` 값이 항상 1보다 크기 때문이다. 따라서, mutable update는 인터랙티브 개발 환경으로부터 독립된 프로세스에서 작동할 것이다.**


### Append items

먼저, `r_list`의 내부 구조를 살펴보기 위해 `pryr::inspect()`를 호출해 보자.


```r
pryr::inspect(r_list)
```

```
## <VECSXP 0x7fd0c4846178>
##   <INTSXP 0x7fd0c1de8070>
##   <INTSXP 0x7fd0c110fc48>
##   <INTSXP 0x7fd0c110fc10>
##   <INTSXP 0x7fd0c110fbd8>
##   <INTSXP 0x7fd0c110fba0>
##   <INTSXP 0x7fd0c110fb68>
##   <INTSXP 0x7fd0c110fb30>
##   <INTSXP 0x7fd0c110faf8>
##   <INTSXP 0x7fd0c110fac0>
##   <INTSXP 0x7fd0c110fa88>
```

이 결과는 `r_list`가 VECSXP라는 형태(list)의 C 객체이며, 그 각 원소는 INTSXP라는 형태(integer vector)의 C 객체임을 보여주고, VECSXP 객체와 각 INTSXP 객체가 저장된 메모리 주소를 보여준다.

파이썬의 list에서 새로운 원소를 추가하기 위해 `append()` method를 사용했던 것과 달리, R의 list에서는 기존 원소를 변경하는 것과 동일한 방법으로 새로운 원소를 추가할 수 있다.


```r
r_list[[11]] <- 11L
r_list[[11]]
```

```
## [1] 11
```

단, 이 때는 modify-in-place가 아닌 copy-on-modify 방식이 작동한다는 것을 인지하자. 다음에서 메모리 주소가 이전과 변경되었음을 확인할 수 있을 것이다.


```r
c(pryr::address(r_list), pryr::refs(r_list))
```

```
## [1] "0x7fd0b7c826d8" "1"
```

좀 더 자세히 살펴보기 위해 `pryr::inspect()`를 다시 호출해 보자.


```r
pryr::inspect(r_list)
```

```
## <VECSXP 0x7fd0b7c826d8>
##   <INTSXP 0x7fd0c1de8070>
##   <INTSXP 0x7fd0c110fc48>
##   <INTSXP 0x7fd0c110fc10>
##   <INTSXP 0x7fd0c110fbd8>
##   <INTSXP 0x7fd0c110fba0>
##   <INTSXP 0x7fd0c110fb68>
##   <INTSXP 0x7fd0c110fb30>
##   <INTSXP 0x7fd0c110faf8>
##   <INTSXP 0x7fd0c110fac0>
##   <INTSXP 0x7fd0c110fa88>
##   <INTSXP 0x7fd0c5173f28>
```

VECSXP 객체의 메모리 주소는 변경된 반면, 하나의 추가된 INTSXP 객체를 제외하면, 기존에 있던 INTSXP 객체의 메모리 주소는 여전히 동일함을 보여준다. 그리고, `pryr::address(r_list)`가 보여주는 주소값은 VECSXP 객체의 주소값임을 보여준다.

원소를 계속 하나씩 추가하면서, list 메모리 위치가 어떻게 바뀌는지 살펴보자.


```r
for (x in 12:30) {
  r_list[[x]] <- x
  print(
    glue::glue(
      "Number of elements: {length(r_list)}",
      "memory address: {pryr::address(r_list)}",
      .sep = ", "
    )
  )
}
```

```
## Number of elements: 12, memory address: 0x7fd0b7cd0778
## Number of elements: 13, memory address: 0x7fd0c4b99338
## Number of elements: 14, memory address: 0x7fd0c4b991d8
## Number of elements: 15, memory address: 0x7fd0c4b99078
## Number of elements: 16, memory address: 0x7fd0c4b98f18
## Number of elements: 17, memory address: 0x600002680b40
## Number of elements: 18, memory address: 0x600002680c00
## Number of elements: 19, memory address: 0x600002886ff0
## Number of elements: 20, memory address: 0x600002ad55e0
## Number of elements: 21, memory address: 0x600002ad55e0
## Number of elements: 22, memory address: 0x600002cef2a0
## Number of elements: 23, memory address: 0x600002cef2a0
## Number of elements: 24, memory address: 0x600002fddf00
## Number of elements: 25, memory address: 0x600002fddf00
## Number of elements: 26, memory address: 0x7fd0d27c4ee0
## Number of elements: 27, memory address: 0x7fd0d27c4ee0
## Number of elements: 28, memory address: 0x7fd0c204ccf0
## Number of elements: 29, memory address: 0x7fd0c204ccf0
## Number of elements: 30, memory address: 0x7fd0c204dac0
```

R list의 경우, 원소 개수가 하나씩 증가할 때마다 메모리 주소가 변경됨을 확인할 수 있을 것이다. 즉, list의 길이가 변경될 때마다 매번 새로 list를 위한 메모리가 재할당되기 때문에 python의 list보다 컴퓨팅 리소스가 좀 더 소모되며, 그 정도는 list의 길이가 길수록 증가할 것이다. 다만, 앞에서 살펴본 바와 같이 VECSXP 객체의 메모리는 재할당되는 반면, 각 원소값이 저장된 메모리는 그대로 유지되어 참조되므로, 추가적인 컴퓨팅은 제한적이라 하겠다.



