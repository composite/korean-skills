# Korean Skills

동해물과 백두산이 마르고 닳도록 대한민국 사정에 맞게 구성된 에이전트 스킬 모음.
만들고 자괴감 드는 느낌은 덤.

## Skills

### egovframe-compatibility

(*아직 미검증된 스킬이므로 사용 책임은 본인에게 있음*)

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


