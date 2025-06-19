## 🗂️ 가이드 파일 구조

다음과 같은 3개의 파일로 구성됩니다. 이 구조는 명확성, 집중성, 확장성을 보장합니다.

```
/guides
├── 📜_00_Guide_Common.md          # ✅ 모든 개발자 필독
├── 🎨_01_Guide_Frontend.md         # ✅ 프론트엔드 개발자 필독 (TS, Next.js, Nuxt.js)
└── ⚙️_02_Guide_Backend.md          # ✅ 백엔드 개발자 필독 (Java, Python)
```

---
---

## 📜 `_00_Guide_Common.md` (공통 가이드)

**이 문서는 모든 개발자가 반드시 준수해야 할 핵심 철학과 공통 원칙을 정의합니다.**

### 🏛️ Part 1. 철학과 원칙 - The "Why"

#### 1.1 🥇 최우선 원칙: 외과수술적 정밀함
> **⚠️ 모든 행동에 앞서는 제1원칙입니다.**
> **요청받지 않은 코드는 절대 변경하거나 삭제하지 않습니다.**
> - **변경 범위 엄수**: 사용자가 명시적으로 요청한 파일과 코드 블록만 수정
> - **기존 코드 보존**: 동작하는 기존 코드는 절대 임의로 리팩토링하거나 제거 금지
> - **스타일 존중**: 기존 코드의 네이밍, 포맷팅, 주석 스타일을 그대로 유지

#### 1.2 🎯 개발의 황금률
- **한국어 우선**: 모든 커뮤니케이션은 명확한 한국어로
- **간결함의 미학**: 적은 코드가 더 나은 코드 (Less Code, Less Debt)
- **DRY 원칙**: 반복하지 마라, 재사용하라 (Don't Repeat Yourself)
- **단일 책임 원칙 (SRP)**: 하나의 기능, 하나의 목적
- **실용주의 (YAGNI)**: 지금 당장 필요 없는 기능은 만들지 마라 (You Aren't Gonna Need It)

### ⚡ Part 2. 공통 실행 규칙 - The "How"

#### 2.1 📖 공통 네이밍 규칙
```
변수/속성: camelCase (userList, orderCount)
상수: UPPER_SNAKE_CASE (MAX_RETRY_COUNT, API_TIMEOUT)
클래스/타입/인터페이스: PascalCase (UserService, UserData)
파일: PascalCase (UserProfile.tsx) 또는 kebab-case (user-profile.service.ts) - **프로젝트 규칙 우선**
```

#### 2.2 🌿 Git 사용 원칙
- **브랜치 전략**: `feat/기능`, `fix/이슈번호`, `refactor/대상` 등 prefix 사용
- **커밋 메시지**: Conventional Commits 규칙 준수
  - `feat: 로그인 기능 추가`
  - `fix: 이메일 유효성 검사 오류 수정`
  - `docs: API 문서 업데이트`

### 💬 Part 3. 커뮤니케이션

#### 3.1 코드 제공 형식
```markdown
### 작업 범위
"요청하신 [컴포넌트/서비스]의 [기능] 로직을 수정했습니다."

### 변경사항 요약
- [핵심 변경 내용 1]
- [핵심 변경 내용 2]

### 코드
[코드 블록]

### 참고사항
- [주의사항이나 후속 조치 필요 항목]
```

#### 3.2 리뷰 응답 형식
```markdown
### 개선점
1. **[분류]** 내용 (예: **[성능]** N+1 쿼리 발생 가능성)
2. **[분류]** 내용 (예: **[가독성]** 복잡한 조건문 변수화 필요)

### 권장사항
- [개선을 위한 구체적인 제안]
```

---
> **Remember**: "코드는 한 번 작성되지만, 수십 번 읽힌다. 미래의 당신과 동료를 위해 명확하게 작성하라."

---
---

## 🎨 `_01_Guide_Frontend.md` (프론트엔드 특화 가이드)

**이 문서는 `공통 가이드`를 상속하며, TypeScript, Next.js, Nuxt.js 환경의 프론트엔드 개발 규칙을 정의합니다.**

### 🏗️ Part 1. 아키텍처 원칙

#### 1.1 컴포넌트 중심 설계 (CDD)
- UI는 재사용 가능한 컴포넌트의 조합으로 구성합니다.
- 로직과 상태를 가진 컨테이너 컴포넌트와 UI 표현에 집중하는 프레젠테이셔널 컴포넌트를 분리합니다.

#### 1.2 상태 관리 전략
- **Local State**: `useState` (React), `ref` (Vue) - 컴포넌트 내부의 단순 상태
- **Complex Local State**: `useReducer` (React), Pinia/Vuex 모듈 - 복잡한 로직을 가진 컴포넌트 상태
- **Global State**: Zustand, Recoil, Pinia - 여러 컴포넌트가 공유하는 전역 상태
- **Server Cache State**: `TanStack Query` (React/Vue) - API 데이터 캐싱, 동기화, 비동기 상태 관리

#### 1.3 폴더 구조 (예시)
```
/src
├── /app (or /pages)    # 라우팅 단위 페이지
├── /components         # 재사용 UI 컴포넌트
│   ├── /common         # 버튼, 인풋 등 범용 컴포넌트
│   └── /domain         # 특정 도메인 관련 컴포넌트 (e.g., /order)
├── /hooks (React)      # 로직 재사용을 위한 커스텀 훅
├── /composables (Vue)  # 로직 재사용을 위한 컴포저블
├── /lib (or /utils)    # 순수 함수, 유틸리티
├── /services           # API 호출 로직
└── /store              # 전역 상태 관리
```

### ⚡ Part 2. 실행 규칙

#### 2.1 컴포넌트 작성 순서 (React 기준)
```typescript
function UserProfile() {
  // 1. State & Refs (useState, useRef)
  // 2. Custom Hooks (useRouter, useQuery, etc.)
  // 3. Event Handlers (const handleClick = () => {})
  // 4. Effects (useEffect, useLayoutEffect)
  // 5. Early returns (if (loading) return <Spinner />)
  // 6. Main return JSX
}
```

#### 2.2 TypeScript 규칙
- `any` 타입 사용 금지. 불명확할 경우 `unknown`을 쓰고 타입 가드를 사용하세요.
- `interface`는 객체/클래스 구조 정의, `type`은 유니언, 인터섹션, 유틸리티 타입에 우선 사용합니다.
- API 응답/요청 데이터는 반드시 타입을 정의합니다. (e.g., `GetUserResponse`, `CreateUserDto`)
- 이벤트 핸들러의 `event` 객체 타입을 명시합니다. (e.g., `React.MouseEvent<HTMLButtonElement>`)

#### 2.3 성능 및 접근성
- **성능**:
  - `React.memo`, `useCallback`, `useMemo`를 사용해 불필요한 리렌더링을 방지합니다. (React)
  - `computed` 속성을 적극 활용해 계산 비용을 줄입니다. (Vue)
  - Next.js/Nuxt.js의 Code Splitting, Image Optimization 기능을 적극 활용합니다.
- **접근성 (A11y)**:
  - 모든 인터랙티브 요소는 키보드로 접근 가능해야 합니다.
  - 의미에 맞는 시맨틱 HTML 태그를 사용합니다. (`<button>`, `<nav>`, `<main>`)
  - `aria-*` 속성을 사용해 스크린 리더에 추가 정보를 제공합니다.

### 🚫 Part 3. 금지 패턴
- **Props Drilling (3단계 이상)**: 3단계 이상 Props를 내릴 경우 Context API 또는 전역 상태 사용을 고려합니다.
- **`useEffect` 남용**: 서버 상태 동기화는 `TanStack Query`, 상태 기반 파생은 `useMemo`를 먼저 고려합니다.
- **CSS `!important`**: 구체적인 선택자를 사용해 재정의합니다.

---
---

## ⚙️ `_02_Guide_Backend.md` (백엔드 특화 가이드)

**이 문서는 `공통 가이드`를 상속하며, Java (Spring) 및 Python (Django/FastAPI) 환경의 백엔드 개발 규칙을 정의합니다.**

### 🏗️ Part 1. 아키텍처 원칙

#### 1.1 계층형 아키텍처 (Layered Architecture)
- **Controller/Resource Layer**: HTTP 요청/응답 처리, 데이터 유효성 검사, Service Layer 호출
- **Service Layer**: 비즈니스 로직 처리, 트랜잭션 관리, Repository Layer 호출
- **Repository/DAO Layer**: 데이터베이스 접근 및 영속성 처리

#### 1.2 API 우선 설계 (API-First Design)
- 기능 구현 전에 API 명세(Contract)를 우선 정의합니다. (Swagger/OpenAPI 활용)
- RESTful 원칙을 준수하여 명확하고 일관된 API를 설계합니다.
- 표준화된 JSON 응답 구조를 사용합니다.
```json
{
  "data": { ... },
  "message": "성공",
  "error": null,
  "statusCode": 200
}
```

#### 1.3 상태 비저장(Stateless) 및 확장성
- 서버는 클라이언트의 상태를 저장하지 않습니다. (인증은 JWT 등 토큰 기반)
- 수평 확장이 용이하도록 서버 인스턴스 간 의존성을 최소화합니다.

### ⚡ Part 2. 실행 규칙

#### 2.1 네이밍 규칙
- **DTO**: `UserCreateRequestDto`, `UserResponseDto` 등 접미사 사용
- **Entity**: `User`, `Order` 등 도메인 명사 사용
- **Service/Repository**: `UserService`, `UserRepository` 등 역할 접미사 사용

#### 2.2 Java (Spring) 규칙
- **의존성 주입**: `@Autowired` 필드 주입보다 생성자 주입을 사용합니다.
- **불변성**: DTO와 Entity 필드는 가능한 `final`로 선언하고, Lombok `@Value` 또는 `@Builder`를 활용합니다.
- **예외 처리**: `try-catch` 남용보다 `@ControllerAdvice`와 `@ExceptionHandler`를 통한 전역 예외 처리를 지향합니다.
- **트랜잭션**: CUD 작업이 포함된 서비스 메서드에는 `@Transactional(readOnly = false)`을, 조회 전용에는 `(readOnly = true)`를 명시하여 성능을 최적화합니다.

#### 2.3 Python (FastAPI/Django) 규칙
- **타입 힌트**: 모든 함수 정의와 변수에 타입 힌트를 반드시 사용합니다.
- **데이터 유효성 검사**: `Pydantic` (FastAPI) 또는 Django Form/Serializer를 사용해 Controller 레벨에서 입력 데이터를 강력하게 검증합니다.
- **의존성 주입**: FastAPI의 `Depends`를 적극 활용하여 코드의 재사용성과 테스트 용이성을 높입니다.
- **ORM 최적화**: N+1 쿼리 문제를 방지하기 위해 `select_related` / `prefetch_related` (Django) 또는 `selectinload` (SQLAlchemy)를 적극 사용합니다.

### 🔐 Part 3. 보안 및 데이터 관리
- **입력값 검증**: 모든 외부 입력은 신뢰하지 않으며, Controller/Resource 계층에서 반드시 검증합니다.
- **민감 정보 보호**: 비밀번호는 `bcrypt`, API 키 등은 환경 변수 또는 Secret Manager를 통해 관리하고, 절대 코드나 로그에 노출하지 않습니다.
- **인증/인가**: 모든 보호된 API에는 인증(Authentication) 및 인가(Authorization) 로직을 적용합니다.
- **로깅**: 주요 비즈니스 로직의 시작/끝, 에러 발생 시점에 구조화된 로그(JSON 형식)를 남겨 추적을 용이하게 합니다.
