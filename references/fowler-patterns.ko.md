# 마틴 파울러 리팩터링 패턴 & 코드 악취

마틴 파울러의 *Refactoring: Improving the Design of Existing Code (2판)*을 기반으로, 현대 TypeScript / React 프론트엔드 개발 환경에 맞게 정리한 실용 레퍼런스 카탈로그입니다.

> 📄 이 파일은 사람을 위한 한국어 번역본입니다. LLM은 이 파일을 읽지 않으며, `fowler-patterns.md`(영문)만 참조합니다.

---

## 1. 코드 악취 & 대응 패턴

| 코드 악취 | 증상 | 주요 리팩터링 패턴 |
|---|---|---|
| **기이한 이름 (Mysterious Name)** | 변수·함수·컴포넌트 이름이 의도를 드러내지 못함 (`data`, `temp`, `handle1`) | 함수/변수 이름 바꾸기 (Change Function / Variable Declaration) |
| **중복 코드 (Duplicated Code)** | 두 곳 이상에 유사하거나 동일한 로직/JSX 반복 | 함수 추출하기 (Extract Function), 커스텀 훅 추출하기 |
| **긴 함수 / 거대 컴포넌트 (Long Function / Mega Component)** | 함수나 컴포넌트가 너무 길고 여러 책임을 가짐 | 함수 추출하기, 하위 컴포넌트 분리, 커스텀 훅 추출하기 |
| **긴 매개변수 목록 (Long Parameter List)** | 함수 인자나 컴포넌트 props가 과도하게 많음 | 매개변수 객체 만들기 (Introduce Parameter Object), 객체 통째로 넘기기 |
| **전역 데이터 / 과도한 전역 상태 (Global Data)** | 변경 추적이 어려운 가변 모듈 전역 변수나 전역 상태 남용 | 변수 캡슐화하기 (Encapsulate Variable), 상태 범위 축소 |
| **뒤엉킨 변경 (Divergent Change)** | 하나의 모듈이 여러 다른 이유(렌더 + API + 계산)로 수정됨 | 단계 쪼개기 (Split Phase), 모듈/훅 추출하기 |
| **산탄총 수술 (Shotgun Surgery)** | 하나의 논리적 변경을 위해 여러 파일을 수정해야 함 | 함수/필드 옮기기 (Move Function / Field), 모듈로 묶기 |
| **기능 편애 (Feature Envy)** | 함수가 자신의 모듈보다 다른 모듈의 데이터를 더 많이 참조 | 함수 옮기기 (Move Function), 추출 후 이동 |
| **데이터 뭉치 (Data Clumps)** | 항상 함께 다니는 변수 묶음 (`startDate`, `endDate`, `keyword`) | 매개변수 객체 만들기, 데이터 클래스/타입 만들기 |
| **기본형 집착 (Primitive Obsession)** | 도메인 개념을 원시 문자열/숫자/불리언으로만 표현, 검증 로직이 흩어짐 | 기본형을 객체로 바꾸기 (Replace Primitive with Object), Branded Type 도입 |
| **반복되는 switch문 (Repeated Switches)** | 동일한 `switch` / `if-else` 타입 분기가 여러 곳에 반복 | 조건부 로직을 다형성/룩업 테이블로 바꾸기 |
| **임시 필드 (Temporary Field)** | 특정 상황에서만 값이 채워지는 상태나 props | 컴포넌트/상태 추출, Null Object 패턴 도입 |
| **메시지 체인 (Message Chains)** | 깊은 탐색 체인 (`a.b.c.d()`) 또는 과도한 prop drilling | 위임 숨기기 (Hide Delegate), Context / 슬롯 패턴 활용 |
| **중개자 (Middle Man)** | 단순히 다른 곳에 위임만 하는 무의미한 래퍼 | 중개자 제거하기 (Remove Middle Man) |
| **거부된 유산 (Refused Bequest)** | 서브타입이 공유 인터페이스의 대부분을 무시 | 서브클래스를 위임으로 바꾸기, 합성(Composition) 선호 |

---

## 2. 리팩터링 패턴 카탈로그 (TypeScript / React 적용 가이드)

### A. 기본 리팩터링 (Foundational Refactorings)

#### 함수 추출하기 (Extract Function)
- **개념**: 코드 조각을 찾아 목적을 파악한 뒤, 독립된 함수로 추출하고 의도가 드러나는 이름을 붙인다.
- **TS/React 적용**:
  - JSX 내 인라인 포매팅/계산 로직 → 독립된 순수 유틸리티 함수.
  - 비즈니스 로직과 상태 오케스트레이션 → `useXxx` 커스텀 훅.
  - 거대 컴포넌트 내 특정 UI 블록 → 집중된 프레젠테이션 하위 컴포넌트.

#### 변수 추출하기 / 인라인하기 (Extract / Inline Variable)
- **개념**: 복잡한 표현식의 결과를 명확한 이름의 상수에 할당해 의도를 드러낸다. 반대로 불필요한 임시 변수는 인라인한다.
- **TS/React 적용**:
  - `if (order.status === 'PAID' && !order.isCancelled && user.role === 'ADMIN')` → `const canCancelOrder = ...`

#### 함수 선언 바꾸기 (Change Function Declaration)
- **개념**: 함수 이름이나 매개변수 목록/반환 타입을 의도가 명확히 드러나도록 수정한다.
- **TS/React 적용**:
  - `process()` → `calculateDiscountedPrice()`.
  - 위치 기반 인자 목록 → 구조화된 `Params` 인터페이스.

#### 변수 캡슐화하기 (Encapsulate Variable)
- **개념**: 데이터에 대한 직접 접근을 함수나 훅으로 감싸 변경 지점을 단일화한다.
- **TS/React 적용**:
  - 여기저기 흩어진 `localStorage.getItem('token')` → `useAuthToken()` 훅.

#### 매개변수 객체 만들기 (Introduce Parameter Object)
- **개념**: 항상 함께 전달되는 인자 묶음을 하나의 객체/인터페이스로 묶는다.
- **TS/React 적용**:
  - `(startDate, endDate, keyword, page)` → `(params: SearchFilterParams)`

---

### B. 조건부 로직 간소화 (Simplifying Conditional Logic)

#### 조건문 분해하기 (Decompose Conditional)
- **개념**: 복잡한 조건 검사와 각 조건절 블록을 명확한 이름의 함수로 추출한다.
- **TS/React 적용**: 뒤엉킨 `if-else` 렌더 로직을 명확하게 이름 붙인 헬퍼 함수나 하위 컴포넌트로 분리.

#### 중첩 조건문을 보호 구문으로 바꾸기 (Replace Nested Conditional with Guard Clauses)
- **개념**: 특수/에러 케이스를 먼저 early return으로 처리해 정상 흐름(happy path)이 중첩되지 않게 한다.
- **TS/React 적용**:
  ```tsx
  if (isLoading) return <Spinner />;
  if (error)     return <ErrorMessage error={error} />;
  if (!data)     return null;
  return <MainContent data={data} />;
  ```

#### 조건부 로직을 다형성/룩업 테이블로 바꾸기 (Replace Conditional with Polymorphism / Lookup Table)
- **개념**: 반복되는 `switch` / `if-else` 분기를 맵이나 전략 객체로 교체한다.
- **TS/React 적용**:
  ```ts
  const STATUS_CONFIG: Record<OrderStatus, { label: string; color: Color }> = {
    PENDING:    { label: '대기중', color: 'gray'  },
    PROCESSING: { label: '처리중', color: 'blue'  },
    COMPLETED:  { label: '완료',   color: 'green' },
    CANCELLED:  { label: '취소',   color: 'red'   },
  };
  ```

---

### C. 기능 이동 (Moving Features)

#### 함수/문장 옮기기 (Move Function / Statements)
- **개념**: 함수가 가장 많이 상호작용하는 모듈로 이동한다.
- **TS/React 적용**: 컴포넌트 파일 안의 데이터 변환 유틸리티 → 다른 곳에서도 쓰인다면 `utils/` 또는 도메인 매퍼 모듈로 이동.

#### 인라인 코드를 함수 호출로 바꾸기 (Replace Inline Code with Function Call)
- **개념**: 이미 라이브러리 유틸리티로 존재하는 직접 구현 코드를 함수 호출로 교체한다.
- **TS/React 적용**: 직접 구현한 날짜 포매팅 → `dayjs(date).format(...)`, 직접 구현한 금액 표시 → `formatCurrency(value)`.

---

### D. 데이터 조직화 (Organising Data)

#### 파생 변수를 질의 함수로 바꾸기 (Replace Derived Variable with Query)
- **개념**: 기존 상태로부터 완전히 계산 가능한 값은 별도 상태로 저장하지 않는다.
- **TS/React 적용**: 파생값에 대한 `useEffect + setState` 안티패턴 제거 → 인라인 계산 또는 `useMemo`로 대체.

#### 단계 쪼개기 (Split Phase)
- **개념**: 두 가지 다른 일을 하나의 함수에서 처리할 때, 중간 데이터 구조를 두고 두 단계로 분리한다.
- **TS/React 적용**: API 원본 응답 (1단계: DTO 파싱/정규화) → UI ViewModel (2단계: 표시용 형태로 변환).

---

### E. 간접 계층 도입 전략 (Indirection Layer Strategies)

> *"소프트웨어 공학의 모든 문제는 또 다른 간접 계층을 도입함으로써 해결할 수 있다."* — 데이비드 휠러

많은 파울러 리팩터링은 결합도를 낮추고 변경 범위를 국소화하기 위해 의도적인 간접 계층을 도입합니다. 주요 프론트엔드 전략:

#### API 어댑터 / 게이트웨이 계층 (API Adapter / Gateway Layer)
- **문제**: 서버 DTO 형태가 UI 컴포넌트에 직접 노출되어 백엔드 스키마 변경 시 여러 화면이 깨짐.
- **해결**: `API 응답 → DTO 파서/어댑터 → UI ViewModel` 계층을 삽입. UI는 ViewModel에만 의존.

#### 커스텀 훅 비즈니스 로직 계층 (Custom Hook as Business Logic Layer)
- **문제**: React 컴포넌트가 렌더, 상태, API 호출, 이벤트 처리, 비즈니스 계산을 한데 뒤섞음.
- **해결**: `useXxx` 훅을 중간 계층으로 추출. 컴포넌트는 순수 프레젠테이션 셸이 됨.

#### 룩업 테이블 / 전략 계층 (Lookup Table / Strategy Layer)
- **문제**: 상태/타입별 스타일·텍스트·동작 분기가 컴포넌트 전체에 산재.
- **해결**: 규칙을 `Record<Status, Config>` 맵으로 중앙화. 새 상태 추가 시 맵만 수정하면 됨 (OCP 달성).

#### 위임 숨기기 / 파사드 (Hide Delegate / Facade)
- **문제**: 컴포넌트가 스토어나 라이브러리의 깊은 체인을 직접 탐색 (`a.b.c.d()`).
- **해결**: 필요한 인터페이스만 노출하는 얇은 파사드 함수나 컨텍스트 래퍼를 도입해 내부 복잡성을 은닉.

---

## 3. 제안 전 체크리스트 (Pre-Proposal Checklist)

리팩터링을 제안하기 전에 다음을 확인합니다:

1. **동작 보존 (Behavior Preservation)** — 내부 구조만 변경되며 외부에서 관찰 가능한 동작은 동일한가?
2. **무(無)사이드이펙트 / 하위 호환성 (Zero Side-Effect / Backward Compatibility)** — 기존 호출처가 수정 없이 컴파일·실행되며, 공개 인터페이스(시그니처, props, 반환 타입)가 유지되는가?
3. **단위성 (Atomicity)** — 리팩터링 단계가 독립적으로 적용(및 롤백) 가능할 만큼 작은가?
4. **응집도 & 가독성 (Cohesion & Readability)** — 변경 후 각 모듈/함수가 하나의 명확한 책임을 가지는가?
5. **타입 안전성 (Type Safety)** — TypeScript 타입 시스템이 회귀를 런타임이 아닌 컴파일 타임에 잡는가?
6. **프로젝트 컨벤션 일치 (Project Conventions)** — 리팩터링된 코드가 기존 스타일(네이밍, 포매팅, 패턴)과 일치하는가?
