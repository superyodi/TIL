# Git 정리



### Merge와 Rebase 차이

둘 다 브랜치를 통합할때 사용한다



**Merge**

<u>변경 내용의 이력이 모두 그대로 남아있기때문에 이력이 복잡해진다</u>



+ Fast-Forward(빨리 감기) : master 브랜치의 상태가 B 시점으로부터 변경이 없는 경우 

  (before)
![capture_stepup1_4_1](https://backlog.com/git-tutorial/kr/img/post/stepup/capture_stepup1_4_1.png)
  
  (after)
![capture_stepup1_4_2](https://backlog.com/git-tutorial/kr/img/post/stepup/capture_stepup1_4_2.png)
  
  

+ master 브랜치가 변경된 경우
  (before)
  ![capture_stepup1_4_3](https://backlog.com/git-tutorial/kr/img/post/stepup/capture_stepup1_4_3.png)
  
  (after)
  ![capture_stepup1_4_4](https://backlog.com/git-tutorial/kr/img/post/stepup/capture_stepup1_4_4.png)
  
  

**Rebase**

이력은 단순해지지만, 원래 커밋 이력이 변경된다. 

<u>정확한 이력을 남겨야 할 필요가 있을 경우에는 사용하면 안됨</u>


(before)
  ![capture_stepup1_4_6](https://backlog.com/git-tutorial/kr/img/post/stepup/capture_stepup1_4_6.png)

(after)



그림과 같이 master의 위치는 그대로 유지된다. 

  ![capture_stepup1_4_8](https://backlog.com/git-tutorial/kr/img/post/stepup/capture_stepup1_4_8.png)



master의 위치를 bugfix 브랜치와 같게 바꾸기 위해선
master 브랜치와 bugfix 브랜치를 **fast-foward merge**하면 된다
  ![capture_stepup1_4_9](https://backlog.com/git-tutorial/kr/img/post/stepup/capture_stepup1_4_9.png)



**상황별 rebase와 merge 사용**



+ 토픽 브랜치에 통합 브랜치의 최신 코드를 적용할 경우 --->  **rebase**
+ 통합 브랜치에 토픽 브랜치를 불러올 경우 ----> 우선 **rebase**하고 **merge**