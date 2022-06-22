# 파이썬의 dictionary, 그리고 R의 named list와 environment {#dictionary-named-list-environment}

다음과 같은 조건을 충족하기 위한 자료 구조를 생각해 보자.

1. 자료는 여러 원소(element)를 지닌다.
2. 각 원소는 키(key)--혹은 이름(name)--와 값(value)의 쌍으로 이루어진다.
3. 각 원소의 값은 서로 다른 형태(type)일 수 있다.

파이썬에서 이 조건들을 동시에 충족하는 대표적인 자료 구조는 dictionary이다. Tuple과 list는 조건 2를 충족하지 못한다.

R의 경우에는 list(보다 구체적으로 named list)와 environment가 세 조건을 모두 충족한다. 그렇다면, named list와 environment 중 어떤 것이 파이썬의 dictionary와 더 비슷할까? 필자는 environment가 더 비슷하다고 생각한다. 다음은 R의 environment만 지닌 파이썬의 dictionary과의 공통점이다.

- Hash를 사용하여 원소를 빨리 lookup할 수 있다.
- 원소 이름이 unique해야 한다. 
- Reference semantics를 지닌다(modify-in-place를 지원한다).
- Position을 사용한 인덱싱이 지원되지 않는다.
- 여러 원소를 한번에 읽을 수 없다.

반면, R의 list만 파이썬의 dictionary와 지닌 공통점은 다음과 같다.

- 원소가 생성된 순서가 보존된다.

여기에서 어떠한 공통점이 중요한지에 따라 파이썬의 dictionary와 대응하여 사용하기에 더 적합한 R의 자료 구조를 선택해야 할 것이다. 본 장에서는 아래와 같은 문제 상황에서 environment가 더 적합한 자료 구조임을 보일 것이다.

1. 원소가 생성된 순서는 의미가 없다.
2. 얼마나 많은 원소가 생성될 지 미리 알 수 없으며, 중간에 원소가 삭제될 수도 있는데, 이 때 처리 속도가 빨라야 한다.
3. 각 원소는 자주 읽히기 때문에, 자료를 찾는 속도가 빨라야 한다.

참고로, 파이썬의 dictionary가 R의 environment나 list보다 유연한 부분이 있다. 파이썬의 dictionary에서 key는 어떠한 immutable 객체라도 사용할 수 있는 반면, R의 environment와 named list는 모두 문자열만을 name으로 사용할 수 있다.


## Dictionary in Python

### 생성

전체 원소 갯수가 미리 정해지지 않은 경우에는 새로운 원소가 들어올 때마다 하나씩 원소를 추가해야 한다. 다음처럼 for loop를 사용하여 원소를 순차적으로 추가하는 시뮬레이션 함수를 만들자. 이 때, key 값을 "item1", "item2", ... 와 같이 지정하자.


```python
def gen_dict(n):
  ret = {}
  for x in range(1, n + 1):
    ret[f'item{x}'] = x
  return ret
  
py_dict = gen_dict(10000)
```

결과를 출력할 때, 원소가 입력된 순서가 보존되어 출력된다.


```python
list(py_dict.__iter__())[:10]
```

```
## ['item1', 'item2', 'item3', 'item4', 'item5', 'item6', 'item7', 'item8', 'item9', 'item10']
```


### Lookup

Key가 "item100"인 원소의 값을 다음과 같이 얻을 수 있다.


```python
py_dict['item100']
```

```
## 100
```

Dictionary의 경우 position을 사용할 수 없다.


```python
py_dict[99]
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): KeyError: 99
```

또한, dictionary의 경우 여러 key를 사용하여 한꺼번에 여러 원소의 값을 읽을 수 없다.


```python
py_dict[['item1', 'item2', 'item3']]
```

```
## Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: unhashable type: 'list'
```


### Reference semantics

두 개의 객체 이름이 같은 dictionary 객체에 binding되었다면, 하나의 객체 이름을 이용하여 특정 원소의 값을 변경하였을 때, reference semantics에 의해 다른 하나의 객체 이름을 이용한 참조 시에도 변경된 값이 나타난다.


```python
id(py_dict)
```

```
## 4552821568
```

```python
new_dict = py_dict
new_dict['item100'] = 0
id(new_dict)
```

```
## 4552821568
```

```python
py_dict['item100']
```

```
## 0
```




## Named list vs environment in R

위의 파이썬 예를 R에서 named list와 environment 두 가지를 이용해서 각각 구현해 보고, 결과 및 수행 시간을 비교해 보자.

### 생성

우선 named list를 생성해 보자. 아래 함수가 리스트 길이 `n`을 인자로 받기 때문에, 빈 리스트를 만든 뒤에 원소를 하나씩 추가하는 코드가 비효율적으로 보일 것이다. 하지만, 여기에서 시뮬레이션을 하려는 상황이 우리가 미리 얼마나 많은 원소가 언제 어떤 이름과 값으로 생성될 지 모르는 상황이라는 점을 기억하자. 따라서, 아래 함수는 단지 비어있는 리스트로 시작하여 원소를 하나씩 추가해야만 하는 경우 수행 시간이 얼마나 걸릴지를 시뮬레이션하기 위한 코드이다. 


```r
gen_list <- function(n) {
  ret <- list()
  for (x in seq_len(n)) {
    ret[[paste0('item', x)]] <- x
  }
  ret
}

system.time(r_list <- gen_list(10000))
```

```
##    user  system elapsed 
##   0.776   0.027   0.803
```



```r
gen_env <- function(n) {
  ret <- new.env()
  for (x in seq_len(n)) {
    ret[[paste0('item', x)]] <- x
  }
  ret
}

system.time(r_env <- gen_env(10000))
```

```
##    user  system elapsed 
##   0.029   0.000   0.030
```

이 결과에서, list에 원소를 하나씩 새로 추가하는 것(즉, 길이가 하나씩 증가하는 것)에 비해, environment에 원소를 하나씩 새로 추가하는 시간이 훨씬 짧게 소요되는 것을 확인할 수 있다. 그 차이는 원소의 갯수가 많을수록 더 커진다.


다음 코드는 원소가 입력된 순서가 보존되는지 확인하기 위한 코드이다.


```r
names(r_list)[1:10]
```

```
##  [1] "item1"  "item2"  "item3"  "item4"  "item5"  "item6"  "item7"  "item8" 
##  [9] "item9"  "item10"
```

```r
names(r_env)[1:10]
```

```
##  [1] "item7082" "item7083" "item7084" "item7085" "item7086" "item5320"
##  [7] "item7087" "item5321" "item7088" "item5322"
```

이 결과에서, list는 순서가 보존되지만, environment는 그렇지 않음을 볼 수 있다. 따라서, 원소 입력 순서를 아는 것이 중요하지 않은 경우에만 environment를 사용하는 것이 적합할 것이다.


### Lookup

Named list와 environment 모두 원소의 이름을 사용하여 원소의 값을 읽어올 수 있다.


```r
r_list[["item100"]]
```

```
## [1] 100
```

```r
r_env[["item100"]]
```

```
## [1] 100
```

하지만 그 수행 시간은 사뭇 다르다. 다음 벤치마크를 보자.


```r
bench::mark(
  r_list[["item100"]],
  r_env[["item100"]],
  time_unit = "ns"
)
```

```
## # A tibble: 2 × 6
##   expression            min median `itr/sec` mem_alloc `gc/sec`
##   <bch:expr>          <dbl>  <dbl>     <dbl> <bch:byt>    <dbl>
## 1 r_list[["item100"]] 792.    875.  1109518.        0B        0
## 2 r_env[["item100"]]   84.0   167.  6469632.        0B        0
```

수행 시간의 `median`값을 볼 때, 이 예에서 environment가 list보다 몇 배 더 빠르다는 것을 확인할 수 있다.


Environment는 파이썬의 dictionary와 마찬가지로 position을 사용하거나 여러 원소 이름을 동시에 사용하여 원소값을 읽을 수 없다.


```r
r_env[[100]]
```

```
## Error in r_env[[100]]: wrong arguments for subsetting an environment
```

```r
r_env[c("item1", "item2", "item3")]
```

```
## Error in r_env[c("item1", "item2", "item3")]: object of type 'environment' is not subsettable
```


하지만 list는 이것이 가능하다.


```r
r_list[[100]]
```

```
## [1] 100
```

```r
r_list[c("item1", "item2", "item3")]
```

```
## $item1
## [1] 1
## 
## $item2
## [1] 2
## 
## $item3
## [1] 3
```



### Reference semantics

Environment는 reference semantics을 지녀 modify-in-place 방식으로 작동한다.


```r
pryr::address(r_env)
```

```
## [1] "0x7f9ac1efd878"
```

```r
new_env <- r_env
new_env[['item100']] <- 0
pryr::address(new_env)
```

```
## [1] "0x7f9ac1efd878"
```

```r
r_env[['item100']]
```

```
## [1] 0
```


반면, list는 copy-on-modify 방식으로 작동한다.


```r
pryr::address(r_list)
```

```
## [1] "0x7f9abaa18000"
```

```r
new_list <- r_list
new_list[['item100']] <- 0
pryr::address(new_list)
```

```
## [1] "0x7f9ababb0000"
```

```r
r_list[['item100']]
```

```
## [1] 100
```



### Uniqueness

R의 named list는 name의 중복을 허용한다. 


```r
names(new_list) <- rep("item100", length(new_list))
head(new_list)
```

```
## $item100
## [1] 1
## 
## $item100
## [1] 2
## 
## $item100
## [1] 3
## 
## $item100
## [1] 4
## 
## $item100
## [1] 5
## 
## $item100
## [1] 6
```

이 경우, 원소의 이름을 사용하여 원소를 찾는 과정이 모호해진다. 원소 이름이 일치하는 첫 번째 원소만을 반환할 것이다.


```r
new_list[["item100"]]
```

```
## [1] 1
```



