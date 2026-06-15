# Study: 실전 Git 협업 워크플로우

## 1. 이 과제의 핵심

이번 과제는 Git 명령어를 단순히 실행하는 것이 아니라, Git과 GitHub를 이용해 실제 팀 협업 흐름을 경험하고 그 과정을 증빙하는 과제다.

핵심은 다음 네 가지다.

- 작업을 브랜치 단위로 나누고 PR로 합치는 흐름을 이해한다.
- Issue, PR, Review를 연결해 작업의 추적성을 남긴다.
- 충돌과 실수 상황을 직접 만들고 해결 과정을 기록한다.
- 팀원이 모두 협업 과정에 참여했다는 증거를 남긴다.

즉, 최종 결과물의 기능 완성도보다 협업 과정의 품질이 더 중요하다.

## 2. Git의 기본 구조

Git은 파일의 변경 이력을 저장하는 분산 버전 관리 시스템이다. Git은 단순히 파일의 최신 상태만 저장하지 않고, 변경 시점마다 프로젝트의 스냅샷을 커밋으로 기록한다.

### 2.1 Working Tree

Working Tree는 실제로 파일을 수정하는 작업 공간이다.

예를 들어 `README.md`를 열어 내용을 바꾸면 그 변경은 먼저 Working Tree에 생긴다.

확인 명령:

```bash
git status
```

상태 예시:

```txt
modified: README.md
```

의미:

- 파일은 수정되었지만 아직 커밋 대상으로 확정되지 않았다.
- 이 상태의 변경은 `git add` 전까지 Staging Area에 올라가지 않는다.

### 2.2 Staging Area

Staging Area는 다음 커밋에 포함할 변경 사항을 고르는 임시 영역이다.

명령:

```bash
git add README.md
```

의미:

- `README.md`의 현재 변경 내용을 다음 커밋 후보로 올린다.
- 모든 변경을 한 번에 커밋하지 않고, 의미 있는 단위로 나누기 위해 사용한다.

### 2.3 Repository

Repository는 커밋들이 저장되는 Git 저장소다.

명령:

```bash
git commit -m "docs: update readme checklist"
```

의미:

- Staging Area에 올라간 변경 사항을 하나의 커밋으로 저장한다.
- 커밋은 해시, 작성자, 시간, 메시지, 부모 커밋 정보를 가진다.

### 2.4 세 영역의 흐름

```txt
Working Tree -> git add -> Staging Area -> git commit -> Repository
```

자주 쓰는 확인 명령:

```bash
git status
git diff
git diff --staged
git log --oneline
```

- `git status`: 현재 변경 상태 확인
- `git diff`: 아직 staged 되지 않은 변경 확인
- `git diff --staged`: staged 된 변경 확인
- `git log --oneline`: 커밋 히스토리 요약 확인

## 3. 커밋

커밋은 Git에서 변경 이력을 남기는 기본 단위다.

좋은 커밋은 다음 조건을 만족한다.

- 하나의 이유를 가진 변경만 담는다.
- 메시지만 봐도 무엇을 바꿨는지 알 수 있다.
- 되돌리거나 리뷰하기 쉽다.

나쁜 커밋 예시:

```txt
update
fix
wip
temp
final
edit file
```

문제점:

- 무엇을 수정했는지 알 수 없다.
- 나중에 문제가 생겼을 때 어떤 커밋을 확인해야 하는지 찾기 어렵다.
- 팀원이 변경 의도를 파악하기 어렵다.

좋은 커밋 예시:

```txt
docs: add contributing guide
feat: add string utility function
fix: handle empty input in sum function
refactor: split review rules section
test: add examples for math utils
```

### 3.1 커밋 메시지 형식

권장 형식:

```txt
type: subject
```

주요 type:

- `feat`: 새 기능 추가
- `fix`: 버그 수정
- `docs`: 문서 수정
- `style`: 코드 의미 변화 없는 포맷 수정
- `refactor`: 기능 변화 없는 구조 개선
- `test`: 테스트 추가 또는 수정
- `chore`: 설정, 빌드, 기타 작업

예시:

```txt
docs: add conflict resolution template
feat: add min value utility
fix: correct team profile link
```

## 4. 브랜치

브랜치는 커밋을 가리키는 포인터다.

많은 사람이 브랜치를 "복사본"처럼 생각하지만, Git 내부에서 브랜치는 특정 커밋을 가리키는 가벼운 이름표에 가깝다.

예시:

```txt
A---B---C  main
```

여기서 `main`은 `C` 커밋을 가리킨다.

새 브랜치를 만들면:

```bash
git switch -c feature/add-readme
```

구조는 다음처럼 볼 수 있다.

```txt
A---B---C  main, feature/add-readme
```

새 브랜치에서 커밋하면:

```txt
A---B---C  main
         \
          D  feature/add-readme
```

즉, 브랜치를 나누면 `main`을 안정적으로 유지하면서 독립적으로 작업할 수 있다.

### 4.1 HEAD

`HEAD`는 현재 내가 바라보고 있는 위치를 의미한다.

보통은 현재 체크아웃한 브랜치를 가리킨다.

예시:

```txt
HEAD -> feature/add-readme -> D
```

의미:

- 현재 작업 위치는 `feature/add-readme` 브랜치다.
- 새 커밋을 만들면 이 브랜치가 앞으로 이동한다.

### 4.2 브랜치를 사용하는 이유

브랜치를 사용하는 이유:

- 작업을 기능 단위로 분리할 수 있다.
- 아직 완성되지 않은 작업이 `main`에 섞이지 않는다.
- PR을 통해 리뷰 후 병합할 수 있다.
- 충돌이나 실수의 영향을 특정 작업 범위 안에 가둘 수 있다.

## 5. 원격 저장소와 로컬 저장소

Git은 분산 버전 관리 시스템이므로 각 개발자 컴퓨터에 전체 저장소 이력이 존재한다.

주요 개념:

- Local Repository: 내 컴퓨터의 Git 저장소
- Remote Repository: GitHub에 있는 원격 저장소
- `origin`: 기본 원격 저장소 이름
- Remote Tracking Branch: 원격 브랜치 상태를 로컬에서 추적하는 참조

자주 쓰는 명령:

```bash
git remote -v
git fetch origin
git pull origin main
git push origin feature/my-work
```

### 5.1 fetch와 pull의 차이

`git fetch`:

- 원격 저장소의 최신 이력을 가져오기만 한다.
- 내 작업 브랜치에는 자동으로 합치지 않는다.
- 안전하게 원격 상태를 확인할 때 좋다.

`git pull`:

- 원격 이력을 가져온 뒤 현재 브랜치에 합친다.
- 내부적으로는 보통 `fetch + merge`처럼 동작한다.
- 협업 중에는 예상치 못한 병합이 생길 수 있으므로 상태 확인 후 사용하는 것이 좋다.

## 6. GitHub Flow

GitHub Flow는 단순하고 실무적인 브랜치 전략이다.

기본 흐름:

```txt
Issue 생성
-> feature 브랜치 생성
-> 작업 및 커밋
-> 원격 브랜치 push
-> Pull Request 생성
-> 코드 리뷰
-> 수정 반영
-> 승인
-> main 병합
```

### 6.1 main 브랜치

`main`은 항상 안정적인 상태여야 한다.

이 과제에서 말하는 "항상 배포 가능한 상태"는 꼭 실제 서비스를 배포한다는 뜻이 아니라, 팀 기준에서 깨지지 않고 신뢰할 수 있는 기준선을 의미한다.

`main`에 직접 push하지 않는 이유:

- 리뷰 없이 변경이 들어가는 것을 막기 위해
- 누가 어떤 이유로 바꿨는지 추적하기 위해
- 문제가 생겼을 때 관련 Issue와 PR을 찾아 되돌아갈 수 있게 하기 위해
- 팀 전체의 작업 기준선을 안정적으로 유지하기 위해

### 6.2 feature 브랜치

작업은 `feature/*` 브랜치에서 진행한다.

브랜치 이름 예시:

```txt
feature/kim-contributing-guide
feature/lee-string-utils
feature/park-conflict-log
feature/choi-team-profile
```

좋은 브랜치 이름의 조건:

- 누가 작업하는지 알 수 있다.
- 어떤 작업인지 알 수 있다.
- 너무 길지 않다.
- 한 브랜치에 여러 주제를 섞지 않는다.

### 6.3 GitHub Flow를 선택하는 이유

GitHub Flow는 브랜치 구조가 단순해서 팀원이 이해하고 적용하기 쉽다. 모든 변경이 PR을 거치므로 리뷰와 추적이 자연스럽게 남는다. `main`을 안정 상태로 유지하면서도 작은 단위로 빠르게 협업할 수 있다.

## 7. Issue

Issue는 작업 단위, 문제, 논의 주제를 기록하는 공간이다.

이 과제에서는 모든 작업을 Issue로 만들고 PR과 연결해야 한다.

Issue에 적으면 좋은 내용:

- 작업 목적
- 완료 조건
- 담당자
- 관련 문서 또는 파일
- 예상되는 충돌 가능성

Issue 예시:

```md
## 목적
팀 협업 규칙을 CONTRIBUTING.md에 정리한다.

## 작업 내용
- 브랜치 네이밍 규칙 추가
- 커밋 메시지 규칙 추가
- PR 및 리뷰 규칙 추가

## 완료 조건
- docs/CONTRIBUTING.md에 필수 항목이 모두 포함된다.
- PR에서 최소 1명 리뷰를 받는다.
```

### 7.1 Issue와 PR을 연결하는 이유

Issue와 PR을 연결하면 다음 장점이 있다.

- 어떤 작업이 어떤 코드 변경으로 해결되었는지 추적 가능하다.
- PR이 merge될 때 Issue를 자동으로 닫을 수 있다.
- `SUBMISSION.md`에서 팀원별 작업 증빙을 정리하기 쉽다.
- 작업 논의와 실제 변경 내역이 분리되지 않는다.

PR 본문 연결 예시:

```md
Closes #3
Fixes #5
```

## 8. Pull Request

Pull Request는 내 브랜치의 변경을 `main`에 병합해 달라고 요청하는 GitHub 기능이다.

PR은 단순한 병합 버튼이 아니라, 협업의 중심 기록이다.

PR에서 확인할 수 있는 것:

- 어떤 파일이 바뀌었는가
- 왜 바뀌었는가
- 어떤 Issue와 연결되는가
- 누가 리뷰했는가
- 어떤 코멘트가 있었는가
- 리뷰 반영 커밋이 있었는가
- 최종적으로 언제 merge되었는가

### 8.1 좋은 PR의 조건

좋은 PR은 작고 명확하다.

- 하나의 작업 목적만 가진다.
- 변경 파일 수가 과도하게 많지 않다.
- What, Why, How가 적혀 있다.
- 관련 Issue가 연결되어 있다.
- 리뷰어가 확인할 방법이 적혀 있다.

PR 본문 예시:

```md
## 연결 이슈
- Closes #4

## 변경 사항(What)
- docs/CONTRIBUTING.md에 브랜치 네이밍 규칙을 추가했습니다.
- 커밋 메시지 컨벤션과 금지 예시를 정리했습니다.

## 변경 이유(Why)
- 팀원이 같은 기준으로 브랜치와 커밋을 만들 수 있도록 하기 위해서입니다.

## 테스트/검증 방법(How)
- 문서의 필수 항목이 과제 요구사항과 일치하는지 확인했습니다.
- 링크와 마크다운 렌더링을 확인했습니다.
```

### 8.2 PR 크기를 작게 유지해야 하는 이유

PR이 너무 크면 리뷰가 어려워진다.

작은 PR의 장점:

- 리뷰어가 변경 의도를 빠르게 이해할 수 있다.
- 충돌 가능성이 줄어든다.
- 문제가 생겼을 때 원인을 찾기 쉽다.
- 리뷰 반영이 빠르다.

## 9. 코드 리뷰

코드 리뷰는 단순히 "맞다", "좋다"를 확인하는 절차가 아니다. 팀원이 변경 의도를 함께 이해하고 품질을 높이는 과정이다.

이 과제에서는 각 PR에 실질적인 리뷰 코멘트가 필요하다.

### 9.1 실질적인 리뷰 코멘트

좋지 않은 리뷰:

```txt
LGTM
좋아요
확인했습니다
```

이런 코멘트는 근거가 부족해서 과제의 최소 품질 기준을 만족하기 어렵다.

좋은 리뷰:

```txt
docs/CONTRIBUTING.md의 브랜치 네이밍 예시가 feature/<name>-<topic>인데,
README의 예시는 feature/<topic>이라 서로 다릅니다.
팀원이 헷갈리지 않도록 하나로 통일하면 좋겠습니다.
```

좋은 이유:

- 특정 파일을 근거로 한다.
- 문제 또는 개선점을 설명한다.
- 해결 방향을 제안한다.

### 9.2 리뷰에서 볼 것

리뷰어는 다음을 확인한다.

- 요구사항을 만족하는가
- 변경 이유가 PR 본문에 설명되어 있는가
- 문서와 코드의 용어가 일관되는가
- 링크가 깨지지 않았는가
- 커밋 메시지가 규칙에 맞는가
- 충돌 가능성이 있는 파일을 건드렸는가
- 더 단순한 해결 방법은 없는가

### 9.3 리뷰 반영 증빙

과제에서는 각 팀원이 본인 PR에서 최소 1회 이상 리뷰를 반영해야 한다.

증빙 방법:

- 리뷰 코멘트에 답글 작성
- 요청된 내용을 수정한 커밋 추가
- "반영했습니다"와 함께 커밋 해시 또는 변경 파일 언급
- PR 타임라인에 리뷰 요청, 수정, 승인 기록 남기기

## 10. Branch Protection Rule

Branch Protection Rule은 중요한 브랜치에 실수로 직접 push하거나 검증 없는 변경이 들어가는 것을 막는 GitHub 설정이다.

이번 과제에서는 `main`에 보호 규칙을 설정해야 한다.

필수 설정:

- `main` 직접 push 금지
- PR을 통한 병합만 허용
- 최소 1명 approve 필요

설정하면 좋은 항목:

- Require a pull request before merging
- Require approvals
- Dismiss stale pull request approvals when new commits are pushed
- Require conversation resolution before merging
- Do not allow bypassing the above settings

### 10.1 Branch Protection을 쓰는 이유

Branch Protection의 목적:

- 실수로 `main`을 망가뜨리는 일을 줄인다.
- 리뷰 없이 변경되는 것을 막는다.
- 팀의 합의와 검증 과정을 강제한다.
- 제출 시 PR 기반 협업 증빙을 남긴다.

## 11. Merge

Merge는 한 브랜치의 변경 이력을 다른 브랜치에 합치는 작업이다.

예시:

```txt
A---B---C  main
         \
          D---E  feature
```

feature를 main에 merge하면:

```txt
A---B---C-------M  main
         \     /
          D---E  feature
```

`M`은 merge commit이다.

### 11.1 Merge Commit의 장점

- 어떤 브랜치가 언제 합쳐졌는지 히스토리에 남는다.
- PR 단위 작업 흐름을 추적하기 좋다.
- 과제에서 `git log --oneline --graph --all` 증빙을 만들 때 협업 흐름이 잘 보인다.

### 11.2 Squash Merge

Squash Merge는 PR 안의 여러 커밋을 하나의 커밋으로 합쳐서 main에 넣는 방식이다.

GitHub에서 사용하는 흐름:

```txt
PR 생성
-> 리뷰 반영 커밋 추가
-> 승인 받기
-> Merge 버튼 옆 옵션에서 Squash and merge 선택
-> 최종 커밋 메시지 확인
-> main에 1개의 커밋으로 반영
```

예시 상황:

```txt
feature/kim-contributing-guide 브랜치

a1b2c3d docs: add contributing outline
b2c3d4e docs: add branch naming rule
c3d4e5f docs: fix typo
```

Squash Merge 후 main:

```txt
f9e8d7c docs: add contributing guide
```

의미:

- feature 브랜치에는 3개의 커밋이 있었지만, main에는 1개의 커밋만 남는다.
- PR 타임라인에는 리뷰 반영 과정이 남으므로 과제 증빙은 PR 링크로 확인할 수 있다.

로컬에서 squash 개념을 실습하는 명령:

```bash
git switch main
git pull origin main
git merge --squash feature/kim-contributing-guide
git status
git commit -m "docs: add contributing guide"
```

주의:

- 실제 과제 저장소에서 `main` 보호 규칙이 켜져 있다면 로컬에서 main에 직접 push하면 안 된다.
- 과제 제출용 협업은 GitHub PR의 `Squash and merge` 버튼을 사용하는 편이 안전하다.
- 로컬 `git merge --squash`는 개념 학습이나 개인 브랜치 정리용으로 이해하면 된다.

장점:

- main 히스토리가 깔끔해진다.
- 작은 수정 커밋이 많이 남지 않는다.

주의점:

- PR 내부의 세부 커밋 흐름은 main 히스토리에서 사라진다.
- 충돌 해결이나 리뷰 반영 커밋을 증빙해야 하는 과제에서는 PR 타임라인과 커밋 기록을 함께 확인해야 한다.

## 12. Rebase

Rebase는 브랜치의 시작 지점을 다른 커밋 위로 옮기는 작업이다.

예시:

```txt
A---B---C  main
     \
      D---E  feature
```

feature를 main 위로 rebase하면:

```txt
A---B---C  main
         \
          D'---E'  feature
```

`D`와 `E`가 그대로 이동하는 것이 아니라, 같은 변경 내용을 가진 새 커밋 `D'`, `E'`가 만들어진다.

### 12.1 Rebase의 장점

- 히스토리를 직선에 가깝게 정리할 수 있다.
- 개인 브랜치의 커밋을 깔끔하게 다듬을 수 있다.
- 보너스 과제의 `git rebase -i`에서 squash, reword를 수행할 수 있다.

### 12.2 최신 main을 내 feature 브랜치에 반영하는 rebase

상황:

```txt
A---B---C  origin/main
     \
      D---E  feature/kim-docs
```

내 feature 브랜치가 오래되어 최신 main의 `C` 커밋을 반영하고 싶다면 다음처럼 진행한다.

```bash
git switch feature/kim-docs
git fetch origin
git rebase origin/main
```

성공하면 히스토리는 다음처럼 바뀐다.

```txt
A---B---C  origin/main
         \
          D'---E'  feature/kim-docs
```

이후 확인:

```bash
git status
git log --oneline --graph --all -10
```

아직 원격에 push하지 않은 개인 브랜치라면 일반 push를 하면 된다.

```bash
git push origin feature/kim-docs
```

이미 원격에 push한 브랜치를 rebase했다면 커밋 해시가 바뀌었기 때문에 일반 push가 거절될 수 있다. 이때는 팀과 공유한 뒤 다음 명령을 사용한다.

```bash
git push --force-with-lease origin feature/kim-docs
```

### 12.3 rebase 중 충돌이 발생했을 때

rebase 중 충돌이 발생하면 Git이 중간에서 멈춘다.

상태 확인:

```bash
git status
```

충돌 파일을 열어 `<<<<<<<`, `=======`, `>>>>>>>` 마커를 해결한다.

해결 후:

```bash
git add docs/CONTRIBUTING.md
git rebase --continue
```

여러 커밋을 replay하는 중이라면 충돌이 여러 번 날 수도 있다. 그때마다 파일 수정, `git add`, `git rebase --continue`를 반복한다.

rebase를 취소하고 원래 상태로 돌아가고 싶을 때:

```bash
git rebase --abort
```

### 12.4 Rebase의 위험

Rebase는 커밋 해시를 바꾼다. 이미 원격에 공유된 커밋을 rebase하면 다른 팀원의 히스토리와 어긋날 수 있다.

안전 수칙:

- 개인 feature 브랜치에서만 사용한다.
- 이미 main에 병합된 커밋에는 사용하지 않는다.
- 팀원이 같이 작업 중인 공유 브랜치에서는 합의 없이 사용하지 않는다.
- force push가 필요할 때는 반드시 팀과 공유한다.
- 가능하면 `--force-with-lease`를 사용한다.

```bash
git push --force-with-lease origin feature/my-work
```

`--force-with-lease`는 원격 브랜치가 내가 마지막으로 본 상태와 다르면 강제 push를 막아준다.

## 13. 충돌

충돌은 Git이 두 변경 사항을 자동으로 합칠 수 없을 때 발생한다.

대표 상황:

- 같은 파일의 같은 줄을 서로 다르게 수정
- 같은 파일의 인접한 영역을 서로 다르게 수정
- 한쪽은 파일 이름 변경, 다른 한쪽은 같은 파일 내용 수정
- 한쪽은 파일 삭제, 다른 한쪽은 같은 파일 수정

### 13.1 충돌 마커

충돌이 발생하면 파일 안에 다음과 같은 마커가 생긴다.

```txt
<<<<<<< HEAD
현재 브랜치의 내용
=======
병합하려는 브랜치의 내용
>>>>>>> feature/example
```

의미:

- `<<<<<<< HEAD`: 현재 브랜치의 변경 시작
- `=======`: 두 변경 내용의 경계
- `>>>>>>> feature/example`: 병합하려는 브랜치의 변경 끝

충돌 해결은 단순히 마커를 지우는 것이 아니다. 두 변경의 의도를 이해하고 최종 파일이 올바른 상태가 되도록 편집해야 한다.

### 13.2 충돌 해결 절차

기본 절차:

```bash
git status
```

충돌 파일 확인:

```txt
both modified: docs/conflict-resolution.md
```

파일을 열어 충돌 마커 확인 후 최종 내용으로 수정한다.

수정 후:

```bash
git add docs/conflict-resolution.md
git commit
```

또는 rebase 중이라면:

```bash
git add docs/conflict-resolution.md
git rebase --continue
```

### 13.3 비자명 충돌

비자명 충돌은 단순히 서로 다른 파일을 수정한 수준이 아니라, 사람이 판단해야 하는 충돌이다.

과제에서 인정될 수 있는 예:

- 같은 문서의 같은 문단을 두 사람이 다르게 수정
- 같은 함수의 로직을 두 사람이 다르게 수정
- 한 사람은 파일 이름을 바꾸고, 다른 사람은 기존 파일 내용을 수정
- 한 사람은 파일을 삭제하고, 다른 사람은 같은 파일을 수정

문서에 기록해야 할 내용:

- 어떤 브랜치에서 발생했는가
- 어떤 파일에서 발생했는가
- 충돌 마커 내용은 무엇이었는가
- 어떤 기준으로 최종 내용을 결정했는가
- 어떤 명령으로 해결했는가
- 관련 PR과 커밋 링크는 무엇인가

## 14. amend, reset, revert, stash

이번 과제의 트러블슈팅 문서에는 네 가지 시나리오가 모두 필요하다.

### 14.1 git commit --amend

`git commit --amend`는 가장 최근 커밋을 수정한다.

주로 사용하는 상황:

- 최근 커밋 메시지를 고치고 싶을 때
- 방금 커밋에 빠뜨린 파일을 추가하고 싶을 때

커밋 메시지만 수정:

```bash
git commit --amend -m "docs: add troubleshooting log"
```

파일을 추가해서 최근 커밋에 포함:

```bash
git add docs/troubleshooting-log.md
git commit --amend
```

주의:

- amend는 기존 커밋을 새 커밋으로 대체한다.
- 커밋 해시가 바뀐다.
- 이미 원격에 push한 커밋을 amend하면 force push가 필요할 수 있다.
- 협업 중 공유된 커밋에는 신중하게 사용한다.

### 14.2 git reset --soft HEAD~1

`git reset --soft HEAD~1`은 최근 커밋 하나를 취소하되, 변경 내용은 Staging Area에 유지한다.

명령:

```bash
git reset --soft HEAD~1
```

결과:

- 최근 커밋은 사라진다.
- 파일 변경 내용은 staged 상태로 남는다.
- 커밋을 다시 나누거나 메시지를 바꿔 새로 커밋할 수 있다.

사용 상황:

- 실수로 너무 큰 커밋을 만들었을 때
- 커밋 메시지가 부적절할 때
- 커밋을 여러 개로 나누고 싶을 때

주의:

- 이미 원격에 push한 커밋에는 조심해야 한다.
- 공유된 히스토리를 reset하면 다른 팀원에게 영향을 줄 수 있다.

### 14.3 git revert

`git revert`는 특정 커밋의 변경을 되돌리는 새 커밋을 만든다.

명령:

```bash
git revert <commit-hash>
```

특징:

- 기존 커밋을 삭제하지 않는다.
- 되돌림 커밋이 새로 생긴다.
- 원격에 이미 push된 커밋을 되돌릴 때 안전하다.

reset 대신 revert를 써야 하는 상황:

- 커밋이 이미 원격 저장소에 push되었다.
- 다른 팀원이 그 커밋을 기준으로 작업하고 있을 수 있다.
- 히스토리를 유지하면서 변경만 취소해야 한다.

예시 흐름:

```bash
git log --oneline
git revert abc1234
git push origin main
```

### 14.4 git stash

`git stash`는 아직 커밋하지 않은 작업을 임시로 보관한다.

사용 상황:

- 작업 중인데 급하게 다른 브랜치로 이동해야 할 때
- pull 또는 merge 전에 작업 트리를 깨끗하게 만들고 싶을 때
- 아직 커밋하기 애매한 중간 작업을 잠시 치워두고 싶을 때

명령:

```bash
git stash
git switch main
git pull origin main
git switch feature/my-work
git stash pop
```

관련 명령:

```bash
git stash list
git stash show
git stash pop
git stash apply
git stash drop
```

`stash pop`과 `stash apply`의 차이:

- `stash pop`: 보관한 작업을 적용하고 stash 목록에서 제거한다.
- `stash apply`: 보관한 작업을 적용하지만 stash 목록에 남겨둔다.

주의:

- stash도 충돌이 날 수 있다.
- 오래된 stash는 어떤 작업이었는지 잊기 쉽다.
- 중요한 작업은 stash보다 커밋으로 남기는 것이 안전하다.

## 15. reset, revert, restore의 차이

세 명령은 모두 "되돌리기"와 관련되어 있어 헷갈리기 쉽다.

### 15.1 reset

`reset`은 브랜치 포인터를 과거 커밋으로 이동시킨다.

주로 로컬 커밋을 정리할 때 사용한다.

```bash
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
```

차이:

- `--soft`: 커밋만 취소, 변경은 staged 상태 유지
- `--mixed`: 커밋 취소, 변경은 unstaged 상태 유지
- `--hard`: 커밋과 변경 모두 제거

주의:

- `--hard`는 작업 내용을 잃을 수 있으므로 매우 조심해야 한다.
- 원격에 공유된 커밋에는 reset을 신중하게 사용해야 한다.

### 15.2 revert

`revert`는 기존 커밋을 지우지 않고, 그 변경을 반대로 적용하는 새 커밋을 만든다.

협업에서 안전한 이유:

- 히스토리가 보존된다.
- 다른 팀원의 로컬 히스토리와 충돌하지 않는다.
- 원격에 push된 커밋을 취소할 때 적합하다.

### 15.3 restore

`restore`는 파일 단위로 변경을 되돌릴 때 사용한다.

예시:

```bash
git restore README.md
git restore --staged README.md
```

의미:

- `git restore README.md`: Working Tree의 변경을 되돌린다.
- `git restore --staged README.md`: Staging Area에서 파일을 내린다.

## 16. git log와 히스토리 증빙

과제에서는 Git 히스토리 증빙이 필요하다.

권장 명령:

```bash
git log --oneline --graph --all
```

예시:

```txt
*   a1b2c3d Merge pull request #8 from feature/lee-string-utils
|\
| * e4f5g6h feat: add string utils
|/
*   i7j8k9l Merge pull request #7 from feature/kim-contributing
|\
| * m1n2o3p docs: add contributing guide
|/
* q4r5s6t Initial commit
```

이 증빙에서 보여야 하는 것:

- feature 브랜치에서 작업한 흔적
- PR merge 기록
- 여러 팀원의 커밋
- main에 병합된 흐름
- 가능하면 충돌 해결 커밋 또는 문서 수정 커밋

## 17. 필수 문서의 역할

### 17.1 README.md

README는 저장소의 첫 화면이다.

포함하면 좋은 내용:

- 팀명과 과제 소개
- 선택한 간단한 결과물 설명
- 실행 또는 확인 방법
- 문서 링크
- 팀원별 결과물 링크

### 17.2 SUBMISSION.md

SUBMISSION은 제출물 인덱스다.

채점자가 빠르게 확인할 수 있도록 모든 증빙 링크를 모아야 한다.

포함할 내용:

- 저장소 URL
- 팀원 목록
- 팀원별 Issue 링크
- 팀원별 PR 링크
- 팀원별 리뷰 링크 또는 리뷰한 PR 링크
- 충돌 해결 문서 링크
- 트러블슈팅 문서 링크
- git log 증빙
- 보너스 수행 시 관련 문서 링크

### 17.3 docs/CONTRIBUTING.md

CONTRIBUTING은 팀 협업 규칙 문서다.

필수 내용:

- GitHub Flow 설명
- 브랜치 네이밍 규칙
- 커밋 메시지 컨벤션
- PR 작성 규칙
- 코드 리뷰 규칙
- 충돌 발생 시 대응 흐름

### 17.4 docs/conflict-resolution.md

충돌 해결 기록 문서다.

필수 내용:

- 충돌 기록 최소 2회
- 최소 1회는 비자명 충돌
- 참여자
- 상황
- 충돌 파일
- 충돌 마커
- 해결 과정
- 결과
- 배운 점
- 관련 PR 또는 커밋 링크

### 17.5 docs/troubleshooting-log.md

Git 실수 또는 문제 해결 기록 문서다.

필수 시나리오:

- `git commit --amend`
- `git reset --soft HEAD~1`
- `git revert`
- `git stash / git stash pop`

각 기록에 포함할 내용:

- 참여자
- 상황
- 시도한 명령
- 결과
- 주의할 점
- 왜 이 방법을 선택했는가

## 18. 간단한 결과물 선택지

과제는 복잡한 프로그램을 만드는 것이 목적이 아니다. 협업 증빙을 남기기 위한 작고 명확한 결과물이 필요하다.

### 18.1 A안: 유틸 함수 모음

구조 예시:

```txt
src/
  math_utils.py
  string_utils.py
  date_utils.py
README.md
```

장점:

- 코드 리뷰를 하기 쉽다.
- 팀원별 함수 1개 이상 기여를 명확히 보여줄 수 있다.
- 간단한 테스트 또는 사용 예시를 붙이기 좋다.

주의:

- 기능을 너무 크게 잡지 않는다.
- 각 함수에 간단한 docstring 또는 README 예시를 남긴다.

### 18.2 B안: 팀 소개 문서

구조 예시:

```txt
team/
  kim.md
  lee.md
  park.md
README.md
```

장점:

- 구현 부담이 낮다.
- 문서 기반 충돌 실습을 만들기 쉽다.
- 팀원별 기여를 나누기 쉽다.

주의:

- 리뷰 코멘트가 형식적인 칭찬만 되지 않게 해야 한다.
- 링크, 문서 구조, 표현 일관성을 리뷰 포인트로 삼는다.

### 18.3 C안: 학습 노트

구조 예시:

```txt
notes/
  kim-git-branch.md
  lee-pull-request.md
  park-conflict.md
README.md
```

장점:

- 과제 목표와 직접적으로 연결된다.
- 각 팀원이 배운 내용을 정리하기 좋다.
- 발표나 질의응답 대비에 좋다.

주의:

- 내용이 너무 중복되지 않도록 주제를 나눈다.
- README에서 목차를 제공한다.

## 19. 보너스: git rebase -i

`git rebase -i`는 커밋 히스토리를 대화형으로 정리하는 명령이다.

명령 예시:

```bash
git rebase -i HEAD~3
```

의미:

- 현재 HEAD부터 최근 3개 커밋을 정리한다.
- 오래된 커밋이 위에, 최신 커밋이 아래에 표시된다.
- 저장하고 에디터를 닫으면 지정한 방식대로 커밋을 다시 만든다.

가능한 작업:

- `pick`: 커밋 유지
- `reword`: 커밋 메시지 수정
- `squash`: 이전 커밋과 합치기
- `fixup`: 이전 커밋과 합치되 메시지는 버리기
- `drop`: 커밋 제거

### 19.1 squash로 여러 커밋을 하나로 합치기

상황:

```txt
feature/kim-troubleshooting 브랜치

a1b2c3d docs: add troubleshooting log
b2c3d4e docs: add reset scenario
c3d4e5f docs: fix typo
```

오타 수정 커밋과 보강 커밋을 하나로 합치고 싶다면 먼저 현재 로그를 확인한다.

```bash
git switch feature/kim-troubleshooting
git log --oneline -5
```

최근 3개 커밋을 정리한다.

```bash
git rebase -i HEAD~3
```

에디터가 열리면 처음에는 다음과 비슷하게 보인다.

```txt
pick a1b2c3d docs: add troubleshooting log
pick b2c3d4e docs: add reset scenario
pick c3d4e5f docs: fix typo
```

첫 커밋은 유지하고 뒤의 두 커밋을 첫 커밋에 합치려면 이렇게 바꾼다.

```txt
pick a1b2c3d docs: add initial troubleshooting log
sq b2c3d4e docs: add reset scenario
squash c3d4e5f docs: fix typo
```

`sq`는 `squash`의 줄임말이다.

저장 후 에디터를 닫으면 Git이 합쳐진 커밋 메시지를 다시 편집하게 한다. 최종 메시지를 다음처럼 정리할 수 있다.

```txt
docs: add troubleshooting scenarios

- Add troubleshooting log structure
- Add reset scenario
- Fix typo in command description
```

완료 후 확인:

```bash
git log --oneline -5
```

결과 예시:

```txt
f9e8d7c docs: add troubleshooting scenarios
```

원격에 아직 push하지 않았다면:

```bash
git push origin feature/kim-troubleshooting
```

이미 원격에 push한 커밋을 squash했다면 해시가 바뀌었으므로 팀과 공유한 뒤 다음 명령을 사용한다.

```bash
git push --force-with-lease origin feature/kim-troubleshooting
```

### 19.2 reword로 커밋 메시지 고치기

상황:

```txt
a1b2c3d update
b2c3d4e docs: add branch naming rule
```

`update`라는 메시지는 의미가 부족하므로 바꿔야 한다.

최근 2개 커밋 중 하나의 메시지를 고치려면:

```bash
git rebase -i HEAD~2
```

에디터에서 다음처럼 바꾼다.

```txt
reword a1b2c3d update
pick b2c3d4e docs: add branch naming rule
```

저장 후 다음 에디터 화면에서 메시지를 구체적으로 수정한다.

```txt
docs: add pull request template
```

확인:

```bash
git log --oneline -3
```

참고:

- 가장 최근 커밋 메시지만 고칠 때는 `git commit --amend -m "새 메시지"`가 더 간단하다.
- 여러 커밋 중 과거 커밋 메시지를 고칠 때는 `git rebase -i`가 적합하다.

### 19.3 fixup으로 오타 수정 커밋 조용히 합치기

`fixup`은 squash와 비슷하지만 합쳐지는 커밋의 메시지를 버린다.

상황:

```txt
a1b2c3d docs: add conflict resolution log
b2c3d4e docs: fix typo
```

명령:

```bash
git rebase -i HEAD~2
```

에디터에서:

```txt
pick a1b2c3d docs: add conflict resolution log
fixup b2c3d4e docs: fix typo
```

결과:

```txt
f9e8d7c docs: add conflict resolution log
```

오타 수정 커밋 메시지는 버려지고 첫 커밋 메시지만 남는다.

### 19.4 정리 전/후 히스토리 증빙 남기기

보너스 과제에서는 rebase 전후를 비교해 문서화하면 좋다.

정리 전 확인:

```bash
git log --oneline --graph -5
```

예시:

```txt
* c3d4e5f docs: fix typo
* b2c3d4e docs: add reset scenario
* a1b2c3d docs: add troubleshooting log
```

정리 후 확인:

```bash
git log --oneline --graph -5
```

예시:

```txt
* f9e8d7c docs: add troubleshooting scenarios
```

문서에는 다음처럼 남기면 된다.

````md
## Rebase 정리 기록

### 대상 브랜치
- feature/kim-troubleshooting

### 정리 전
- docs: add troubleshooting log
- docs: add reset scenario
- docs: fix typo

### 수행 명령
```bash
git rebase -i HEAD~3
```

### 정리 내용
- reset 시나리오 추가 커밋과 오타 수정 커밋을 첫 문서 작성 커밋에 squash했다.
- 최종 커밋 메시지를 `docs: add troubleshooting scenarios`로 정리했다.

### 정리 후
- docs: add troubleshooting scenarios

### 주의점
- rebase로 커밋 해시가 바뀌었기 때문에 원격 브랜치 갱신 시 `--force-with-lease`를 사용했다.
````

결과:

- 커밋 메시지를 더 명확히 고칠 수 있다.
- 자잘한 오타 수정 커밋을 하나로 합칠 수 있다.
- PR 히스토리를 리뷰하기 좋게 만들 수 있다.

주의:

- rebase는 커밋 해시를 바꾼다.
- 개인 feature 브랜치에서만 안전하게 사용한다.
- 이미 다른 사람이 기반으로 삼은 커밋에는 사용하지 않는다.
- 정리 전/후 `git log --oneline --graph`를 기록하면 보너스 증빙에 좋다.

## 20. 보너스: CODEOWNERS

CODEOWNERS는 특정 파일이나 폴더의 책임 리뷰어를 자동 지정하는 GitHub 기능이다.

파일 위치:

```txt
.github/CODEOWNERS
```

예시:

```txt
# 문서 전체는 문서 담당자가 리뷰
docs/ @team-docs

# 소스 코드는 개발 담당자가 리뷰
src/ @team-dev

# 제출 인덱스는 전체 팀 리뷰
SUBMISSION.md @member1 @member2 @member3
```

장점:

- PR 생성 시 자동으로 리뷰어가 지정된다.
- 파일별 책임 영역이 명확해진다.
- 중요한 문서나 코드가 리뷰 없이 병합되는 일을 줄인다.

주의:

- GitHub 사용자명 또는 팀명이 정확해야 한다.
- private 저장소나 조직 설정에 따라 팀 지정 권한이 필요할 수 있다.
- Branch Protection과 함께 쓰면 리뷰 강제 효과가 더 커진다.

## 21. 과제 진행 추천 흐름

실제 팀 저장소에서는 다음 순서로 진행하면 좋다.

1. 팀 저장소 생성
2. 팀원 초대
3. `main` 브랜치 확인
4. 기본 폴더와 파일 생성
5. Branch Protection 설정
6. `docs/CONTRIBUTING.md` 초안 작성
7. Issue 템플릿 또는 PR 템플릿 추가
8. 팀원별 작업 Issue 생성
9. 각자 `feature/*` 브랜치 생성
10. 개인 작업 커밋
11. PR 생성 및 Issue 연결
12. 서로 리뷰 작성
13. 리뷰 반영 커밋 추가
14. 승인 후 merge
15. 충돌 실습 2회 진행
16. 트러블슈팅 4종 실습
17. `SUBMISSION.md`에 모든 증빙 링크 정리
18. `git log --oneline --graph --all` 증빙 저장
19. 보너스 수행 시 rebase 기록과 CODEOWNERS 추가
20. 제출 전 체크리스트로 최종 점검

## 22. 명령어 실전 치트시트

이 섹션은 과제를 실제로 수행할 때 자주 치게 되는 명령어를 흐름별로 정리한 것이다.

명령어는 반드시 현재 브랜치와 변경 상태를 확인한 뒤 실행해야 한다.

가장 자주 확인할 명령:

```bash
git status
git branch
git log --oneline --graph --decorate --all -10
```

### 22.1 최초 설정

Git 사용자 정보 확인:

```bash
git config user.name
git config user.email
```

전역 사용자 정보 설정:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

현재 저장소에서만 사용자 정보를 다르게 설정:

```bash
git config user.name "Team Member Name"
git config user.email "team-member@example.com"
```

설정 전체 확인:

```bash
git config --list
```

### 22.2 저장소 시작 또는 가져오기

새 로컬 저장소 시작:

```bash
git init
git branch -M main
```

원격 저장소 연결:

```bash
git remote add origin git@github.com:ORG_OR_USER/REPO.git
git remote -v
```

처음 push:

```bash
git push -u origin main
```

이미 만들어진 저장소 clone:

```bash
git clone git@github.com:ORG_OR_USER/REPO.git
cd REPO
```

원격 브랜치까지 확인:

```bash
git branch -a
```

### 22.3 하루 작업 시작 루틴

작업 시작 전에는 main을 최신 상태로 맞춘다.

```bash
git switch main
git pull --ff-only origin main
```

`--ff-only`는 fast-forward가 가능할 때만 pull을 허용한다. 예상치 못한 merge commit이 생기는 것을 막는 데 도움이 된다.

새 feature 브랜치 생성:

```bash
git switch -c feature/kim-contributing-guide
```

이미 있는 feature 브랜치로 이동:

```bash
git switch feature/kim-contributing-guide
```

브랜치 목록 확인:

```bash
git branch
```

브랜치별 원격 추적 상태 확인:

```bash
git branch -vv
```

### 22.4 변경 확인, 스테이징, 커밋

현재 상태 확인:

```bash
git status
```

짧은 상태 확인:

```bash
git status -sb
```

아직 staged 되지 않은 변경 확인:

```bash
git diff
```

특정 파일 변경만 확인:

```bash
git diff docs/CONTRIBUTING.md
```

파일을 Staging Area에 올리기:

```bash
git add docs/CONTRIBUTING.md
```

여러 파일 올리기:

```bash
git add README.md SUBMISSION.md docs/CONTRIBUTING.md
```

현재 디렉터리 아래 변경을 모두 올리기:

```bash
git add .
```

staged 된 변경 확인:

```bash
git diff --staged
```

커밋:

```bash
git commit -m "docs: add contributing guide"
```

변경 내용을 보면서 커밋 메시지 작성:

```bash
git commit -v
```

최근 커밋 확인:

```bash
git log --oneline -5
```

### 22.5 원격 브랜치 push

feature 브랜치를 처음 원격에 올릴 때:

```bash
git push -u origin feature/kim-contributing-guide
```

이후 같은 브랜치에서 추가 push:

```bash
git push
```

원격 브랜치 이름을 명시해서 push:

```bash
git push origin feature/kim-contributing-guide
```

원격 브랜치 삭제:

```bash
git push origin --delete feature/kim-contributing-guide
```

로컬 브랜치 삭제:

```bash
git branch -d feature/kim-contributing-guide
```

강제로 로컬 브랜치를 삭제해야 하는 경우:

```bash
git branch -D feature/kim-contributing-guide
```

주의:

- `-D`는 merge되지 않은 로컬 변경 이력도 삭제할 수 있으므로 신중하게 사용한다.
- 과제에서는 PR 병합 후 GitHub가 제공하는 delete branch 기능을 사용해도 된다.

### 22.6 Issue와 PR을 GitHub CLI로 다루기

GitHub CLI인 `gh`가 설치되어 있고 로그인이 되어 있다면 명령어로 Issue와 PR을 만들 수 있다.

로그인 상태 확인:

```bash
gh auth status
```

Issue 생성:

```bash
gh issue create \
  --title "docs: contributing guide 작성" \
  --body "브랜치 네이밍, 커밋 메시지, PR 규칙을 정리합니다."
```

Issue 목록 확인:

```bash
gh issue list
```

PR 생성:

```bash
gh pr create \
  --base main \
  --head feature/kim-contributing-guide \
  --title "docs: add contributing guide" \
  --body "Closes #3

## 변경 사항(What)
- CONTRIBUTING.md에 협업 규칙을 추가했습니다.

## 변경 이유(Why)
- 팀원이 같은 기준으로 작업하기 위해 필요합니다.

## 테스트/검증(How)
- 마크다운 렌더링과 필수 항목 포함 여부를 확인했습니다."
```

PR 목록 확인:

```bash
gh pr list
```

PR 상세 확인:

```bash
gh pr view 7
```

브라우저에서 PR 열기:

```bash
gh pr view 7 --web
```

다른 사람 PR을 로컬로 가져와 확인:

```bash
gh pr checkout 7
```

리뷰 코멘트 작성:

```bash
gh pr review 7 --comment --body "docs/CONTRIBUTING.md의 브랜치 예시와 README의 예시가 다릅니다. 하나로 통일하면 좋겠습니다."
```

승인 리뷰 작성:

```bash
gh pr review 7 --approve --body "필수 항목을 모두 충족했고 예시도 일관됩니다."
```

수정 요청 리뷰 작성:

```bash
gh pr review 7 --request-changes --body "PR 본문에 검증 방법이 빠져 있어 보강이 필요합니다."
```

주의:

- `gh`를 쓰지 않아도 GitHub 웹 UI로 같은 작업을 할 수 있다.
- 과제에서는 명령 사용 여부보다 Issue, PR, Review 기록이 남는 것이 중요하다.

### 22.7 main 최신 내용을 feature 브랜치에 반영

방법 A: merge 사용

```bash
git switch feature/kim-contributing-guide
git fetch origin
git merge origin/main
```

장점:

- 기존 커밋 해시가 바뀌지 않는다.
- 공유된 브랜치에서도 비교적 안전하다.

단점:

- merge commit이 생길 수 있다.

방법 B: rebase 사용

```bash
git switch feature/kim-contributing-guide
git fetch origin
git rebase origin/main
```

장점:

- 히스토리가 직선에 가깝게 정리된다.

단점:

- 커밋 해시가 바뀐다.
- 이미 push한 브랜치라면 `--force-with-lease`가 필요할 수 있다.

### 22.8 충돌 해결 명령

충돌 발생 상태 확인:

```bash
git status
```

충돌 파일 예시:

```txt
both modified: docs/conflict-resolution.md
```

파일을 수정해 충돌 마커를 제거한 뒤:

```bash
git add docs/conflict-resolution.md
```

merge 중이었다면:

```bash
git commit
```

rebase 중이었다면:

```bash
git rebase --continue
```

merge를 취소하고 충돌 전으로 돌아가기:

```bash
git merge --abort
```

rebase를 취소하고 충돌 전으로 돌아가기:

```bash
git rebase --abort
```

충돌 해결 후 히스토리 확인:

```bash
git log --oneline --graph --decorate --all -10
```

### 22.9 파일 이동, 삭제, 이름 변경

Git이 파일 이동을 더 잘 추적하도록 `git mv`를 사용할 수 있다.

파일 이름 변경:

```bash
git mv docs/old-guide.md docs/contributing-guide.md
git commit -m "docs: rename contributing guide"
```

파일 삭제:

```bash
git rm docs/unused.md
git commit -m "docs: remove unused guide"
```

주의:

- 한쪽은 파일 이동, 다른 한쪽은 내용 수정을 하면 충돌 또는 병합 이슈가 생길 수 있다.
- 과제의 비자명 충돌 실습에 사용할 수 있는 시나리오다.

### 22.10 작업 되돌리기

Working Tree의 특정 파일 변경 취소:

```bash
git restore README.md
```

Staging Area에서 파일 내리기:

```bash
git restore --staged README.md
```

최근 커밋 메시지 수정:

```bash
git commit --amend -m "docs: add submission index"
```

최근 커밋 취소, 변경은 staged 상태로 유지:

```bash
git reset --soft HEAD~1
```

최근 커밋 취소, 변경은 unstaged 상태로 유지:

```bash
git reset --mixed HEAD~1
```

원격에 push된 커밋을 안전하게 되돌리기:

```bash
git revert <commit-hash>
```

특정 커밋이 바꾼 내용 확인:

```bash
git show <commit-hash>
```

주의:

- 협업 중 원격에 push된 커밋은 reset보다 revert로 되돌리는 것이 안전하다.
- `git reset --hard`는 변경 내용을 잃을 수 있으므로 과제 중에는 특별한 이유 없이 사용하지 않는 편이 좋다.

### 22.11 stash로 작업 잠시 보관

현재 작업 보관:

```bash
git stash push -m "docs: contributing 작성 중"
```

stash 목록 확인:

```bash
git stash list
```

stash 내용 요약 확인:

```bash
git stash show stash@{0}
```

stash 내용 자세히 확인:

```bash
git stash show -p stash@{0}
```

가장 최근 stash 적용 후 목록에서 제거:

```bash
git stash pop
```

stash 적용만 하고 목록에는 유지:

```bash
git stash apply stash@{0}
```

특정 stash 삭제:

```bash
git stash drop stash@{0}
```

stash 실습 흐름 예시:

```bash
git status
git stash push -m "docs: conflict log 작성 중"
git switch main
git pull --ff-only origin main
git switch feature/kim-conflict-log
git stash pop
```

### 22.12 히스토리와 증빙 확인

전체 브랜치 그래프:

```bash
git log --oneline --graph --all
```

최근 20개만 보기:

```bash
git log --oneline --graph --decorate --all -20
```

파일별 변경 통계:

```bash
git log --stat --oneline
```

특정 파일 히스토리:

```bash
git log --oneline -- docs/CONTRIBUTING.md
```

특정 커밋의 변경 파일 목록:

```bash
git show --name-only <commit-hash>
```

특정 커밋의 변경 요약:

```bash
git show --stat <commit-hash>
```

두 브랜치 차이 확인:

```bash
git diff main...feature/kim-contributing-guide
```

과제 제출 증빙으로 남기기 좋은 명령:

```bash
git log --oneline --graph --decorate --all -30
git branch -a
git remote -v
```

### 22.13 과제용 추천 명령 흐름

개인 작업 1개를 Issue에서 PR까지 진행하는 예시다.

```bash
git switch main
git pull --ff-only origin main
git switch -c feature/kim-contributing-guide
```

파일 수정 후:

```bash
git status
git diff
git add docs/CONTRIBUTING.md
git diff --staged
git commit -m "docs: add contributing guide"
```

원격에 올리기:

```bash
git push -u origin feature/kim-contributing-guide
```

PR 생성 후 리뷰 반영:

```bash
git status
git add docs/CONTRIBUTING.md
git commit -m "docs: clarify branch naming rule"
git push
```

PR merge 후 로컬 정리:

```bash
git switch main
git pull --ff-only origin main
git branch -d feature/kim-contributing-guide
```

## 23. 자주 하는 실수

### 23.1 main에 직접 push

문제:

- PR 기록이 남지 않는다.
- 리뷰 증빙이 사라진다.
- 과제 요구사항을 충족하지 못할 수 있다.

예방:

- Branch Protection을 먼저 설정한다.
- 모든 작업은 feature 브랜치에서 시작한다.

### 23.2 Issue 없이 PR 생성

문제:

- 작업 추적성이 떨어진다.
- `Closes #n` 증빙이 없다.

예방:

- 작업 전에 Issue를 먼저 만든다.
- PR 본문 첫 부분에 연결 Issue를 적는다.

### 23.3 형식적인 리뷰만 남김

문제:

- "LGTM"만 있는 리뷰는 실질 리뷰로 인정되기 어렵다.

예방:

- 파일, 줄, 문단, 로직 중 하나를 근거로 코멘트를 남긴다.
- 질문, 대안 제안, 리스크 지적 중 하나 이상을 포함한다.

### 23.4 충돌을 문서화하지 않음

문제:

- 충돌을 해결해도 과제 증빙이 부족해진다.

예방:

- 충돌 발생 화면 또는 충돌 마커를 기록한다.
- 관련 PR과 커밋 링크를 남긴다.
- 해결 기준과 명령을 문서에 적는다.

### 23.5 reset과 revert를 혼동

문제:

- 원격에 push된 커밋을 reset으로 되돌리면 팀원 히스토리와 충돌할 수 있다.

예방:

- 로컬에서 아직 공유하지 않은 커밋 정리는 reset을 사용한다.
- 원격에 push된 커밋 취소는 revert를 사용한다.

## 24. 제출 전 확인 질문

제출 전 다음 질문에 답할 수 있어야 한다.

- 브랜치는 Git 내부에서 무엇을 가리키는가?
- 왜 main에 직접 push하지 않는가?
- GitHub Flow의 흐름은 무엇인가?
- Issue와 PR을 연결하는 이유는 무엇인가?
- 좋은 PR 본문에는 무엇이 들어가야 하는가?
- 실질적인 코드 리뷰란 무엇인가?
- 충돌 마커의 각 영역은 무엇을 의미하는가?
- 비자명 충돌은 어떤 상황인가?
- `reset --soft`와 `revert`는 언제 각각 사용하는가?
- `stash pop`과 `stash apply`는 무엇이 다른가?
- rebase는 왜 히스토리를 깔끔하게 만들지만 위험할 수 있는가?
- CODEOWNERS는 어떤 문제를 줄여주는가?

## 25. 핵심 요약

이 과제에서 가장 중요한 것은 "기록"이다.

GitHub 협업은 단순히 결과 파일을 합치는 일이 아니다. 누가, 왜, 어떤 방식으로 변경했고, 누가 검토했으며, 문제가 생겼을 때 어떻게 해결했는지를 남기는 과정이다.

따라서 제출 전에는 다음 증거가 분명히 보여야 한다.

- 팀원이 모두 Issue를 만들었다.
- 팀원이 모두 PR을 만들었다.
- 팀원이 모두 리뷰에 참여했다.
- 리뷰 반영 기록이 있다.
- 충돌 해결 기록이 있다.
- 트러블슈팅 기록이 있다.
- main은 PR을 통해 관리되었다.
- `SUBMISSION.md`에서 모든 링크를 빠르게 확인할 수 있다.
