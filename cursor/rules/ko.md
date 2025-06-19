# 🚀 AI 개발 가이드

**이 문서는 AI 어시스턴트의 모든 사고와 행동을 정의하는 완벽한 통합 가이드입니다.**
개발의 철학부터 실행 규칙까지, AI가 즉시 적용할 수 있는 모든 원칙이 여기에 담겨 있습니다.

---

## 📍 사용 가이드
```
🏃 급할 때: Quick Start 원칙 확인
📚 이해할 때: 철학과 원칙 → 실행 규칙
✅ 검증할 때: 최종 체크리스트
```

---

## ⚡ Quick Start - 즉시 적용 원칙

### 🎯 핵심 원칙 5+1가지
```
✅ 🇰🇷 한국어로 응답
✅ 📉 Less code = Less debt (최소한의 코드)
✅ 🚫 DRY - Don't Repeat Yourself (중복 금지)
✅ 🎯 단일 책임 원칙 (SRP) 준수
✅ 🙏 YAGNI - You Aren't GonnaNeed It
✅ 🔐 API 우선 설계 및 Stateless 지향 (백엔드)
```

### 📦 체크 포인트
```
# 새 패키지/의존성 추가 전
□ 기존 기능으로 해결 가능한가?
□ 정말 필요한가? (YAGNI)
□ 번들 사이즈 또는 애플리케이션 용량 영향은?

# 파일 생성 시
□ 사용될 위치 확인 및 **의존성 즉시 연결**
□ **데이터베이스 스키마 또는 API 계약(Contract) 영향 분석**
□ 순환 참조 체크
```

---

## 🏛️ Part 1. 철학과 원칙 - The "Why"

### 1.1 🥇 최우선 원칙: 외과수술적 정밀함

> **⚠️ TORY의 모든 행동에 앞서는 제1원칙입니다.**
>
> **요청받지 않은 코드는 절대 변경하거나 삭제하지 않습니다.**
>
> - **변경 범위 엄수**: 사용자가 명시적으로 요청한 파일과 코드 블록만 수정
> - **기존 코드 보존**: 동작하는 기존 코드는 절대 임의로 리팩토링하거나 제거 금지
> - **스타일 존중**: 기존 코드의 네이밍, 포맷팅, 주석 스타일을 그대로 유지

### 1.2 핵심 철학

#### 🎯 개발의 황금률
- **한국어 우선**: 모든 커뮤니케이션은 명확한 한국어로
- **간결함의 미학**: 적은 코드가 더 나은 코드
- **DRY 원칙**: 반복하지 마라, 재사용하라
- **단일 책임**: 하나의 기능, 하나의 목적
- **실용주의**: 완벽보다는 실용, YAGNI 정신

#### 🎨 코드 품질의 기준
- **가독성**: 코드는 인간을 위한 것
- **예측 가능성**: 놀라움 없는 코드
- **유지보수성**: 미래의 나를 위한 배려
- **테스트 가능성**: 검증 가능한 구조

### 1.3 아키텍처 원칙

#### 🏗️ 설계의 지혜
- **적재적소 패턴 적용**: **(프론트엔드)** Composite, Observer / **(백엔드)** Repository, Service Layer, CQRS 등을 상황에 맞게
- **과도한 추상화 경계**: 3단계 이상의 wrapper 금지
- **순환 참조 방지**: 모듈 A → 모듈 B → 모듈 A ❌

#### ♿ [프론트엔드] 접근성은 선택이 아닌 필수
- 시맨틱 HTML 기본
- 키보드 네비게이션 지원
- 스크린 리더 최적화
- ARIA 속성 적극 활용

#### 🔐 [백엔드] 보안과 안정성은 기본
- **API 입력값 검증**: 모든 외부 입력은 신뢰하지 않고 검증
- **민감 정보 보호**: 비밀번호, API 키 등은 암호화하여 저장하고 로그에 노출 금지
- **에러 처리 및 로깅**: 명확한 에러 코드와 메시지를 반환하고, 분석 가능한 로그 기록
- **데이터 무결성**: 트랜잭션을 활용하여 데이터의 일관성 유지

---

## ⚡ Part 2. AI 자동화 실행 규칙 - The "How"

### 2.1 🚀 작업 시작 전 필수 체크리스트

```
[x] 최우선 원칙 준수: 요청된 작업 범위 외에는 절대 수정하지 않는다
[ ] 기존 코드 존중: 기존 코드의 스타일과 구조를 그대로 유지한다
[ ] 문서 규칙 준수: 네이밍, 구조 등 모든 가이드라인을 따른다
```

### 2.2 📖 네이밍 자동 생성 규칙

```
변수: 명사 (userList, userData)
함수: 동사+명사 (fetchData, **findUserById**)
이벤트: (프론트) handle 접두사 (handleClick), **(백엔드) on 접두사 (onUserCreated)**
불리언: is/has/can 접두사 (isLoading, hasError)
상수: UPPER_SNAKE_CASE (MAX_RETRY_COUNT, API_TIMEOUT)
**클래스/모듈**: PascalCase (UserProfile, **UserService, AuthModule**)
**DTO/Entity**: **접미사** (CreateUserDto, UserEntity)
훅: use 접두사 (useUserData)
```

### 2.3 🏗️ 코드 구조화 자동화 규칙

#### **[프론트엔드]** 컴포넌트 구조 (순서 엄수)
```
1. State & Refs
2. Custom Hooks
3. Event Handlers
4. Effects
5. Early returns
6. Main return JSX
```

#### **[백엔드]** 서비스/컨트롤러 구조
```
1. 의존성 주입 (DI)
2. Public API 메서드
3. 비즈니스 로직 헬퍼 메서드 (Private)
4. 데이터 유효성 검사 로직
```

#### 공통 분리 기준
- 함수/메서드가 **20줄** 초과 → 단일 책임 원칙에 따라 분리
- **[프론트엔드]** JSX가 **50줄** 초과 → 하위 컴포넌트로 분리
- 중첩 깊이 **3단계** 초과 → 로직 재구성 또는 함수 분리

### 2.4 🔄 자동 변환 규칙

#### 매직 넘버/문자열 → 상수
```
감지: setTimeout(() => {}, 3000) or res.status(201)
변환: const ANIMATION_DELAY_MS = 3000 or const HTTP_CREATED = 201
```

#### 복잡한 조건 → 명확한 변수
```
감지: if (user && user.age >= 18 && user.email && !user.suspended)
변환: const isEligibleUser = /* 조건 */
```

#### 중첩 삼항 연산자 → Early Return 또는 조건문
```
감지: return loading ? <Spinner /> : error ? <Error /> : <Data />
변환: if (loading) return <Spinner />; if (error) return <Error />; return <Data />;
```

### 2.5 🧠 데이터 및 상태 관리 자동 선택

#### **[프론트엔드]** UI/클라이언트 상태
```
단순 UI 상태 → useState
복잡한 로컬 상태 → useReducer  
전역 UI 상태 → Context API
전역 앱 상태 → Zustand, Recoil
서버 상태 캐싱 → TanStack Query
```

#### **[백엔드]** 데이터/서버 상태
```
휘발성 캐시 데이터 → In-memory Cache (Redis, Memcached)
관계형 데이터 → RDBMS (PostgreSQL, MySQL)
비정형/대용량 데이터 → NoSQL (MongoDB, DynamoDB)
전문 검색/로그 → Search Engine (Elasticsearch, OpenSearch)
메시지 큐 → RabbitMQ, Kafka
```

### 2.6 📋 비동기/API 처리 표준

#### **[프론트엔드]** 클라이언트 관점
모든 비동기 작업은 3가지 상태를 관리:
- `data`: 성공 시 데이터
- `isLoading`: 로딩 상태
- `error`: 에러 상태

#### **[백엔드]** 서버 API 응답 관점
표준화된 응답 구조를 사용:
```json
{
  "data": { /* 성공 시 페이로드 */ },
  "message": "요청이 성공적으로 처리되었습니다.",
  "error": null // 실패 시 에러 코드 또는 메시지
}
```

### 2.7 🚫 금지 패턴 자동 회피

#### TypeScript / JavaScript
```
❌ any → ✅ unknown + 타입 가드
❌ as any → ✅ 올바른 타입 정의
❌ @ts-ignore → ✅ 타입 문제 해결
❌ var → ✅ const/let
❌ == → ✅ ===
❌ eval() → ✅ 대안 구현
```

#### **[프론트엔드]** React & CSS
```
❌ dangerouslySetInnerHTML → ✅ 안전한 렌더링
❌ Props Drilling (3+) → ✅ Context or 합성
❌ !important → ✅ 구체적 선택자
❌ 인라인 스타일 남용 → ✅ CSS 클래스
```

#### **[백엔드]**
```
❌ N+1 쿼리 문제 → ✅ Eager/Lazy 로딩, Dataloader 패턴으로 해결
❌ 트랜잭션 부재 → ✅ 중요한 CUD 작업은 트랜잭션으로 묶기
❌ 로그/응답에 민감 데이터 노출 → ✅ DTO를 통한 데이터 필터링
❌ 동기 I/O 블로킹 (Node.js) → ✅ 비동기 I/O 사용
```

### 2.8 🚀 특수 명령어 실행

- **"최적화"**: 성능 개선 (**프론트**: 메모이제이션, 번들 사이즈 / **백엔드**: 쿼리 최적화, 인덱싱)
- **"접근성 강화"**: **[프론트엔드]** ARIA, 키보드 지원 등 추가
- **"보안 강화"**: **[백엔드]** SQL 인젝션 방어, 인증/인가 로직 추가, 입력값 검증 강화
- **"타입 강화"**: any 제거, 타입 안정성 개선
- **"클린업"**: 요청 시에만 불필요한 코드 정리
- **"분리"**: 요청 시에만 컴포넌트/함수/모듈 분리

---

## 💬 Part 3. 커뮤니케이션 가이드

### 3.1 코드 제공 형식

```markdown
### 작업 범위
"요청하신 UserProfile 컴포넌트의 상태 관리 로직과 User API의 응답 형식을 수정했습니다."

### 변경사항 요약
- **프론트엔드**: 주문 상태 업데이트 로직에 낙관적 업데이트 적용
- **백엔드**: 사용자 조회 시 불필요한 `password` 필드 제외

### 코드
[전체 코드 블록]

### 주의사항
- 에러 발생 시 자동 롤백됩니다.
- 네트워크 재시도는 클라이언트에서 최대 3회 수행합니다.
```

### 3.2 리뷰 응답 형식

```markdown
### 개선점
1. **[성능]** `user.posts` 조회 시 N+1 쿼리가 발생할 수 있습니다.
2. **[안정성]** 이메일 전송 실패 시 에러 처리가 누락되었습니다.

### 권장사항
- `findAll` 쿼리에서 `posts`를 함께 조회(JOIN)하도록 수정하는 것을 권장합니다.
- 이메일 전송 로직을 `try-catch`로 감싸고, 실패 시 재시도 큐에 넣는 로직을 추가하는 것을 고려해 보세요.
```

---

## ✅ Part 4. 최종 검증 체크리스트

### 4.1 코드 품질 체크

```typescript
const codeQualityCheck = {
  // 공통 원칙
  obeysTheGoldenRule: true,     // ✅ 요청 범위만 수정
  preservesWorkingCode: true,   // ✅ 기존 코드 보존
  singleResponsibility: true,   // ✅ 단일 책임
  consistentNaming: true,       // ✅ 일관된 네이밍
  noMagicNumbers: true,         // ✅ 매직 넘버 없음
  
  // 타입 안정성 (TS)
  noAnyType: true,              // ✅ any 타입 없음
  strictNullCheck: true,        // ✅ null/undefined 체크
  
  // 에러 및 상태 처리
  robustErrorHandling: true,    // ✅ try-catch/에러 상태, 적절한 HTTP 상태코드
  hasLoadingState: true,        // ✅ (프론트) 로딩 상태
  gracefulShutdown: true,       // ✅ (백엔드) 프로세스 종료 처리
  
  // **프론트엔드**
  semanticHTML: true,           // ✅ 시맨틱 태그
  keyboardAccessible: true,     // ✅ 키보드 접근
  noUnnecessaryRenders: true,   // ✅ 불필요한 리렌더 없음
  
  // **백엔드**
  secureAgainstVulns: true,     // ✅ 일반적 보안 취약점 방어 (OWASP)
  hasDataValidation: true,      // ✅ 입력 데이터 검증
  optimizedQueries: true,       // ✅ N+1 등 비효율 쿼리 없음
  hasLogging: true,             // ✅ 주요 로직 로깅
};
```

### 4.2 프로젝트 체크

```typescript
const projectCheck = {
  // 의존성
  noUnusedDeps: true,           // ✅ 미사용 패키지 없음
  noDuplicateDeps: true,        // ✅ 중복 기능 패키지 없음
  
  // 파일 구조
  consistentStructure: true,    // ✅ 일관된 폴더 구조
  noCircularDeps: true,         // ✅ 순환 참조 없음
  
  // **프론트엔드 최적화**
  treeShaking: true,            // ✅ 트리 쉐이킹
  codeSplitting: true,          // ✅ 코드 스플리팅
  
  // **백엔드 및 인프라**
  hasCI_CD: true,               // ✅ CI/CD 파이프라인 존재
  hasContainerization: true,    // ✅ Dockerfile 등 컨테이너화
  envVarManagement: true,       // ✅ 환경변수 분리 (.env)
};
```

---

**Remember**:
> "코드는 한 번 작성되지만, 수십 번 읽힌다. 미래의 당신과 동료를 위해 명확하게 작성하라."
