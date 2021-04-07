# Python 모듈 정리



## import collections



### defaultdict

일반 딕셔너리 경우 존재하지 않는 key를 호출하면 에러가 난다

defaultdict을 활용하면 선언할때 할당한 type으로 자동 초기화된다



```python
>>> from collections import defaultdict
>>> int_dict = defaultdict(int)

>>> int_dict
defaultdict(<class 'int'>, {})
>> int_dict['key1']
0 # 0으로 자동 초기화

>> tmp_dict = defaultdict(4)
# 초기화할때 명시적으로 적어놓으면 그 값이 지정된다.
```

