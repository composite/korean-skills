# Korean Skills

동해물과 백두산이 마르고 닳도록 대한민국 사정에 맞게 구성된 에이전트 스킬 모음.
만들고 국뽕에 취하여 주모를 부르는 느낌은 덤.

## Work in progress

아직은 검증 단계이므로 원치 않은 결과가 발생할 수 있음.

목표와 다른 결과가 나왔을 경우, 주저 없이 [Issues](https://github.com/composite/korean-skills/issues) 에 알려주시면 감사하겠음.

우리가 한국인이라 필요한 프로젝트인 만큼, 고국의 품(?)으로 돌아갈 수 있으며, 이 경우 당연히 기쁘게 사전 공지 예정.

## Skills

### egovframe-compatibility

[전자정부 표준프레임워크 호환성 가이드](https://www.egovframe.go.kr/home/sub.do?menuNo=70) 문서에 따라 코드를 검증하거나 코드 생성 시 호환성 가이드를 준수하여 리뷰 및 코드 생성을 도와주는 스킬.

간편 설치:

```sh
npx skills add https://github.com/composite/korean-skills --skill egovframe-compatibility
```

권장 시나리오:

- 업무 MVC 레이어 생성(Contoller, Service, DAO)
- 프로젝트 호환성 리뷰
- 일반 스프링 프레임워크 프로젝트를 전자정부로 마이그레이션 시에 대한 호환성 도우미

비권장 시나리오:

- 전자정부 4.x 미만(3.x 이하) 사용 시 (레거시는 고려하고 싶지 않음. 알아서들 하시길.)
- 전자정부 쓸 필요 없는 프로젝트 (앵간하면 주요 거대 LLM이 잘만 도와줌)
- 호환성확인 점검기관의 점검 (이 스킬은 표준 가이드만 따른 것으로, 검증 기관의 가이드는 검증 기관에 문의할 것.)

### kordoc Skills

[chrisryugj/kordoc](https://github.com/chrisryugj/kordoc) 프로젝트를 Agent 친화적인 스킬로 여러분의 문서 분석을 편리하게!

`ref/kordoc` 문서와 `src/mcp.ts` 로직을 기준으로 만든 `npx` 실행 기반 스킬 모음.
MCP 없이 `npm exec --package=kordoc --package=pdfjs-dist` 또는 `kordoc` CLI / one-off Node 실행으로 동작하도록 구성되어 있음.

간편 설치:

```sh
npx skills add https://github.com/composite/korean-skills --skill kordoc-parse-document
npx skills add https://github.com/composite/korean-skills --skill kordoc-detect-format
npx skills add https://github.com/composite/korean-skills --skill kordoc-parse-metadata
npx skills add https://github.com/composite/korean-skills --skill kordoc-parse-pages
npx skills add https://github.com/composite/korean-skills --skill kordoc-parse-table
npx skills add https://github.com/composite/korean-skills --skill kordoc-compare-documents
npx skills add https://github.com/composite/korean-skills --skill kordoc-parse-form
```

권장 시나리오:

- HWP, HWPX, PDF 문서를 `npx kordoc` 기반으로 읽고 요약할 때
- 문서 포맷 판별, 메타데이터 추출, 특정 페이지/테이블 추출이 필요할 때
- 신구대조표용 문서 비교나 서식형 문서의 필드 추출이 필요할 때

요구사항:

- `kordoc-compare-documents` 는 지원되는 문서 2개가 반드시 필요함
- 나머지 `kordoc-*` 스킬은 지원되는 문서 1개를 반드시 첨부하거나 명확한 로컬 경로로 가리켜야 함
- 지원 포맷은 `.hwp`, `.hwpx`, `.pdf` 로 제한됨
- `kordoc-parse-pages` 는 페이지 범위를 함께 제공해야 함
- `kordoc-parse-table` 은 테이블 인덱스 또는 "첫 번째 테이블" 같은 명확한 대상을 함께 제공해야 함

스킬 목록:

| Skill | 용도 | 필수 입력 |
|------|------|-----------|
| `kordoc-parse-document` | 문서 전체를 Markdown/JSON 으로 파싱 | 지원 문서 1개 |
| `kordoc-detect-format` | 매직 바이트 기반 포맷 감지 | 파일 1개 |
| `kordoc-parse-metadata` | 메타데이터 중심 추출 | 지원 문서 1개 |
| `kordoc-parse-pages` | 특정 페이지 범위만 파싱 | 지원 문서 1개 + 페이지 범위 |
| `kordoc-parse-table` | 특정 테이블만 추출 | 지원 문서 1개 + 테이블 인덱스 |
| `kordoc-compare-documents` | 두 문서의 변경점 비교 | 지원 문서 2개 |
| `kordoc-parse-form` | 양식형 문서에서 필드 추출 | 지원 문서 1개 |


## 라이선스

MIT, 죽을 쓰든 밥을 쓰든 MIT 공대에서 네 차를 지붕에 올려놓으리.
