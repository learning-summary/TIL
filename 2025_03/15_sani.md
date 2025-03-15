### 알고리즘 - 완전탐색

https://school.programmers.co.kr/learn/courses/30/lessons/84512

```python
from itertools import product


def solution(word):
    answer = 0
    prod = []
    target = 'AEIOU'

    # 모든 경우의 수 구하기
    for i in range(1, len(target) + 1):
        prod.extend([''.join(tup) for tup in product(target, repeat=i)])

    # 사전순 정렬
    prod.sort()

    # 순서 찾기
    answer = prod.index(word) + 1

    return answer
```
