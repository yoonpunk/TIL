# Git 기초

## Git 저장소의 영역
* ```working directory(or working copy)```: 실제 프로젝트 파일이 위치하는 곳. 파일 수정 삭제 가능
* ```staging area(or index, cache)```: 커밋 대기 영역
* ```local repository```: 커밋된 결과물이 저장되는 영역

## Git 명령어 사용법
* ```git [command] —help```: command의 매뉴얼 보기
* ```git init```: git Repository 초기화, 버전 정보를 저장할 .git 디렉터리를 생성
* ```git status```: 현재 저장소 상태 확인
    * ```Untracked files```: 버전 관리가 되고 있지 않은 파일. 신규 생성 파일
    * ```Changes not staged for commit```: working directory에서 변경된 파일
    * ```Changes to be committed```: staging area에 올라온 변경된 파일
* ```git add [file]```: stage area에 신규 파일 및 수정된 파일을 추가, 디렉터리명 입력 시 디렉터리 전체 추가
* ```git commit```: 변경 사항(staging area)을 로컬 저장소에 커밋
* ```git commit -m “commit message”```: 커밋 메시지로 바로 로컬 저장소에 커밋
* ```git commit -a```: working directory의 변경 사항을 바로 add 하면서 커밋, 단 신규 생성 파일은 포함되지 않음
* ```git commit -am “commit message”```: -a, -m 옵션을 동시에 적용
* ```git log```: 현재 ```git Repository의 현재 branch의 커밋 로그 확인. Q를 눌러 나가기
* ```git log -p```: 각 커밋에서의 변경 내용을 보여줌  
    * ```+```: 이번 커밋에서 추가
    * ```-```: 이번에서 삭제  
* ```git log —branches —decorate```: 커밋 변경 내용을 branch 정보와 함께 표시 
* ```git log —branches —decorate — graph```: 커밋 변경 내용을 branch 정보와 함께 그래프로 표시 
* ```git log —branches —decorate — graph —oneline```: 커밋 변경 내용을 branch 정보와 함께 그래프로 간결하게 표시
* ```git log [branch name1]..[branch name2]```: branch 1에는 없고 branch 2에는 있는 커밋 내용 출력
* ```git log -p [branch name1]..[branch name2]```: branch 1에는 없고 branch 2에는 있는 커밋 내용과 소스 변경 내용을 출력
* ```git diff```: working directory와 staging area의 차이 비교
* ```git diff —staged```: staging area와 local repository 비교
* ```git diff [comimit]..[commit]```: 두 버전의 차이 비교
* ```git diff [branch name1]..[branch name2]```: 두 브랜치의 차이 비교 
    * ```-```: branch 1의 내용
    * ```+```: branch 2의 내용
* ```git reflog```: git Repository에서 수행한 작업 로그를 출력
* ```git reset [commit] —hard```: 특정 버전으로 되돌아감, 변경 사항(working directory, staging aread) 전부 삭제. remote repository에 공유된 커밋은 reset 하지말 것
* ```git reset HEAD —hard```: 최신 commit으로 하드 reset
* ```git revert [commit]```: 특정 버전의 변경 사항을 되돌리며 그 내용을 커밋.
staging area에 변경 사항이 있으면 실행 불가하며 working directory에서의 변경 사항과 revert에서 수정될 파일과 충돌될 경우에도 실행 불가
* ```git branch```: branch 목록을 확인. 현재 작업 중인 branch는 *로 표시
* ```git branch [branch name]```: 신규 branch를 생성
* ```git branch -d [branch name]```: 해당 branch를 삭제
* ```git switch [branch name]```: branch 변경
* ```git switch -c [branch name]```: 신규 branch를 생성하며 branch를 변경 (HEAD를 해당 branch로 변경)
* ```git switch —detach [commitid]```: 해당 commit으로 detach HEAD로 변경
* ```git merge [target branch name]```: 현재 branch에 target branch를 병합
* ```git stash```: 현재 working directory와 index 상태를 임시 저장하고 해당 내용을 비움. Untracked files은 stash의 저장대상이 아님.
* ```git stash apply```: stash에 저장한 가장 최근 내용을 불러옴
* ```git stash list```: stash에 저장한 목록을 출력
* ```git stash drop```: stash에 저장한 가장 최근 내용을 삭제
* ```git stash pop```: apply 후 drop까지 수행
* ```git clone [remote url]```: 현재 디렉터리에 remote repository를 clone, remote의 alias는 origin으로 기본 설정
* ```git clone [remote url] [directory]```: 특정 디렉터리에 remote repository를 clone
* ```git remote add [alias] [remote url]```: 현재 local repository를 alias를 갖는 remote repository에 연결
* ```git remote -v```: remote repository 정보 출력
* ```git remote remove [alias]```: alias의 remote repository를 제거
보통 main remote repository의 alias는 origin으로 설정한다.
* ```git push -u origin main```: local repository의 main branch를 origin remote repository의 main branch와 연결하여 push
이후 push나 pull 명령어에서 branch를 생략할 수 있음
* ```git fetch```: remote repository의 변경 내용을 local repository로 가져옴
* ```git merge```: fetch로 가져온 최신 커밋을 local branch에 병합
* ```git pull```: fetch 후 merge를 동시에 진행, local repository와 remote repository를 동기화
* ```git tag```: tag 정보 출력
* ```git tag -v [tag name]```: tag의 상세 정보 출력
* ```git tag [tag name]```: 현재 branch에 light weigh tag 생성
* ```git tag [tag name] [branch]```: 특정 branch에 light weigh tag 생성
* ```git tag -a [tag name] -m “[message]”```: 현재 branch에 특정 정보를 담은 anotated tag 생성
* ```git tag -d [tag name]```: tag 삭제
* ```git push —tags```: local repository의 tag를 push (—tags 옵션이 없으면 tag는 push되지 않음)
* ```git rebase [branch name]```: 현재 branch를 특정 branch로 rebase 수행
* ```git rebase —continue```: rebase 과정 중 발생하는 충돌을 해결한 후 rebase를 지속적으로 수행
* ```git rebase [basebranch] [topicbranch]```: basebranch로 topicbranch를 rebase
* ```git cherry-pick [commitid]```: 특정 commit을 cherry-pick
* ```git cherry-pick [comid1] [comid2] [comid3]```: 복수 개의 commit을 cherry-pick
* ```git cherry-pick [startcommit]..[endcommit]```: 특정 구간의 commit을 cherry-pick
* ```git cherry-pick —continue```: cherry-pick 과정 중 발생하는 충돌을 해결한 후 cherry-pick을 마무리

--- 

#### 참고자료
https://www.inflearn.com/course/%EC%A7%80%EC%98%A5%EC%97%90%EC%84%9C-%EC%98%A8-git/dashboard
https://git-scm.com/book/ko/v2