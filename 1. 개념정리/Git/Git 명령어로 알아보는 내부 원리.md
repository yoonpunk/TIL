# Git 명령어로 알아보는 내부 원리

## git add

working directory에 파일 생성 후, git add를 통해 staging area에 변경 사항을 적용하면 .git 디렉터리의 내부에 아래와 같은 변화가 생긴다.

* staging area의 변경 사항에 해당하는 파일 내용을 담은 object 파일을 objects 디렉터리 이하에 해시값을 생성하여 object 파일명으로 저장  
파일 내용과 일부 부가 정보를 포함하여 내용을 압축한 후, 압축한 내용을 SHA-1 해시 알고리즘을 통해 얻은 해시값으로 objects 디렉터리와 object 파일명으로 사용함.  
(앞 두 자리: objects 디렉터리명, 앞 세 자리부터 끝까지: object 파일명)

* objects 디렉터리 이하에 생성된 object 파일명(해시값)과 staging area의 변경 사항에 해당하는 파일명(실제 파일명)을 연결하는 내용을 index 파일에 추가  
(ex. [object 해시값] - [staging area의 실제 파일명])
    
* 파일 내용이 같으면 staging area의 파일이 달라도 git에서는 같은 objects 파일로 관리함. [중복 저장 비용을 줄임]

## git commit

commit을 하면 .git 디렉터리의 내부에 아래와 같은 변화가 생긴다.
* commit 정보를 담은 object 파일을 생성한다.  
해당 object 파일에는 커밋 메시지 작성자, 부모 커밋 정보, tree(현재 버전의 스냅샷)의 해시값을 갖는다.
* tree 정보는 현재 버전의 스냅샷 정보를 나타낸다. 현재 버전의 실제 파일명과 해당 파일 내용을 갖는 object 파일의 해시값을 갖는다.
* 특정 디렉터리의 파일은 또 다른 tree 정보로 저장한다.  해당 tree에는 디렉터리 이하에 있는 실제 파일에 대응되는 object 파일의 해시값을 갖는다.
    
Git에서 관리하는 object 파일은 크게 아래 세 종류이다.
    
* ```Blog```: 파일의 내용을 나타내는 object 파일. 파일 내용을 압축하여 저장한다.
* ```Tree```:  디렉터리와 파일 구조를 나타내는 ojbect 파일. 디렉터리 이름, 파일 이름, 파일 모드, Blob 객체의 해시값을 저장한다.
* ```Commit```: 커밋 정보를 저장하는 객체로 커밋 메시지, 작성자, 날짜, 부모 커밋의 해시, 트리 객체의 해시를 저장한다.

## git status

git status를 통해, 현재의 변경사항을 파악하는 메커니즘은 아래와 같다.

* 최신 commit의 tree 정보와 index 정보를 비교했을 때의 차이를 비교  
차이가 있을 경우, 아래와 같은 정보가 출력
    * ```Changes to be committed```: 최신 commit의 tree 정보와 index 정보가 불일치
* 현재 index 정보와 working directory의 파일 정보를 비교했을 때의 차이를 비교 
차이가 있을 경우, 아래와 같은 정보가 출력
    * ```Changes not staged for commit```: index에 존재하는 파일 정보와 불일치
    * ```Untracked files```: index에 존재하지 않는 신규 파일

## git branch

git은 아래와 같은 파일을 통해 branch를 관리한다.

* ```HEAD```: 현재 HEAD가 가리키는 branch 정보 파일명을 저장한다.  
(git init 시, refs/heads/main과 같은 기본 branch의 정보 파일을 저장한다.)

* ```refs/heads/[branch name]```: branch 정보를 저장하는 파일.  
refs/heads 디렉터리 밑에 자신의 branch명으로 된 파일에 branch 정보를 저장한다.  branch 파일에는 자신의 branch의 가장 최신 commit object의 해시값을 저장한다.  
(git init 시, refs/heads/main에는 아무 commit 정보가 없으므로 저장하고 있는 commit object 파일의 해시값이 없다.)  

각 branch에 commit이 발생하면 branch 정보 파일에 최신 commit object 파일의 해시값을 새로이 저장한다.  

git switch를 통해 branch가 변경되면 HEAD 정보를 저장하고 있는 HEAD 파일에 현재 HEAD가 가리키고 있는 branch의 정보 파일의 해시값을 새로이 저장한다.  
이를 통해 git은 HEAD와 각 branch의 commit 정보를 관리한다.

## git merge

서로 다른 파일을 수정하거나, 같은 파일의 서로 다른 영역을 수정했을 경우에는 auto-merge를 통해 자동으로 병합이 이루어지고 해당 merge 내용에 대한 commit이 생성된다.  

서로 같은 파일의 같은 영역을 수정했을 경우에는 충돌이 발생한다. 이 경우에는 git이 충돌이 발생한 부분을 나타내주므로 이를 수정하여 commit 하면 된다.

## git merge 충돌 처리

git merge 시 충돌이 발생하면, 아래와 같은 일들이 발생한다.

* ```index``` 파일에서 충돌이 발생한 파일의 3가지 버전의 object 파일의 해시값을 참조  
(공통 조상 commit, 현재 branch, merge 대상 branch 세 버전의 object 파일)
* ```MERGE_HEAD``` 파일에서 현재 branch의 최신 commit object 파일의 해시값을 참조
* ```ORIG_HEAD``` 파일에서 merge 전 현재 branch의 최신 commit object 파일의 해시값을 참조
* ```MERGE_MGS``` 파일에서 merge message 저장
* 충돌이 발생한 파일의 충돌 내용을 담은 object 파일의 해시값 (임시 파일)

 git merge 완료 후 해당 내용이 add 되면 충돌 처리를 완료한 파일의 object 파일이 생성되고 이 파일의 해시값을 index 파일에서 참조한다.

## merge 전략
Git은 3-way merge 전략을 사용해 merge를 수행한다.

* 2-way merge:
2-way merge는 Base 없이 Me와 Other 두 대상을 가지고 merge를 진행하며, 두 버전이 일치하지 않는 부분에서 충돌 발생
    
    
    | Me | Other | 2 way merge |
    | :---: | :---: | :---: |
    | A |  | Conflict |
    | B | B | B |
    | 1 | 2 | Conflict |
    |  | D | Conflict |

* 3-way merge:
3-way merge는 공통 버전인 Base와 Me, Other의 세 가지 대상을 가지고 merge를 진행하며, Base를 기준으로 한 쪽이 수정되었으면 수정 내용을 반영하고, Base를 기준으로 양 쪽이 수정되었으면 충돌이 발생
    
    
    | Me | Base | Other | 3 way merge |
    | :---: | :---: | :---: | :---: |
    | A | A |  |  |
    | B | B | B | B |
    | 1 | C | 2 | Confilict |
    |  | D | D |  |

3-way merge가 2-way merge보다 충돌 상황이 적으므로 더 효율적이다.


## git reset

```git reset —hard [commitid]```를 수행하면 아래와 같은 일이 발생한다.

* ```refs/heads/[branch name]```이 가리키고 있는 최신 commit 정보를 reset한 commit으로 변경한다.
* ```ORIG_HEAD```에 reset을 수행하기 전 최신 commit 정보를 저장한다.
    (reset을 통해 변경 전 최신 commit 정보를 삭제하진 않는다.)
* ```logs/refs/heads/main```에 branch 파일에서 reset된 commit 이력을 저장한다.
    
만약 reset 행위를 복구하고자 한다면, 아래와 같은 방법으로 복구가 가능하다.
    
* ```git reset —hard ORIG_HEAD```를 실행해 ORIG_HEAD에 임시로 저장한 변경 전 commit 정보를 가져와 복구
* ```git reflog```의 정보를 활용해 원하는 commit으로 복구
    
 git switch —detach [commitid]를 통해 HEAD의 위치를 변경하면, HEAD 파일은 특정 branch 파일의 해시값을 저장하는 것이 아닌 commit object 파일의 해시값을 직접 저장한다.  이 상태는 HEAD가 특정 branch를 가리키는 것이 아니므로 detached HEAD라고 한다.

 ## git reset의 대표적인 세 가지 옵션

git reset의 대표적인 세 가지 옵션은 아래와 같다.
* ```git reset —soft [commitid]```: commitid 시점까지 repository 영역만 reset 하고 index와 working directory는 유지된다.
* ```git reset —mixed [commitid]```: commitid 시점까지 repository 영역과 index 영역을 reset 하고 working directory는 유지한다.
* ```git reset —hard [commitid]```: commitid 시점까지 모든 영역을 전부 reset 한다.

|옵션\영역|wirking directory|index|repository|
|:--:|:--:|:--:|:--:|
|--soft|||reset|
|--mixed||reset|reset|
|--hard|reset|reset|reset|

## git remote

```git remote``` 명령어를 통해, local repository에 remote repository를 연결하면 아래와 같은 일이 발생한다.

 * config 파일 내에 remote repository 정보가 저장된다.  
 remote repository의 URL과 remote branch와 local branch를 연결하는 fetch 정보

 ```git push```를 통해 첫 push를 수행하려면, local branch와 remote branch를 연결하는 아래와 같은 명령어를 수행해야 한다.

 * ```git push —set—upstream origin main```

 이는 현재 local branch를 origin의 main branch와 연결하겠다는 뜻이다. 이 작업을 수행하면 아래와 같은 일이 발생한다.

 * config 파일 내에 main branch에 대한 정보가 저장된다.  
 remote repository의 alias와 현재 local branch의 파일명(```refs/heads/main```)
 * ```refs/remotes/origin/main```이라는 remote branch 정보 파일이 생성되고, commit 정보가 저장된다.  
 현재 local branch에서 remote branch로 push 된 가장 최근 commit object 파일의 해시값을 저장

## git pull과 git fetch

```git pull```을 수행하면 remote branch의 내용을 local branch로 병합하며 아래와 같은 일이 발생한다.

 * remote branch에 file과 tree, commit에 대한 object 파일이 생성된다.
 * ```refs/remotes/origin/main(remote branch)```에 저장된 최신 commit object 파일의 해시값이 변경된다.
 * ```refs/heads/main(remote branch에 대응되는 local branch)```에 저장된 commit object 파일의 해시값이 ```refs/remotes/origin/main(remote branch)```이 저장하는 commit object 파일의 해시값으로 변경된다.
 * ```ORIG_HEAD```가 pull 받기 직전 commit object 파일의 해시값을 저장한다.

 ```git fetch```를 수행하면 remote branch의 정보만 가져오며 아래와 같은 일이 발생한다. (병합 x)

 * remote branch에  file과 tree, commit에 대한 object 파일이 생성된다.
 * ```refs/remotes/origin/main(remote branch)```에 저장된 최신 commit object 파일의 해시값이 변경된다.

## git tag

tag 생성 시, tag 종류에 따라 아래와 같은 일이 발생한다.

* light weigh tag 생성: ```refs/tags/[태그명]```의 파일이 생성되고, tag가 붙은 commit object 파일의 해시값을 저장한다.
* annotated tag 생성: annotated tag에 대한 object 파일이 생성되고, 해당 파일에는 commit object 파일의 해시값과 tag에 대한 자세한 정보를 저장한다.  
그리고 ```refs/tags/[태그명]```의 파일이 생성되고, 위에서 생성한 annotated tag에 대한 object 파일의 해시값을 저장한다.

tag와 branch는 내부 메커니즘이 동일하다. 다만, tag는 가리키는 commit이 변경되지 않지만 가리키는 commit이 해당 branch의 최신 commit으로 변경된다는 점이 다르다.

---

#### 참고자료
https://www.inflearn.com/course/%EC%A7%80%EC%98%A5%EC%97%90%EC%84%9C-%EC%98%A8-git/dashboard  
https://git-scm.com/book/ko/v2