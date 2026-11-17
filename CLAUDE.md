# 주안의 하루 (할 일 + 기록 앱)

이주안이 폰에서 쓰는 개인 할 일·기록 앱. 폰(claude.ai/code)에서 Claude와 매일 조금씩 고쳐 나간다.

## 구조
- `index.html` 하나에 화면·스타일·코드가 다 있다. 빌드 없음. 외부 라이브러리 없음.
- 공개 저장소 `leejooanna/todo` → GitHub Pages: https://leejooanna.github.io/todo/
  - main에 푸시하면 1~2분 뒤 자동 반영. **이 저장소는 공개라 개인 기록·토큰을 절대 넣지 않는다.**
- 데이터: 비공개 저장소 `leejooanna/claude-work`의 `todo/data.json`. 앱이 GitHub API로 읽고 쓴다(폰에 저장한 fine-grained 토큰 사용). 폰 localStorage에도 같은 내용이 있다.
  - 형식: `{version, todos:[{id,text,tag,due,done,doneAt,createdAt,updatedAt,deleted}], journal:{"YYYY-MM-DD":{mood(1~5),note,question,answer,updatedAt}}}`
  - `meditations:[{id,date,minutes,at,createdAt,updatedAt}]` 명상 기록(명상 탭에서 1분 이상 하면 추가).
  - 지운 할 일은 `deleted:true`로 남긴다(두 기기 병합 때문). 형식을 바꾸면 `merge()`와 기존 데이터 호환을 꼭 챙긴다.

## 일하는 방식
- 이주안은 비개발자. 답은 짧게, 결론 먼저. 바꾼 뒤 "폰에서 새로고침하면 보여요"처럼 확인 방법을 한 줄로.
- 기본 크기는 아이폰 15(393pt)에 맞춰 작게(본문 16px). 터치 영역은 44pt 이상, 큰 글씨는 '나' 탭 옵션으로. 높은 대비 유지(접근성은 이주안의 관심사: 노약자·장애인용 키오스크).
- 바로 main에 커밋·푸시해도 된다(혼자 쓰는 앱).
- **고칠 때마다 index.html의 `APP_VERSION`을 바꾼다**(예: 2026-11-18a). GitHub Pages가 10분 캐시라, 이 값이 달라야 폰 앱이 열릴 때 새 버전으로 바뀐다.
- "나에 대해 알아줘", "이번 주 어땠어?" 같은 요청이면 claude-work의 `todo/data.json`을 읽고 기록(기분, 일기, 질문 답)을 바탕으로 답한다. 새로 알게 된 건 claude-work `notes/`에 정리한다.
- 개발 기록은 claude-work `notes/YYYY-MM-DD-할일앱.md`에 남긴다.

## 화면 (2026-11-17 개편)
- 하단 메뉴 5개: 캘린더 / 할 일 / 명상 / 운동 / 식단. 설정은 캘린더 오른쪽 위 버튼.
- 캘린더: 한 달(일요일 시작), 날짜 칸에 할 일 막대 2개(+N)·운동·스티커. 날짜를 누르면 "하루 화면"(dialog #day): 스티커, 그날 할 일 바로 추가, 운동·식단 합계, 기분, 한 줄 일기, 오늘의 질문.
- 할 일: 맨 위 칸에 적고 바로 추가(분류 없음). 오늘/내일/언젠가/날짜 칩. 밀린 일·오늘·내일·이후·언젠가로 묶어 보여 줌. 줄을 누르면 고치기 창.
- 운동: 종류 적고 시간 칩(10~90분). 최근 7일 막대.
- 식단: 끼니 칩 + "김치찌개, 공기밥 2" 입력 → `FOOD` 칼로리표(1인분 대략값)로 계산. 표에 없으면 ?, 사용자가 숫자를 넣으면 `foodMemo`에 기억. 칼로리표는 추정값이라 "대략"이라고 표시한다.
- 디자인: 흰 배경 + 파스텔, **항상 밝은 화면**(다크 모드 없음). 기본 크기는 아이폰 15(393pt) 기준, 터치 영역 44pt 이상.
- `#today`→할 일, `#journal`→캘린더로 연결(알림 링크 호환).
- 데이터 추가 형식: `workouts:[{id,date,text,minutes}]`, `meals:[{id,date,slot,text,items:[{name,base,kcal}],kcal}]`, `foodMemo:{음식:kcal}`, `journal[날짜].sticker`.

## 알림
- 앱이 직접 알림을 받는다(웹 푸시). `sw.js`는 알림 전용 서비스 워커이고, 화면 캐시는 하지 않는다(매일 고치는 앱이라).
- '나' 탭 '알림 켜기' → 이 폰의 알림 주소를 `data.pushSubs`(`{id,endpoint,keys,device,createdAt,updatedAt}`)에 저장. 토큰이 있어야 저장된다. 아이폰은 홈 화면에 추가한 앱에서만 된다.
- 보내는 쪽: claude-work `.github/workflows/todo-notify.yml` + `scripts/todo-notify.py`, KST 9시·21시. 비밀 키는 secret `VAPID_PRIVATE_KEY`, 공개 키는 index.html의 `VAPID_PUBLIC`(공개라 괜찮음). 둘은 한 쌍이라 따로 바꾸면 안 된다.
- 워크플로 파일은 맥북 gh 로그인에 workflow 권한이 없어 푸시가 막힌다. 바꿀 땐 GitHub 웹 편집기로.
