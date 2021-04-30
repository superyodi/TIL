# Binary Search



+ Binary Search
  + 정렬된 데이터에서 특정값 K가 존재하는지 검색하는 알고리즘
  + Lower Bound
    + K가 중복되게 존재하는 정렬된 데이터에서 <u>K 이상의 값이 처음으로 나오는 위치</u>를 찾는 과정
  + Upper Bound
    + K가 중복되게 존재하는 정렬된 데이터에서 <u>K를 초과하는 값이 처음으로 나오는 위치</u>를 찾는 과정



![img](https://k.kakaocdn.net/dn/bMIAMA/btqwvrNzD8k/LYflC0V1UnMn2OFyHfX0U1/img.png)



## lower bound



```python
def find_lbound(nums, target):

    print("start")
    start, end = 0, len(nums)
    while start < end:

        print("before",start, end)
        mid = (start + end) // 2

        if nums[mid] < target:
            start = mid + 1

        else:
            end = mid

        print("after", start, end)


    return start
```



## upper bound









> 참고
>
> + https://hee96-story.tistory.com/80