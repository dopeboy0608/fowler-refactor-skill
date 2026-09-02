# fowler-refactor-skill

**마틴 파울러의 리팩터링 카탈로그**를 코드베이스에 적용하는 AI 에이전트 스킬입니다.

`grill-me` 방식의 집중 인터뷰를 통해 무엇을 개선할지 파악하고, 관련 코드 악취를 파울러의 패턴과 매핑한 뒤 Before/After 계획을 제안합니다. 코드는 사용자의 명시적인 승인이 있을 때만 수정하며, **기존 호출처에 대한 Breaking Change는 절대 발생하지 않습니다.**

> 📄 이 파일은 사람을 위한 한국어 번역본입니다. LLM은 이 파일을 읽지 않습니다.

---

## 설치

```bash
npx skills@latest add dopeboy0608/fowler-refactor-skill
```

**Claude Code**, **OpenCode** 및 [skills.sh](https://skills.sh) 형식을 지원하는 모든 에이전트에서 사용할 수 있습니다.

## 제거

```bash
# 인터랙티브 — 설치된 스킬 목록에서 선택
npx skills@latest remove

# 이 스킬을 직접 지정해서 제거
npx skills@latest remove fowler-refactor-skill

# 전역(global) 범위에서 제거
npx skills@latest remove --global fowler-refactor-skill

# 특정 에이전트에서만 제거
npx skills@latest remove --agent claude-code fowler-refactor-skill
```

---

## 사용법

에이전트에서 명시적으로 호출합니다:

```
/fowler-refactor-skill
```

"이거 정리해줘", "리팩토링해줘" 같은 일반적인 요청에는 **자동으로 트리거되지 않습니다.** 반드시 명시적으로 호출해야 실행됩니다.

---

## 주요 기능

1. **인터뷰** (`grill-me` 방식) — 대상 / 코드 악취 / 제약조건이 명확해질 때까지, 한 번에 하나의 질문과 추천 답변을 제시합니다.
2. **패턴 매핑** — 파울러 카탈로그에서 가장 적합한 패턴을 선정합니다 (Extract Function, Decompose Conditional, Split Phase, Replace Conditional with Lookup Table 등).
3. **제안서 리포트** — 코드 수정 전에 Before/After 설계와 호환성 보증을 포함한 구조화된 제안서를 먼저 제시합니다.
4. **피드백 루프** — 계획에 대한 수정 요청이 가능하며, 명시적 승인 후에만 코드를 적용합니다.
5. **안전한 적용** — 타입 체크 / 린트 검증 + 포매터를 수정된 파일에 적용합니다.

### 포함된 핵심 패턴

- **기본 리팩터링**: 함수 추출하기, 매개변수 객체 만들기, 변수 캡슐화하기, 함수 선언 바꾸기
- **조건부 로직 간소화**: 조건문 분해하기, 보호 구문으로 바꾸기, 조건부 로직을 룩업 테이블/다형성으로 바꾸기
- **기능 이동**: 함수 옮기기, 단계 쪼개기, 인라인 코드를 함수 호출로 바꾸기
- **데이터 조직화**: 파생 변수를 질의 함수로 바꾸기
- **간접 계층 전략**: API Adapter / Gateway, 커스텀 훅 비즈니스 계층, Strategy/Lookup Table, Hide Delegate / Facade

---

## 호환성 보증

이 스킬이 제안하는 모든 리팩터링은 다음을 보존합니다:

- 공개 함수 시그니처, 컴포넌트 props, 반환 타입
- 기존 모든 호출처 (호출처 코드 수정 불필요)
- 런타임에서 관찰 가능한 동작

---

## 참고 자료 및 크레딧

- Martin Fowler, *Refactoring: Improving the Design of Existing Code (2nd Edition)*, Addison-Wesley Professional, 2018.
- 패턴 카탈로그: [refactoring.com](https://refactoring.com/catalog/)
- 인터뷰 방식은 Matt Pocock의 [`/grill-me`](https://github.com/mattpocock/skills) 스킬에서 영감을 받았습니다 (MIT License).

> 이 프로젝트는 독립적인 커뮤니티 도구이며, 마틴 파울러 또는 Pearson Education과 공식적인 제휴 관계가 없습니다.

---

## 라이선스

[MIT](./LICENSE) © YongKyu Kim
