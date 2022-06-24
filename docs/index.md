--- 
title: "Python & R"
author: "이영록"
date: "2022-06-24"
site: bookdown::bookdown_site
documentclass: book
# bibliography: [book.bib, packages.bib]
# url: your book url like https://bookdown.org/yihui/bookdown
# cover-image: path to the social sharing image like images/cover.jpg
description: |
  이 책은 Python과 R의 비슷한 점과 차이점을 예제를 통해 알아보기 위한 책이다. 
  어느 하나가 다른 하나의 우위에 있음을 보이려는 의도가 아니며, Python 사용자가 
  R을 이해하거나 R 사용자가 Python을 이해하는 데 도움을 주기 위해 작성되었다.
link-citations: yes
github-repo: youngroklee-ml/pynr
---

# Overview

이 책은 Python과 R의 비슷한 점과 차이점을 예제를 통해 알아보기 위한 책이다. 어느 하나가 다른 하나의 우위에 있음을 보이려는 의도가 아니며, Python 사용자가 R을 이해하거나 R 사용자가 Python을 이해하는 데 도움을 주기 위해 작성되었다.

특정 용도(통계 분석, 시각화, 머신 러닝 등)보다는 일반적인 프로그래밍 언어 관점에서 살펴보고자 한다. 따라서 가능한 한 기본 Python 및 R의 내에서 비교하며, R의 경우에는 필요에 따라 R을 좀 더 프로그래밍 언어적으로 사용하기 위한 패키지(rlang 등)를 필요에 따라 추가로 사용할 예정이다. 

책 작성 시 사용된 Python과 R의 버전은 다음과 같다.

## Python


```python
import sys
print(sys.version)
```

```
## 3.8.9 (default, Apr 13 2022, 08:48:07) 
## [Clang 13.1.6 (clang-1316.0.21.2.5)]
```

## R


```r
R.version$version.string
```

```
## [1] "R version 4.1.3 (2022-03-10)"
```

