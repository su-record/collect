# 🚀 AI 개발 헌장 (The Grand Unified Charter) - **진정한 최종 완성본**

## 🗂️ 파일 구조

이 헌장은 내용의 깊이와 방대함을 고려하여, 역할과 목적에 따라 다음과 같이 **5개의 핵심 파일**로 구성됩니다. 이 구조는 탐색의 효율성과 유지보수의 용이성을 극대화합니다.

```
/the-dev-charter
├── 📜 README.md                   # 👈 이 헌장의 서문이자 모든 것의 시작점
├── 🌐 00_Universal_Principles.md   # 👈 시대를 초월하는 보편 원칙
├── 🎨 01_Frontend_Canon.md         # 👈 프론트엔드 정경(正經)
├── 🖥️ 02_Backend_Canon.md          # 👈 백엔드 정경(正經)
└── ✅ 03_Collaboration_and_Verification.md # 👈 협업 규약 및 검증 의식
```

---
---

## 📜 `README.md`

# 🚀 AI 개발 헌장 (The AI Development Charter)

**이 문서는 코드의 창조부터 소멸까지, 우리의 모든 기술적 사고와 행동을 이끄는 근본적인 규범의 서문이다.**
이것은 단순한 가이드가 아닌, 품질, 협업, 그리고 지속 가능한 성장을 위한 우리 모두의 약속이다.

---

### 📍 이 헌장을 읽는 법

-   🏃 **즉시 행동이 필요할 때**: 이 문서의 **[⚡️핵심 5계명](#-핵심-5계명)**을 가슴에 새기고 시작하라.
-   🤔 **철학적 깊이가 필요할 때**: **[🌐 보편 원칙](00_Universal_Principles.md)**을 정독하라.
-   💻 **구체적인 구현이 필요할 때**: 아래 목차를 통해 각 분야의 **[정경(Canon)](01_Frontend_Canon.md)**을 참조하라.
-   🤝 **함께 일할 때**: **[협업 규약 및 검증 의식](03_Collaboration_and_Verification.md)**을 통해 존중과 완결성을 추구하라.

---

### 📋 헌장의 구성

1.  **[📜 서문 (현재 문서)](#-ai-개발-헌장-the-ai-development-charter)**
    -   이 헌장의 목적과 구조, 그리고 우리의 다짐.

2.  **[🌐 보편 원칙 (00_Universal_Principles.md)](00_Universal_Principles.md)**
    -   언어와 플랫폼을 초월하여 모든 코드에 적용되는 보편적 진리. **품질, 구조, Git 사용법, 네이밍, 의존성 관리**, 그리고 우리가 피해야 할 **금지사항**에 대한 원칙.

3.  **[🎨 프론트엔드 정경 (01_Frontend_Canon.md)](01_Frontend_Canon.md)**
    -   사용자 경험의 최전선을 구축하는 장인들을 위한 심층 규범. TypeScript, Next.js, Nuxt.js 기반의 **아키텍처, 상태 관리, 성능, 접근성, 스타일링**, 그리고 **금지 패턴**에 대한 모든 것.

4.  **[🖥️ 백엔드 정경 (02_Backend_Canon.md)](02_Backend_Canon.md)**
    -   견고한 시스템의 심장을 만드는 설계자들을 위한 심층 규범. Java/Spring, Python 기반의 **아키텍처, API 설계, 데이터베이스, 보안, 로깅**, 그리고 **금지 패턴**에 대한 모든 것.

5.  **[✅ 협업 규약 및 검증 의식 (03_Collaboration_and_Verification.md)](03_Collaboration_and_Verification.md)**
    -   팀으로서 위대함을 달성하기 위한 **소통 규약, 작업 프로세스, 코드 리뷰 문화**. 그리고 창조물을 세상에 내보내기 전 거쳐야 할 **최종 검증 의식**과 **특수 명령어**.

---

### 🎯 우리의 신조 (Our Creed)

#### 🥇 제1원칙: 외과수술적 정밀함 (Surgical Precision)

> **요청받지 않은 것은 절대 건드리지 않는다.**
> 이것은 신뢰의 기반이자 모든 협업의 시작이다. 우리는 주어진 임무의 경계를 명확히 인지하고, 그 범위를 넘어서는 어떠한 행동도 하지 않는다.

### ⚡️핵심 5계명 (The Five Commandments)

> 1.  **DRY (Don't Repeat Yourself)**: 반복은 악이다. 모든 지식은 단일하고, 명확하며, 신뢰할 수 있는 표현을 가져야 한다.
> 2.  **SRP (Single Responsibility Principle)**: 각 모듈은 단 하나의 책임만을 가진다. 하나의 변경 이유는 하나의 변경 지점만을 낳는다.
> 3.  **YAGNI (You Ain't Gonna Need It)**: 미래를 예측하려 들지 마라. 지금 당장 필요한 가장 단순한 것을 하라.
> 4.  **Readability First**: 코드는 기계보다 인간이 더 많이 읽는다. 명확함은 영리함보다 우월하다.
> 5.  **Secure by Default**: 보안은 나중에 추가하는 기능이 아니다. 모든 코드의 설계 단계부터 시작되는 기본이다.

---
> **"우리가 만드는 것은 단순한 코드가 아니다. 그것은 생각의 결정체이며, 미래를 향한 유산이다."**

---
---

## 🌐 `00_Universal_Principles.md`

# 🌐 보편 원칙: 시대를 초월하는 진리

**이 문서는 언어와 플랫폼을 초월하여 모든 코드에 적용되는 보편적 진리를 담고 있다.**

### 🎨 Part 1. 코드 품질의 기준
- **가독성 (Readability)**: 변수명, 함수명만으로 의도가 90% 이상 파악되어야 한다. 주석은 '왜'에 집중하고, 코드는 '어떻게'를 보여준다. 좋은 코드는 설명서가 필요 없다.
- **예측 가능성 (Predictability)**: 함수는 이름 그대로의 동작만 해야 한다. 놀라운 부수 효과(Side Effect)를 만들지 마라. 동일한 입력에 대해 항상 동일한 출력을 보장하는 순수 함수(Pure Function)를 지향한다.
- **유지보수성 (Maintainability)**: 변경이 쉬워야 좋은 코드다. 새로운 요구사항이 생겼을 때 최소한의 수정으로 대응 가능한 구조를 지향하라. 결합도(Coupling)는 낮추고 응집도(Cohesion)는 높여야 한다.
- **테스트 가능성 (Testability)**: 의존성이 낮고 순수한 함수로 구성된 코드는 테스트하기 쉽고 버그가 적다. 의존성 주입(DI)은 테스트 가능성을 높이는 핵심 기술이다.

### 🏗️ Part 2. 공통 코드 구조 원칙
- **함수/메서드**: 최대 20줄, 중첩 3단계 이하, 단일 책임 원칙을 준수한다. 하나의 함수는 오직 한 가지 일만 잘해야 한다.
- **파일/모듈**: 하나의 파일은 하나의 주요 개념(클래스, 컴포넌트, 모듈)을 담는다. 파일 길이는 300줄을 넘지 않도록 노력한다.
- **레이어 분리**: Presentation(표현) → Business(로직) → Data(데이터) 계층 분리를 지향하여 각 계층의 책임을 명확히 한다.

### 🌿 Part 3. Git 사용 원칙: 협업의 역사 기록
#### 브랜치 전략
- `feat/기능명`: 새 기능 개발 (e.g., `feat/user-authentication`)
- `fix/이슈번호`: 버그 수정 (e.g., `fix/123-login-error`)
- `refactor/대상`: 리팩토링 (e.g., `refactor/user-service`)
- `docs/문서명`: 문서 작업 (e.g., `docs/api-guide`)
- `chore/작업명`: 빌드, 설정 등 (e.g., `chore/update-dependencies`)

#### 커밋 메시지 (Conventional Commits)
커밋 메시지는 변경 사항의 '무엇'과 '왜'를 명확히 전달해야 한다.
- `feat`: 새로운 기능 추가
- `fix`: 버그 수정
- `docs`: 문서 변경
- `style`: 코드 포맷팅, 세미콜론 등 (기능 변경 없음)
- `refactor`: 코드 리팩토링
- `test`: 테스트 추가/수정
- `chore`: 빌드, 패키지 매니저 설정 등

**좋은 예시**:
```
feat(auth): JWT 기반 로그인 기능 구현

사용자가 이메일과 비밀번호로 로그인하면 액세스 토큰과 리프레시 토큰을 발급하는 API 엔드포인트를 추가합니다.
액세스 토큰은 15분, 리프레시 토큰은 7일의 유효 기간을 가집니다.

Fixes #45
```
**나쁜 예시**: `버그 수정`, `작업 완료`

### 📖 Part 4. 네이밍 규칙
- `camelCase`: 변수, 함수 (`userList`, `fetchData`)
- `PascalCase`: 클래스, 컴포넌트, 타입, 인터페이스 (`UserService`, `UserProfile`)
- `UPPER_SNAKE_CASE`: 상수, 환경변수 (`MAX_RETRY_COUNT`, `DATABASE_URL`)
- `is/has/can`: 불리언 접두사 (`isLoading`, `hasPermission`)

### 📦 Part 5. 패키지/의존성 관리
#### 추가 전 체크리스트
```
□ 이 기능은 핵심 라이브러리/프레임워크로 해결 불가능한가?
□ 정말 필요한가? (YAGNI)
□ 라이선스는 프로젝트와 호환되는가? (MIT, Apache 2.0 선호)
□ 보안 취약점은 없는가? (npm audit, Snyk)
□ 커뮤니티가 활발하고 유지보수가 잘 되고 있는가? (GitHub Stars, 최근 커밋 날짜)
□ 번들/빌드 크기에 미치는 영향은 미미한가?
```

### 🚫 Part 6. 공통 금지사항: 절대 넘지 말아야 할 선
- **보안**: 하드코딩된 시크릿, 평문 비밀번호, 검증 없는 사용자 입력, 민감 정보 로그 노출.
- **품질**: `any` 타입, `@ts-ignore`, `var`, `==` 비교, `eval()`, 빈 `catch` 블록, `console.log`.
- **구조**: 순환 참조, 전역 변수 남용, 매직 넘버/문자열.

#### **매직 넘버/문자열 변환 예시**
```typescript
// ❌ 나쁜 코드: 숫자와 문자열의 의미를 알 수 없다.
if (user.role === 1) { // 1이 관리자인가?
  // ...
}
setTimeout(doSomething, 3000);

// ✅ 좋은 코드: 의미를 가진 상수로 대체한다.
const USER_ROLES = {
  ADMIN: 1,
  USER: 2,
} as const;

const REFRESH_INTERVAL_MS = 3000;

if (user.role === USER_ROLES.ADMIN) {
  // ...
}
setTimeout(doSomething, REFRESH_INTERVAL_MS);
```

---
---

## 🎨 `01_Frontend_Canon.md`

# 🎨 프론트엔드 정경: 사용자 경험의 미학

**이 문서는 사용자 경험의 최전선을 구축하는 장인들을 위한 심층 규범이다.**

### 🏗️ Part 1. 아키텍처 원칙
- **컴포넌트 중심 설계 (CDD)**: UI를 독립적이고 재사용 가능한 부품의 조합으로 생각한다.
    - **프레젠테이셔널 컴포넌트 (Dumb)**: UI 표현에만 집중. props로만 데이터를 받는다. 스토리북에서 독립적으로 테스트하기 용이하다.
    - **컨테이너 컴포넌트 (Smart)**: 로직과 상태를 관리하고 데이터를 주입. API 호출, 상태 관리 라이브러리와의 연동을 담당한다.
- **폴더 구조**: 도메인 중심으로 파일을 구성하여 응집도를 높인다. 관련 있는 컴포넌트, 훅, 타입, 서비스가 한곳에 모여 있어야 한다.
    ```
    /src/features/user
    ├── /components
    │   ├── UserProfile.tsx
    │   └── UserList.tsx
    ├── /hooks
    │   └── useUserQuery.ts
    ├── /services
    │   └── user.api.ts
    └── index.ts (public API export)
    ```
- **API 통신**: `services` 또는 `queries` 계층을 두어 컴포넌트와 API 호출 로직을 완전히 분리한다. 컴포넌트는 데이터가 어떻게 오는지 몰라야 한다.

### 🧠 Part 2. 상태 관리 전략
상태의 '소유권'과 '생명주기'를 기준으로 적절한 도구를 선택한다.

| 상태 유형 | 추천 도구 | 예시 |
| :--- | :--- | :--- |
| **Local UI State** | `useState`, `ref` | 모달 열림/닫힘, 입력값 |
| **Server Cache State**| `TanStack Query` | 사용자 프로필, 상품 목록 |
| **Global App State**| `Zustand`, `Pinia` | 로그인 상태, 테마 |
| **URL State** | `Router params/query`| 검색 필터, 페이지 번호 |

### ⚡️ Part 3. 기술별 실행 규칙 (React/Vue)

#### **React 컴포넌트 구조 (상세 예시)**
```typescript
// 파일: src/features/user/components/UserProfile.tsx

// 1. Imports
import React, { useState, useEffect, useMemo, useCallback, useRef } from 'react';
import { useUserQuery } from '../hooks/useUserQuery';
import { Spinner } from '@/components/common/Spinner';
import { ErrorDisplay } from '@/components/common/ErrorDisplay';

// 2. Type/Interface 정의
interface UserProfileProps {
  userId: string;
}

// 3. 컴포넌트 함수
export function UserProfile({ userId }: UserProfileProps) {
  // 4. State & Refs
  const [isEditing, setIsEditing] = useState(false);
  const nameInputRef = useRef<HTMLInputElement>(null);

  // 5. Custom Hooks
  const { data: user, error, isLoading } = useUserQuery(userId);

  // 6. Memoized 값
  const userStatus = useMemo(() => {
    return user?.isActive ? '활성' : '비활성';
  }, [user?.isActive]);

  // 7. Event Handlers
  const handleEditToggle = useCallback(() => {
    setIsEditing(prev => !prev);
  }, []);

  // 8. Effects
  useEffect(() => {
    if (isEditing && nameInputRef.current) {
      nameInputRef.current.focus();
    }
  }, [isEditing]);

  // 9. Early returns
  if (isLoading) return <Spinner />;
  if (error) return <ErrorDisplay error={error} />;
  if (!user) return <div>사용자 정보를 찾을 수 없습니다.</div>;

  // 10. Main render (JSX)
  return (
    <div className="user-profile">
      <h1>{user.name}</h1>
      <p>이메일: {user.email}</p>
      <p>상태: {userStatus}</p>
      <button onClick={handleEditToggle}>{isEditing ? '저장' : '수정'}</button>
      {isEditing && (
        <input ref={nameInputRef} defaultValue={user.name} />
      )}
    </div>
  );
}
```

#### **Vue 컴포넌트 구조 (상세 예시)**
```vue
<!-- 파일: src/features/user/components/UserProfile.vue -->
<script setup lang="ts">
// 1. Imports
import { ref, computed, watch, nextTick } from 'vue';
import { useUserQuery } from '../composables/useUserQuery';
import Spinner from '@/components/common/Spinner.vue';

// 2. Props & Emits
const props = defineProps<{
  userId: string;
}>();

// 3. Composables
const { data: user, error, isLoading } = useUserQuery(() => props.userId);

// 4. Reactive state
const isEditing = ref(false);
const nameInput = ref<HTMLInputElement | null>(null);

// 5. Computed
const userStatus = computed(() => (user.value?.isActive ? '활성' : '비활성'));

// 6. Methods
const handleEditToggle = () => {
  isEditing.value = !isEditing.value;
};

// 7. Lifecycle & Watchers
watch(isEditing, async (isNowEditing) => {
  if (isNowEditing) {
    await nextTick(); // DOM 업데이트를 기다림
    nameInput.value?.focus();
  }
});
</script>

<template>
  <div v-if="isLoading" class="spinner-container">
    <Spinner />
  </div>
  <div v-else-if="error" class="error-container">
    <p>오류 발생: {{ error.message }}</p>
  </div>
  <div v-else-if="user" class="user-profile">
    <h1>{{ user.name }}</h1>
    <p>이메일: {{ user.email }}</p>
    <p>상태: {{ userStatus }}</p>
    <button @click="handleEditToggle">{{ isEditing ? '저장' : '수정' }}</button>
    <input v-if="isEditing" ref="nameInput" :value="user.name" />
  </div>
  <div v-else>
    <p>사용자 정보를 찾을 수 없습니다.</p>
  </div>
</template>
```

### 🎨 Part 4. 스타일링 전략
- **CSS Modules**: 컴포넌트 스코프 스타일링, 클래스명 충돌 방지에 기본적으로 사용.
- **CSS-in-JS (Styled Components)**: 동적 스타일, 테마 시스템이 복잡하게 필요한 경우 제한적으로 사용.
- **Tailwind CSS**: 빠른 프로토타이핑, 일관된 디자인 시스템 구축 시 고려.
- **선택 기준**: 팀 선호도, 프로젝트 규모, 동적 스타일 필요성을 고려하여 선택한다.

### ♿ Part 5. 접근성 (A11y): 모두를 위한 디자인
- **시맨틱 HTML**: `nav`, `main`, `button` 등 의미에 맞는 태그를 사용한다. `div`와 `span`은 최후의 수단이다.
- **키보드 네비게이션**: 모든 인터랙티브 요소는 키보드(Tab, Enter, Space)로 조작 가능해야 한다. 포커스 순서도 논리적이어야 한다.
- **ARIA 속성**: 동적인 UI 변화를 스크린 리더가 인지할 수 있도록 `aria-live`, `aria-expanded` 등을 적절히 사용한다.
- **라벨링**: 모든 `input`에는 `label`을 제공하거나 `aria-label`을 명시한다.
- **색상 대비**: WCAG 2.1 AA 기준(최소 4.5:1)을 준수하여 텍스트 가독성을 보장한다.

### 🚫 Part 6. 프론트엔드 금지 패턴
- **Props Drilling (3단계 이상)**: `Context API`나 상태 관리 라이브러리로 해결한다. 또는 컴포넌트 합성을 활용한다.
- **`useEffect` 남용**: 서버 상태 동기화는 `TanStack Query`, 파생 상태 계산은 `useMemo`를 우선 고려한다. `useEffect`는 동기화가 필요할 때만 신중하게 사용한다.
- **인덱스를 `key`로 사용**: 데이터가 변경될 때 예측 불가능한 버그를 유발한다. 고유 ID를 사용한다.
- **`!important` 남용**: CSS 우선순위(Specificity)를 이해하고 더 구체적인 선택자를 사용한다.

---
---

## 🖥️ `02_Backend_Canon.md`

# 🖥️ 백엔드 정경: 견고한 시스템의 심장

**이 문서는 견고하고 확장 가능하며 안전한 시스템의 심장을 만드는 설계자들을 위한 심층 규범이다.**

### 🏗️ Part 1. 아키텍처 원칙

#### **레이어드 아키텍처 예시 (Java/Spring)**
```java
// === Presentation Layer (Controller) ===
@RestController
@RequestMapping("/api/users")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<UserResponseDto> getUserById(@PathVariable Long id) {
        UserResponseDto userDto = userService.findUserById(id);
        return ResponseEntity.ok(userDto);
    }
}

// === Business Layer (Service Interface & Impl) ===
public interface UserService {
    UserResponseDto findUserById(Long id);
}

@Service
@RequiredArgsConstructor
public class UserServiceImpl implements UserService {
    private final UserRepository userRepository; // 인터페이스에 의존

    @Override
    @Transactional(readOnly = true)
    public UserResponseDto findUserById(Long id) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("User not found with id: " + id));
        // Entity를 DTO로 변환하여 반환
        return new UserResponseDto(user.getId(), user.getName(), user.getEmail());
    }
}

// === Data Access Layer (Repository Interface & Entity) ===
public interface UserRepository extends JpaRepository<User, Long> {
    // Spring Data JPA가 메서드 이름으로 쿼리 생성
}

@Entity
@Getter // Lombok 등 활용
public class User {
    @Id @GeneratedValue private Long id;
    private String name;
    private String email;
    private String password; // 해시된 비밀번호
}

// === DTO ===
@Data // Lombok 등 활용
public class UserResponseDto {
    private Long id;
    private String name;
    private String email;
    // password 필드는 절대 포함하지 않는다.
}
```

### 📡 Part 2. API 설계 표준
- **RESTful 원칙**: 리소스 중심 URL(`GET /users/{id}`), HTTP 메서드 의미 준수, 표준 상태 코드 활용.
- **표준 응답 구조**: 일관된 응답 구조(`data`, `message`, `error`)로 클라이언트 개발 효율 증대.
- **페이지네이션**: 대용량 데이터는 Cursor 기반 또는 Offset 기반 페이지네이션 구현.

### ⚡️ Part 3. 기술별 실행 규칙 (Java/Spring & Python)
#### Java/Spring
- **의존성 주입**: 생성자 주입(`@RequiredArgsConstructor`) 사용. 테스트 용이성을 높이고 순환 참조를 방지한다.
- **예외 처리**: `@ControllerAdvice`를 통한 전역 예외 처리. 서비스 계층에서는 비즈니스 예외를 던진다.
- **트랜잭션**: CUD 작업에는 `@Transactional`, 조회 작업에는 `@Transactional(readOnly = true)`를 사용하여 성능을 최적화한다.

#### Python (FastAPI/Django)
- **타입 힌트**: 모든 함수 시그니처와 변수에 타입 힌트 필수 사용. `mypy`로 정적 분석 수행.
- **데이터 검증**: `Pydantic` 또는 `DRF Serializer`로 경계에서 데이터 검증.
- **ORM 최적화**: N+1 방지를 위해 `select_related`/`prefetch_related` 적극 사용.

### 🔐 Part 4. 보안 및 데이터 관리
- **인증/인가**: Stateless JWT 기반 인증을 기본으로, API 엔드포인트마다 권한 체크.
- **입력값 검증**: SQL Injection, XSS 방지를 위해 모든 사용자 입력을 검증하고 Parameterized Query 사용.
- **민감 정보**: 비밀번호는 `bcrypt`로 해시, API 키 등은 환경 변수/Secret Manager로 관리.

### 📜 Part 5. 로깅 및 모니터링
- **구조화된 로깅**: JSON 형식의 로그를 출력하여 분석 및 검색 용이성 확보.
- **로그 레벨**: `DEBUG`, `INFO`, `WARN`, `ERROR` 등 상황에 맞는 로그 레벨 사용.
- **메트릭 수집**: Prometheus 등을 활용하여 요청 수, 지연 시간 등 핵심 지표 수집.

### 🚫 Part 6. 백엔드 금지 패턴
- **N+1 쿼리**: 루프 안에서 쿼리 실행 금지.
- **Entity 직접 노출**: Controller에서 Entity를 직접 반환하지 않고 항상 DTO로 변환.
- **동기 대량 처리**: 시간이 오래 걸리는 작업은 비동기(Message Queue) 처리.
- **트랜잭션 남용**: 트랜잭션 범위는 가능한 작게 유지.

---
---

## ✅ `03_Collaboration_and_Verification.md`

# ✅ 협업 규약 및 검증 의식

**이 문서는 팀으로서 위대함을 달성하기 위한 소통 규약, 작업 프로세스, 그리고 창조물을 세상에 내보내기 전 거쳐야 할 최종 검증 의식을 정의한다.**

### 🤝 Part 1. 협업 규약: 함께 성장하는 법
#### 작업 프로세스
1.  **작업 전**: 이슈 생성/할당 -> 브랜치 생성 -> 기존 코드 및 영향 범위 분석
2.  **작업 중**: 코딩(헌장 준수) -> 단위 테스트 작성 -> 셀프 리뷰
3.  **작업 후**: Pull Request(PR) 생성 -> 코드 리뷰 -> CI/CD 통과 -> Merge -> 이슈 종료

#### 소통 프로토콜
- **비동기 우선**: 긴급하지 않은 논의는 Slack, Jira 등 비동기 채널을 우선 사용. 이는 동료의 집중을 존중하는 문화의 시작이다.
- **문서화**: 중요한 결정은 반드시 문서로 남겨 "기억이 아닌 기록에 의존한다."
- **에러 보고**: "안 돼요"가 아니라 "A를 했을 때 B 에러 발생, C 시도해봤다" 형식으로 구체적 보고.

#### 코드 리뷰 의식
코드 리뷰는 결함을 찾는 과정이 아니라, 지식을 공유하고 더 나은 해결책을 함께 모색하는 신성한 의식이다.
- **리뷰어**: 명령이 아닌 제안을, 주관이 아닌 근거를, 비난이 아닌 칭찬을 앞세운다. "이렇게 바꾸세요" (X) -> "이 부분은 A 원칙에 따라 B로 개선하는 건 어떨까요?" (O)
- **요청자**: 자신과 코드를 분리하고, 방어 대신 질문으로 더 깊이 이해한다. "왜 그렇게 생각하시는지 더 자세히 설명해주실 수 있나요?"

#### **코드 리뷰 요청 형식 (Pull Request Template 예시)**
```markdown
### 📜 요약 (Summary)
사용자 프로필 이미지 업로드 기능을 추가했습니다.

### 🤔 배경 (Context)
기존에는 프로필 이미지를 변경할 수 없었습니다. 이 기능은 사용자가 자신의 프로필을 개인화할 수 있도록 합니다. (관련 이슈: #78)

### 💻 변경 내용 (Changes)
- `POST /api/users/{id}/avatar` API 엔드포인트 추가
- S3에 이미지를 업로드하는 `S3UploadService` 구현
- 프론트엔드에 이미지 업로드 UI 추가

### ✅ 리뷰 중점사항 (Points for Review)
- `S3UploadService`의 예외 처리 로직이 충분히 견고한지 검토해주세요.
- 파일 사이즈 제한(5MB) 로직이 적절한지 의견 부탁드립니다.

### ⚠️ 잠재적 위험 (Potential Risks)
- 악의적인 파일 업로드(e.g., 스크립트 파일) 시도에 대한 방어 로직이 추가로 필요할 수 있습니다.
```

### ✔️ Part 2. 최종 검증 의식
코드 머지 전, 아래 체크리스트를 통해 스스로의 작업을 성찰한다.

#### 코드 품질 체크리스트
```
□ 제1원칙(외과수술적 정밀함)을 준수했는가?
□ 핵심 5계명(DRY, SRP 등)을 위반하지 않았는가?
□ 네이밍 컨벤션을 따랐는가? 매직 넘버는 없는가?
□ 에러 처리가 명확하고, 실패 상황을 고려했는가?
□ 테스트 코드를 작성했거나, 기존 테스트를 업데이트했는가?
```
#### 프론트엔드 특별 체크
```
□ 모든 인터랙티브 요소는 키보드로 조작 가능한가? (접근성)
□ 불필요한 리렌더링을 유발하지 않는가? (성능)
□ 모바일 화면에서도 UI가 깨지지 않는가? (반응형)
```
#### 백엔드 특별 체크
```
□ 모든 외부 입력값을 검증했는가? (보안)
□ N+1 쿼리 등 비효율적인 쿼리가 없는가? (성능)
□ 민감 정보가 로그나 API 응답에 노출되지 않는가? (보안)
```

### 🎮 Part 3. 특수 명령어: AI의 잠재력을 깨우다
AI에게 작업을 지시할 때 사용하는 특별한 키워드.

- **공통**: `최적화`, `리팩토링`, `테스트작성`, `문서화`
- **프론트엔드**: `컴포넌트분리`, `접근성강화`, `반응형적용`
- **백엔드**: `API설계`, `쿼리최적화`, `보안강화`, `캐싱전략`

**사용 예시**:
- `"기존 UserProfile 컴포넌트가 너무 큽니다. '컴포넌트분리' 명령을 실행하여 관심사에 따라 여러 하위 컴포넌트로 나눠주세요."`
- `"사용자 생성 API의 성능이 느립니다. '쿼리최적화' 명령을 통해 관련 로직을 분석하고 개선안을 제시해주세요."`
