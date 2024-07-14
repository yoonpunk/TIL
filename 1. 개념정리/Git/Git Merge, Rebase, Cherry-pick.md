# Git Merge, Rebase, Cherry-pick

## Merge

Merge는 두 가지 방식이 있다.

* fast-forward 방식:
아래 사진과 같이 master에서 hotfix를 merge 하는 경우이다.   
이 경우에는 head(master)가 merge 대상인 hotfix의 조상이므로 master의 위치를 hotfix로 옮기기만 하면 된다.
    
    ![fast-forward1](https://github.com/yoonpunk/images/blob/main/fast-forward1.png?raw=true)
    
    따라서 아래와 같이 별도의 commit을 생성하지 않고 최신 commit으로만 이동하는 방식을 fast-forward 방식이라고 한다.
    
    ![fast-forward2](https://github.com/yoonpunk/images/blob/main/fast-forward2.png?raw=true)
    
* 3-Way Merge:
아래와 같이 head와 merge의 대상이 조상-자손의 관계가 아닐 때 merge 하는 경우이다. 이 경우에는 공통의 조상 commit과 head 그리고 merge 대상 3개의 commit을 사용하여 3-Way Merge를 한다.
    
    ![3-way-merge1](https://github.com/yoonpunk/images/blob/main/3-way-merge2.png?raw=true)
    
    이 경우에는 3개의 commit 내용이 병합된 새로운 commit을 만들고 head가 새로운 commit을 가리키게 한다.

     ![3-way-merge1](https://github.com/yoonpunk/images/blob/main/3-way-merge.png?raw=true)

## Rebase

Rebase는 한 branch를 다른 branch로 합치는 방법 중 하나로, Merge와는 다르게 현재의 branch의 base를 다른 branch로 변경하며 병합하는 방법이다.

![rebase](https://github.com/yoonpunk/images/blob/main/rebase.png?raw=true)

위 그림과 같이 Feature branch의 base를 Matser branch와의 공통 base에서 master branch의 마지막 commit으로 재설정하여 하나의 branch로 합치는 방법이 rebase다.

Feature branch로 진행된 2개의 commit을 Master branch의 최종 commit 이후의 새로운 commit으로 재 commit 하는 과정을 거친다.   
(Feature branch의 기존 commit 2개는 rebase된 이후의 2개의 commit과는 다르다)

이를 통해 Merge와는 다르게 rebase는 commit history를 선형적으로 만든다.

이미 push된 commit에 대해선 rebase를 하지 말자. rebase는 기존 commit을 변경하여 재 commit하는 것으로, 이미 push가 되어 공개된 commit을 사용하는 다른 개발자에게 문제를 발생시킬  수 있기 때문이다.  


## Merge vs Rebase

* Merge: branch를 병합하는 것으로 기존 branch를 유지한 채 다른 branch의 변경 내용을 통합한다. 따라서, 두 branch의 변경 내용을 하나의 새로운 commit으로 합쳐 이전 branch와 새로운 branch의 변경 내용을 둘 다 보존한다.  
Merge는 Rebase 대비 작업을 쉽게 할 수 있다. 단 branch에서 수행한 작업이 commit history에 모두 남기 때문에 commit history가 복잡해질 수 있다.

* Rebase: 현재 branch를 다른 branch의 끝으로 이동시킨다. 현재 branch의 변경 내용을 임시 저장소에 저장한 후, 다른 branch의 최신 commit 이후에 차례로 적용한다. 이를 통해 commit history를 선형으로 깔끔하게 유지할 수 있다.  
Rebase는 Merge 대비 작업이 어렵다. 변경 내용을 임시 저장소에 저장한 후, 다시 적용하기 때문에 변경 내용이 손실될 가능성이 있다. 이는 특히 협업 시, 문제가 될 수 있다. 그러나, commit history가 간단해져 작업 내용을 파악하기 쉬워진다.

## Cherry-pick

Cherry-pick은 다른 branch의 특정 commit만 현재 branch에 가져오고 싶을 때 사용한다.

![Cherry-pick](https://github.com/yoonpunk/images/blob/main/cherry-pick1.png?raw=true)

위 그림에서와 같이 develop branch에 feature branch의 commit “C”만 가져와 병합하는 방법이 cherry-pick이다.

cherry-pick을 남발하면, commit history가 꼬일 수 있으므로 주의해서 사용해야 한다.

#### 참고자료
https://www.inflearn.com/course/%EC%A7%80%EC%98%A5%EC%97%90%EC%84%9C-%EC%98%A8-git/dashboard  
https://git-scm.com/book/ko/v2  
https://www.youtube.com/watch?v=b72mDco4g78