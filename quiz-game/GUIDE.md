# Python 콘솔 퀴즈 게임 단계별 실습

이 폴더는 실행해 볼 수 있는 참고 구현입니다. 직접 만드는 실습은 옆의 빈 `quiz-game-practice` 폴더에서 진행합니다. 각 단계에서 먼저 코드를 작성하고, 확인을 마친 뒤 해당 단계의 파일만 커밋합니다. 아래 예상 결과는 기본 Python 퀴즈를 사용했을 때를 기준으로 합니다. 주제를 바꾸면 문제와 정답에 맞춰 확인값도 바꿉니다.

## 1. 실습 폴더와 GitHub 저장소 준비하기

터미널에서 먼저 `workstation-lab`과 `quiz-game`이 보이는 상위 폴더로 이동합니다. 다음 명령으로 실습 폴더를 만들고 Git을 시작합니다.

```bash
python3 --version
git --version
mkdir quiz-game-practice
cd quiz-game-practice
git init -b main
git config --local user.name "본인 이름"
git config --local user.email "본인 이메일"
```

Python이 3.10 이상인지 확인합니다. `git config --local`의 이름과 이메일은 본인 값으로 바꿉니다. 이 값은 공개 저장소의 커밋에 기록되므로 이메일 공개를 원치 않으면 GitHub 계정의 커밋용 noreply 주소를 사용합니다.

`README.md`를 만들고 제목과 필수 소제목 6개를 잡습니다. 순서는 **프로젝트 개요 → 퀴즈 주제와 선정 이유 → 실행 방법 → 기능 목록 → 파일 구조 → 데이터 파일 설명**입니다. 이 단계에서 프로젝트 목표와 선택한 주제를 한두 문장 적어 둡니다.

`.gitignore`에는 다음 세 줄을 씁니다.

```gitignore
__pycache__/
/state.json
/.state-*.tmp
```

GitHub에서 본인 계정으로 빈 공개 저장소 `quiz-game`을 만듭니다. 원격에서 README, .gitignore, 라이선스 초기화 옵션을 비워 두면 로컬 첫 커밋을 그대로 푸시할 수 있습니다. 터미널에서 GitHub 인증을 마친 뒤, 아래 URL의 `YOUR-ACCOUNT`를 본인 계정으로 바꿉니다. [로컬 저장소를 GitHub에 올리는 방법](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github)

```bash
git add README.md .gitignore
git commit -m "Chore: 프로젝트 초기화와 README 목차 추가"
git remote add origin https://github.com/YOUR-ACCOUNT/quiz-game.git
git push -u origin main
```

**확인** GitHub 저장소 첫 화면에서 README와 첫 커밋을 확인합니다.

## 2. 메뉴와 숫자 입력 만들기

1. `main.py`에서 `read_number(prompt, minimum, maximum)`을 정의합니다. `while True` 안에서 `input(prompt).strip()`을 `int()`로 변환합니다. `ValueError`가 나거나 범위를 벗어나면 안내하고 루프를 계속합니다. 범위에 들어오면 번호를 반환합니다.
2. `show_menu()`에서 `1. 퀴즈 풀기`, `2. 퀴즈 추가`, `3. 퀴즈 목록`, `4. 점수 확인`, `5. 종료`를 출력합니다.
3. `main()`에서 메뉴를 반복해서 표시하고 `read_number("선택: ", 1, 5)`를 호출합니다. 5번이면 반복을 끝내고, 1~4번에는 기능이 준비 중이라는 메시지를 표시합니다. 반복문을 `try`로 감싸고 `except (KeyboardInterrupt, EOFError)`에서 종료를 안내합니다.
4. 파일 맨 아래에 실행 진입점을 추가합니다.

```python
if __name__ == "__main__":
    main()
```

**확인** `python3 main.py`를 실행합니다. `abc`, `9`, 빈 Enter를 차례로 입력하면 매번 다시 입력을 받습니다. ` 5 `는 종료됩니다. 새로 실행해 메뉴에서 `Ctrl+C`를 눌러도 종료 안내가 나와야 합니다.

```bash
git add main.py
git commit -m "Feat: 콘솔 메뉴와 공통 숫자 입력 구현"
```

## 3. 문제 하나를 Quiz 클래스로 표현하기

`quiz.py`를 만듭니다. `Quiz.__init__(self, question, choices, answer)`에서 문제 문자열, 선택지 리스트, 정답 번호를 속성에 저장합니다. 빈 질문, 비어 있는 선택지, 선택지가 4개가 아닌 경우, 1~4 밖의 정답은 `ValueError`로 거부합니다. 정답 타입은 `type(answer) is int`로 확인합니다.

`display(self)`는 질문을 출력하고 `enumerate(self.choices, start=1)`로 선택지에 번호를 붙입니다. `is_correct(self, selected)`는 `selected == self.answer`의 결과인 `bool`을 반환합니다. 코드가 막히면 [Quiz 클래스](quiz.py)의 해당 메서드를 참고합니다.

**확인** 다음 명령에서 선택지 4개가 보이고 마지막 두 줄이 `True`, `False`여야 합니다.

```bash
python3 - <<'PY'
from quiz import Quiz

quiz = Quiz("빈 리스트는?", ["[]", "{}", "()", "0"], 1)
quiz.display()
print(quiz.is_correct(1))
print(quiz.is_correct(2))
PY
git add quiz.py
git commit -m "Feat: 개별 문제를 관리하는 Quiz 클래스 작성"
```

## 4. 기본 퀴즈 5개 이상 작성하기

`quiz.py` 아래에 `default_quizzes()`를 작성합니다. 함수는 `Quiz("문제", ["첫째", "둘째", "셋째", "넷째"], 정답번호)` 형태의 객체를 담은 리스트를 반환합니다. Python 기초를 선택했다면 `len()`, `input()`, 리스트 리터럴, 몫 연산자, `return`을 문제로 만들어 볼 수 있습니다. 질문과 선택지의 표현을 직접 정하고 정답 번호를 따로 메모합니다.

**확인** 다음 명령에서 5 이상이 출력되어야 합니다. 목록의 각 문제가 하나의 정답만 갖는지도 직접 확인합니다.

```bash
python3 -c 'from quiz import default_quizzes; print(len(default_quizzes()))'
git add quiz.py
git commit -m "Feat: 주제에 맞는 기본 퀴즈 작성"
```

## 5. 브랜치에서 퀴즈 풀기 구현하기

먼저 새 브랜치를 만듭니다.

```bash
git checkout -b feature/play
```

`main.py`에서 `default_quizzes`를 가져옵니다. `main()`에서 `quizzes = default_quizzes()`로 목록을 준비하고, 메뉴 1번에서 `play(quizzes)`를 호출합니다.

1. `play(quizzes)`에서 빈 목록이면 안내하고 `return`합니다.
2. `correct = 0`으로 시작해 `for quiz in quizzes`로 한 문제씩 출력합니다. `read_number("정답 번호 (1~4): ", 1, 4)`로 번호를 받고 `quiz.is_correct(selected)`가 참이면 `correct`에 1을 더합니다. 오답이면 올바른 번호를 알려 줍니다.
3. 끝에서 문제 수와 정답 수를 출력하고 `correct`를 반환합니다. 빈 목록에서 돌아갈 때는 `None`을 반환합니다.

**확인** `python3 main.py`에서 1번을 선택합니다. 정답 입력에 `0`, `abc`, 빈 Enter를 넣어 같은 문제에서 다시 묻는지 봅니다. 참고 구현의 정답 `2 → 3 → 1 → 4 → 2`를 넣으면 5개 정답으로 표시됩니다. `python3 -c 'from main import play; play([])'`로 빈 목록 안내도 확인합니다.

```bash
git add main.py
git commit -m "Feat: 퀴즈 출제와 채점 구현"
git checkout main
git merge --no-ff feature/play -m "Merge: 퀴즈 풀기 기능 병합"
git log --oneline --graph --all
```

그래프에 `feature/play` 작업과 병합 커밋이 보이면 다음으로 넘어갑니다.

## 6. 퀴즈 추가 기능 만들기

`main.py`의 가져오기 줄을 `from quiz import Quiz, default_quizzes`로 바꿉니다. `read_text(prompt)`를 정의해 앞뒤 공백을 없앤 문자열이 비어 있으면 다시 묻게 합니다. `add_quiz(quizzes)`는 문제 한 줄, 선택지 네 줄, 정답 번호를 순서대로 받고, 모든 입력을 마친 뒤에만 `Quiz`를 만들어 `quizzes.append(...)`를 호출합니다. 정답 번호는 기존 `read_number()`를 재사용합니다. 메뉴 2번을 `add_quiz(quizzes)`에 연결합니다.

**확인** 2번을 누르고 다음 문제를 등록합니다. 질문 입력에서 먼저 공백만 넣고, 정답 번호에는 먼저 `0`을 넣어 다시 묻는지 확인합니다.

| 입력 항목 | 값 |
| --- | --- |
| 문제 | 딕셔너리에서 값을 찾는 데 쓰는 것은? |
| 선택지 1 | 인덱스 |
| 선택지 2 | 키 |
| 선택지 3 | 줄 번호 |
| 선택지 4 | 파일명 |
| 정답 | 2 |

같은 실행에서 1번을 눌렀을 때 새 문제가 마지막에 나와야 합니다. 파일 저장은 10단계에서 연결합니다.

```bash
git add main.py
git commit -m "Feat: 문제와 선택지를 입력받아 퀴즈 등록"
```

## 7. 퀴즈 목록 보여 주기

`list_quizzes(quizzes)`를 정의하고 메뉴 3번에 연결합니다. 빈 목록이면 안내하고, 문제가 있으면 전체 개수와 각 질문을 출력합니다. `enumerate(quizzes, start=1)`을 쓰면 1번부터 번호를 붙일 수 있습니다.

**확인** 새로 실행하면 3번에서 기본 5개가 보입니다. 6단계 예시 문제를 추가한 직후 다시 3번을 누르면 총 6개와 추가한 질문이 보입니다. `python3 -c 'from main import list_quizzes; list_quizzes([])'`로 빈 목록도 확인합니다.

```bash
git add main.py
git commit -m "Feat: 등록된 퀴즈 목록 출력"
```

## 8. 최고 기록 비교하기

`main()`의 메뉴 반복문 앞에 `best_score = None`을 둡니다. 1번 분기에서 `correct = play(quizzes)`로 결과를 받아, 완료한 판의 결과가 첫 기록이거나 이전 기록보다 높을 때만 갱신합니다. `correct is not None`으로 판이 끝났는지 확인하면 0개 정답도 기록할 수 있습니다. `show_score(best_score)`는 기록이 `None`이면 첫 플레이 전이라는 안내를, 그 외에는 최고 정답 수를 표시합니다. 메뉴 4번에 연결합니다.

**확인** 첫 실행 직후 4번을 누릅니다. 1번으로 한 판을 모두 맞히고 4번에서 최고 5개를 확인합니다. 다시 플레이해 더 적게 맞혀도 최고 기록은 5개여야 합니다.

```bash
git add main.py
git commit -m "Feat: 최고 정답 수 비교와 점수 조회 구현"
```

## 9. QuizGame 클래스로 구조 정리하기

`main.py`에 `QuizGame`을 정의합니다. `__init__(self)`에 `self.quizzes = default_quizzes()`와 `self.best_score = None`을 둡니다. 기존 메뉴 반복문을 `run(self)`에 옮기고, 기능 함수 `show_menu`, `play`, `add_quiz`, `list_quizzes`, `show_score`를 클래스의 메서드로 바꿉니다. 기능 안에서 `self.quizzes`와 `self.best_score`에 접근하게 고칩니다. 최고 기록의 비교와 갱신은 `play()`의 결과 출력 뒤에 두면 됩니다. `read_number`와 `read_text`는 공통 함수로 둡니다.

파일 끝의 `if __name__ == "__main__":` 아래를 `QuizGame().run()`으로 바꿉니다.

**확인** `python3 main.py`에서 1~5번을 다시 시험합니다. 리팩터링 전과 같은 입력에 같은 동작을 하는지 확인합니다.

```bash
git add main.py
git commit -m "Refactor: QuizGame으로 게임 상태와 메뉴 관리"
```

## 10. state.json으로 저장과 복구 구현하기

1. `quiz.py`의 `Quiz.to_dict(self)`에서 `question`, `choices`, `answer`를 딕셔너리로 반환합니다.
2. `main.py`에서 `json`과 `Path`를 가져오고, `STATE_PATH = Path(__file__).resolve().parent / "state.json"`으로 저장 경로를 정합니다. 프로젝트를 다른 폴더에서 실행해도 같은 파일을 사용하게 됩니다.
3. `load_state(self)`는 UTF-8로 파일을 읽고 `json.loads()`로 파싱합니다. `quizzes`가 리스트인지 확인하고 각 항목으로 `Quiz` 객체를 만듭니다. `best_score`는 `None` 또는 0 이상의 정수만 받습니다. 전체 검증을 통과한 뒤에 두 속성을 교체합니다. 파일이 없으면 초기값을 사용하고, 데이터가 손상됐으면 안내합니다. 읽기 오류도 `try/except`로 안내합니다.
4. `save_state(self)`는 두 필드가 담긴 딕셔너리를 만듭니다. `json.dump(..., ensure_ascii=False, indent=2)`로 UTF-8 파일에 쓰면 한글을 그대로 읽을 수 있습니다. 참고 구현의 [save_state](main.py)는 같은 폴더의 임시 파일에 모두 쓴 뒤 `state.json`으로 교체합니다. 쓰기 오류가 나면 안내하고 성공 여부를 반환합니다.
5. `run()`이 메뉴를 띄우기 전에 `load_state()`를 호출합니다. `self.needs_save`를 두어 첫 실행, 손상 데이터 복구, 새 퀴즈 등록, 최고 기록 갱신 때 저장할 상태를 표시합니다. 추가와 최고 기록 갱신 직후 저장하고, 성공하면 `needs_save`를 `False`로 바꿉니다. `run()`의 `finally`에서는 남은 저장이 있으면 시도합니다. 이 경로는 메뉴 5번, `Ctrl+C`, EOF에서도 실행됩니다.

**확인** 아래 확인표를 순서대로 따라갑니다. 손상과 빈 목록 검사는 기존 기록을 옮겨 둔 후 실행합니다.

| 검사 | 입력 또는 명령 | 예상 결과 |
| --- | --- | --- |
| 기록과 한글 유지 | 처음부터 5문제를 맞힌 뒤 6단계 예시를 등록합니다. 5번으로 종료하고 재실행해 3번과 4번을 누릅니다. | 목록에 한글 문제 6개, 최고 기록 5개 정답 |
| 저장 형식 | `python3 -m json.tool --no-ensure-ascii state.json` | `quizzes`와 `best_score`가 보이는 JSON |
| 중간 등록 취소 | 2번을 누르고 문제만 쓴 뒤 `Ctrl+C`로 끝냅니다. 다시 실행합니다. | 종료 안내가 나오고 미완성 문제가 목록에 없음 |
| EOF | `python3 main.py < /dev/null` | 오류 추적 없이 종료 안내 |
| 첫 실행 | 백업을 만든 뒤 `state.json`이 없는 상태에서 실행해 3번과 4번을 누릅니다. | 기본 5문제와 플레이 기록 없음 안내 |
| 손상 복구 | `printf '{' > state.json` 후 실행해 3번과 5번을 누릅니다. | 손상 안내, 기본 5문제, 종료 후 정상 JSON |
| 빈 목록 | `printf '%s\n' '{"quizzes":[],"best_score":null}' > state.json` 후 1번과 3번을 누릅니다. | 두 메뉴에서 등록된 문제가 없다고 안내 |

백업은 기존 파일이 있는 상태에서 `mv -i state.json ../quiz-game-practice-state-backup.json`으로 만듭니다. 같은 이름의 백업이 이미 있다면 새 이름을 사용합니다. 검사를 끝내면 `mv ../quiz-game-practice-state-backup.json state.json`으로 원래 기록을 돌려놓습니다.

```bash
git check-ignore state.json
git add main.py quiz.py
git commit -m "Feat: UTF-8 JSON 저장과 손상 데이터 복구 구현"
```

## 11. README 완성하고 푸시하기

1단계에서 만든 소제목을 아래 순서로 채웁니다. 제목과 순서는 [참고 README](README.md)에서도 확인할 수 있습니다. 파일 구조에는 본인 저장소에 실제로 존재하는 파일을 적고, 실행 시 생성되는 파일은 그렇게 표시합니다.

| 필수 소제목 | 넣을 내용 |
| --- | --- |
| 프로젝트 개요 | 콘솔 퀴즈 게임의 목적과 각 클래스의 역할 |
| 퀴즈 주제와 선정 이유 | 직접 고른 주제와 선택한 이유 |
| 실행 방법 | Python 3.10 이상, 프로젝트 루트에서 `python3 main.py` |
| 기능 목록 | 풀기, 추가, 목록, 점수 확인, 종료 |
| 파일 구조 | 실행 파일과 문제 클래스 파일의 역할 |
| 데이터 파일 설명 | 루트의 `state.json`, UTF-8, `quizzes`와 `best_score`의 구조 및 의미 |

```bash
git add README.md
git commit -m "Docs: README 실행법과 데이터 구조 작성"
git push origin main
git log --oneline --graph --all
git rev-list --count --no-merges main
```

이 순서로 진행했다면 병합을 빼도 기능 단위 커밋 11개가 보입니다. GitHub에서 최신 README와 커밋을 확인합니다.

## 12. 별도 폴더에서 clone, push, pull 실습하기

원래 실습 폴더 `quiz-game-practice`에서 시작합니다. `YOUR-ACCOUNT`를 본인 계정으로 바꿉니다.

```bash
cd ..
git clone https://github.com/YOUR-ACCOUNT/quiz-game.git quiz-game-clone
cd quiz-game-clone
git config --local user.name "본인 이름"
git config --local user.email "본인 이메일"
printf '\n복제한 저장소에서 실행 방법을 확인했습니다.\n' >> README.md
python3 main.py
```

복제본이 실행되면 5번으로 종료합니다. 복제 폴더에서 변경을 커밋하고 푸시한 후 원래 폴더로 돌아갑니다.

```bash
git add README.md
git commit -m "Docs: 복제본 실행 확인 기록"
git push origin main
cd ../quiz-game-practice
git pull --ff-only origin main
tail -n 2 README.md
git log --oneline --graph --all
```

원래 폴더의 README 마지막 줄에 방금 추가한 문장이 보이면 `clone → commit → push → pull` 실습을 마친 것입니다.

## 제출 전 확인

- [ ] 공개 GitHub 저장소 URL을 적습니다. 로그아웃 상태에서 열리는지 확인합니다.
- [ ] 편집기와 터미널에서 Python 버전, Git 버전, 저장소 설정이 보이게 환경 화면을 캡처합니다. 예를 들어 `python3 --version`, `git --version`, `git branch --show-current`를 표시합니다.
- [ ] 메뉴, 플레이 결과, 퀴즈 추가, 목록, 점수 화면을 직접 캡처합니다. 저장소에 넣을 경우 `docs/screenshots/menu.png`, `play.png`, `add_quiz.png`, `list.png`, `score.png`처럼 이름을 붙입니다.
- [ ] `git log --oneline --graph --all`에서 기능별 커밋과 병합이 보이게 캡처합니다.
- [ ] README 필수 소제목 6개와 최초 푸시, 브랜치 병합, clone, pull 실습을 모두 확인합니다.
- [ ] `git status --short`로 의도한 파일만 커밋됐는지 확인합니다. 스크린샷을 저장소에 포함했다면 이미지 파일만 추가 커밋한 뒤 푸시합니다.
