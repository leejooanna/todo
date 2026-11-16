# 주안의 하루 (할 일 + 기록 앱)

이주안이 폰에서 쓰는 개인 할 일·기록 앱. 폰(claude.ai/code)에서 Claude와 매일 조금씩 고쳐 나간다.

## 구조
- `index.html` 하나에 화면·스타일·코드가 다 있다. 빌드 없음. 외부 라이브러리 없음.
- 공개 저장소 `leejooanna/todo` → GitHub Pages: https://leejooanna.github.io/todo/
  - main에 푸시하면 1~2분 뒤 자동 반영. **이 저장소는 공개라 개인 기록·토큰을 절대 넣지 않는다.**
- 데이터: 비공개 저장소 `leejooanna/claude-work`의 `todo/data.json`. 앱이 GitHub API로 읽고 쓴다(폰에 저장한 fine-grained 토큰 사용). 폰 localStorage에도 같은 내용이 있다.
  - 형식: `{version, todos:[{id,text,tag,due,done,doneAt,createdAt,updatedAt,deleted}], journal:{"YYYY-MM-DD":{mood(1~5),note,question,answer,updatedAt}}}`
  - 지운 할 일은 `deleted:true`로 남긴다(두 기기 병합 때문). 형식을 바꾸면 `merge()`와 기존 데이터 호환을 꼭 챙긴다.

## 일하는 방식
- 이주안은 비개발자. 답은 짧게, 결론 먼저. 바꾼 뒤 "폰에서 새로고침하면 보여요"처럼 확인 방법을 한 줄로.
- 큰 글씨·높은 대비·큰 터치 영역을 유지한다(접근성은 이주안의 관심사: 노약자·장애인용 키오스크).
- 바로 main에 커밋·푸시해도 된다(혼자 쓰는 앱).
- "나에 대해 알아줘", "이번 주 어땠어?" 같은 요청이면 claude-work의 `todo/data.json`을 읽고 기록(기분, 일기, 질문 답)을 바탕으로 답한다. 새로 알게 된 건 claude-work `notes/`에 정리한다.
- 개발 기록은 claude-work `notes/YYYY-MM-DD-할일앱.md`에 남긴다.
