현재까지 진행한 내용

1. GitHub 저장소 생성
- ai-sw-basic-git-workflow 저장소 생성
- 기본 프로젝트 구조 생성

README.md
SUBMISSION.md

docs/
 ├ CONTRIBUTING.md
 ├ conflict-resolution.md
 └ troubleshooting-log.md

src/
 └ calculator.py

2. 협업 환경 구성
- 팀원 Collaborator 초대 완료
- 개인 Repository 방식 사용
- Issue 기능 활성화

3. Branch Protection 설정
- Pull Request 필수
- 승인 1명 이상 필요
- Force Push 금지

4. Issue 생성
#1 docs: create contributing guide

5. CONTRIBUTING.md 작성
- GitHub Flow 전략
- 브랜치 네이밍 규칙
- 커밋 메시지 컨벤션
- PR 규칙
- 코드 리뷰 규칙
- 충돌 대응 절차

6. Pull Request 생성
feature/contributing-guide → main

PR 생성 완료
Closes #1 연결 완료


[향후 진행 시나리오]

프로젝트 주제
- 간단 계산기 프로그램

구현 기능
- add(a, b)
- subtract(a, b)
- multiply(a, b)
- divide(a, b)

1단계 : 기능 구현

Issue 생성

#2 add 함수 구현
#3 subtract 함수 구현
#4 multiply 함수 구현
#5 divide 함수 구현

담당 배정

팀원1 → add
팀원2 → subtract
팀원3 → multiply
팀원4 → divide

작업 흐름

Issue 생성
→ feature 브랜치 생성
→ 개발
→ Pull Request 생성
→ 리뷰
→ Merge

2단계 : 코드 리뷰

모든 PR에 대해

- 최소 1명 리뷰
- 최소 1명 승인

예시 리뷰

"0으로 나누는 경우 예외 처리가 필요해 보입니다."

작성자는 수정 후 답글 작성

"예외 처리 추가했습니다."

3단계 : 충돌 실습

충돌 1

팀원1
return a+b

팀원2
result = a+b
return result

같은 코드 수정으로 충돌 발생

충돌 2

팀원1
calculator.py → calc.py 변경

팀원2
calculator.py 수정

파일명 변경 + 수정으로 비자명 충돌 발생

4단계 : 충돌 기록

docs/conflict-resolution.md 작성

기록 내용

- 충돌 원인
- 충돌 상황
- 해결 과정
- 최종 결과

5단계 : 트러블슈팅 실습

실습 항목

- git commit --amend
- git reset --soft
- git revert
- git stash

기록 문서

docs/troubleshooting-log.md

6단계 : 최종 제출

SUBMISSION.md 작성

포함 내용

- 팀원 역할
- Issue 목록
- PR 목록
- 리뷰 내역
- 충돌 사례
- 트러블슈팅 사례

증빙 자료

git log --oneline --graph --all

스크린샷 첨부