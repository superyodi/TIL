# issue 기반 버전 관리



[이슈 작성 - 개발 - 이슈 해결 ] 이런 흐름으로 프로젝트를 진행해 나가려 한다.

github의 issue, milestone, project 기능을 제대로 사용하고 싶었고 1인 프로젝트여서 관리가 소홀해지다 보니 이러한 버전 관리 방식을 채택하게 되었다.



</dir>

### 커밋에 이슈번호 달기

개발하고자 하는 내용을 github repo에서 issue로 발행하고

로컬에서 해당 issue에 맞게 개발한 후에 issue가 해결되었음을 다시 github에 알리도록 하기 위함 

이런 방식은 개발자의 작업 목적을 분명히 하며, 프로젝트 진행상황을 손쉽게 관리할 수 있다는 장점이 있다. 



#### How to?

commit message에 #(issue number) 붙이기 



```
{동작} #번호 - 내용
```

이 포맷으로 작성한 제목 예시

```
Refactor #28 - getPersonID()
Test #411 - Company class and its public methods
Implement #58 - Company 클래스 메소드 3개
```



</dir>

### 커밋 메세지로 github issue 자동 종료하기



```
키워드 #번호
```

+ 키워드 목록
  + close
  + closes
  + closed
  + fix
  + fixes
  + fixed
  + resolve
  + resolves
  + resolved



</dir>

이 포멧으로 작성한 commit message 예시

```
# 제목에 이슈 한 개 닫기를 적용한 사례
Close #31 - refactoring wrap-up

* This is wrap-up of refactoring main code.
* main.c
  * removed old comments
  * fixed rest indentations
  * method extraction at line no. 35


# 본문에 이슈 여러 개 닫기를 적용한 사례
Update policy 16/04/02

* This closes #128 - cab policy, closes #129 - new hostname, and fixes #78 - bug on logging.
* cablist.txt: changed ACL due to policy update delivered via email on 16/04/02, @mr.parkyou
* hostname.properties: cab hostname is updated
* BeautifulDeveloper.java: logging problem on line no. 78 is fixed. The `if` statement is never happening. This deletes the `if` block.
```

이렇게 커밋을 작성하여 푸시하면, 푸시한 브랜치에 따라 이슈가 자동으로 닫힙니다. 예를 들어 Github 저장소의 default branch를 `master`로 설정해뒀을 경우, `master` 브랜치에 커밋 후 푸시하면 즉시 해당 번호의 이슈가 닫힙니다. `develop` 브랜치에 푸시했다면, 이슈는 닫히지 않고 있다가 나중에 `develop -> master Merge`가 되었을 때 알아서 닫힙니다.



</dir>

---



*참고 블로그*

+ [github 하나로 1인 개발 워크플로우 완성하기: 실전 편](https://www.huskyhoochu.com/issue-based-version-control-201/#review)
+ [좋은 git 커밋 메시지를 작성하기 위한 8가지 약속](https://djkeh.github.io/articles/How-to-write-a-git-commit-message-kor/)

