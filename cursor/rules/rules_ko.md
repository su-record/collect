# **📜 AI 개발 가이드라인 (AI Development Guideline)**

## 🗂️ 목차

본 가이드라인은 하나의 문서 안에서 다음과 같이 5개의 주요 섹션으로 구성됩니다. 각 항목을 클릭하면 해당 섹션으로 이동합니다.

1.  [**🏁 개요**](#-개요)
2.  [**🌐 공통 원칙**](#-공통-원칙)
3.  [**🎨 프론트엔드 가이드라인**](#-프론트엔드-가이드라인)
4.  [**⚙️ 백엔드 가이드라인**](#-백엔드-가이드라인)
5.  [**🤝 협업 및 검증 프로세스**](#-협업-및-검증-프로세스)

---

## 🏁 개요

**이 문서는 AI 개발 프로젝트의 일관성, 품질, 협업 효율성을 높이기 위한 기술 표준과 절차를 정의합니다.** 이는 단순한 권장 사항이 아닌, 품질, 협업, 그리고 지속 가능한 성장을 위한 팀 전체의 약속입니다.

### 🎯 핵심 원칙

#### **원칙 1: 작업 범위 명확화 (Scope Clarity)**

> **요청된 작업 범위 외의 코드는 수정하지 않는다.**
> 이는 팀원 간의 신뢰를 형성하고 예측 가능한 개발 환경을 만드는 기본 원칙입니다. 각자 맡은 작업의 경계를 명확히 인지하고, 그 범위를 벗어나는 수정은 지양합니다.

### 🔑 핵심 개발 원칙 5가지 (The Five Core Principles)

> 1.  **DRY (Don't Repeat Yourself)**: 코드의 중복을 피한다. 모든 로직과 정보는 시스템 내에서 단일하고 명확한 형태로 존재해야 한다.
> 2.  **SRP (Single Responsibility Principle)**: 각 모듈은 단 하나의 책임만을 가져야 한다. 변경의 이유는 하나여야 하며, 이는 단 하나의 모듈 수정으로 이어져야 한다.
> 3.  **YAGNI (You Ain't Gonna Need It)**: 현재 필요하지 않은 기능은 구현하지 않는다. 미래의 요구사항을 예측하기보다, 현재 시점에서 가장 단순하고 필요한 것을 구현한다.
> 4.  **Readability First**: 코드는 기계보다 사람이 더 많이 읽는다. 간결함이나 영리함보다 명확하고 가독성 높은 코드를 우선한다.
> 5.  **Secure by Default**: 보안은 부가 기능이 아니다. 모든 개발 단계에서 보안을 기본 원칙으로 고려하여 설계하고 구현한다.

---
> **"잘 작성된 코드는 그 자체로 명확한 설계 문서이다."**

---

## 🌐 공통 원칙

**이 섹션은 특정 언어나 플랫폼에 구애받지 않고 모든 코드에 보편적으로 적용되는 개발 원칙을 정의합니다.**

### ✨ 1. 코드 품질 원칙
- **가독성 (Readability)**: 변수와 함수 이름만으로 코드의 의도가 90% 이상 파악되어야 합니다. 코드가 '어떻게'를 설명하고, 주석은 '왜'를 보완합니다.
- **예측 가능성 (Predictability)**: 함수는 이름에 명시된 기능만 수행해야 하며, 예상치 못한 부수 효과(Side Effect)를 최소화해야 합니다. 순수 함수(Pure Function) 사용을 지향합니다.
- **유지보수성 (Maintainability)**: 변경에 용이한 코드를 작성합니다. 새로운 요구사항에 최소한의 수정으로 대응할 수 있도록 결합도(Coupling)를 낮추고 응집도(Cohesion)를 높이는 구조를 지향합니다.
- **테스트 용이성 (Testability)**: 의존성이 낮고 순수 함수로 구성된 코드는 테스트가 용이합니다. 의존성 주입(Dependency Injection)은 테스트 용이성을 높이는 핵심 기법입니다.

### 🏗️ 2. 공통 코드 구조 원칙
- **함수/메서드**: 최대 20줄, 중첩 3단계 이하를 권장하며, 단일 책임 원칙을 준수합니다.
- **파일/모듈**: 하나의 파일은 하나의 주요 개념(클래스, 컴포넌트 등)을 중심으로 구성하며, 300줄을 넘지 않도록 관리합니다.
- **레이어 분리**: 프레젠테이션(Presentation), 비즈니스(Business), 데이터 접근(Data Access) 계층을 분리하여 각 계층의 책임을 명확히 합니다.

### 🌿 3. Git 사용 정책
#### **브랜치 전략**
- `feat/{기능명}`: 신규 기능 개발 (예: `feat/user-authentication`)
- `fix/{이슈번호}`: 버그 수정 (예: `fix/123-login-error`)
- `refactor/{대상}`: 코드 리팩토링 (예: `refactor/user-service`)
- `docs/{문서명}`: 문서 수정 (예: `docs/api-guide`)
- `chore/{작업명}`: 빌드, 설정 등 기타 작업 (예: `chore/update-dependencies`)

#### **커밋 메시지 (Conventional Commits 표준 준수)**
커밋 메시지는 변경 사항의 '내용'과 '이유'를 명확하게 전달해야 합니다.
- `feat`: 새로운 기능 추가
- `fix`: 버그 수정
- `docs`: 문서 변경
- `style`: 코드 포맷팅, 세미콜론 등 (기능 변경 없음)
- `refactor`: 코드 리팩토링
- `test`: 테스트 코드 추가/수정
- `chore`: 빌드, 패키지 매니저 설정 등

**좋은 예시**:
```
feat(auth): JWT 기반 로그인 기능 구현

사용자가 이메일과 비밀번호로 로그인하면 액세스 토큰과 리프레시 토큰을 발급하는 API 엔드포인트를 추가합니다.
액세스 토큰은 15분, 리프레시 토큰은 7일의 유효 기간을 가집니다.

Fixes #45
```
**나쁜 예시**: `버그 수정`, `작업 완료`

### 🏷️ 4. 네이밍 컨벤션
- `camelCase`: 변수, 함수 (`userList`, `fetchData`)
- `PascalCase`: 클래스, 컴포넌트, 타입, 인터페이스 (`UserService`, `UserProfile`)
- `UPPER_SNAKE_CASE`: 상수, 환경 변수 (`MAX_RETRY_COUNT`, `DATABASE_URL`)
- `is/has/can`: 불리언(Boolean) 값 접두사 (`isLoading`, `hasPermission`)

### 📦 5. 패키지/의존성 관리
#### **라이브러리 추가 전 검토 항목**
```
□ 핵심 라이브러리/프레임워크로 해결 불가능한 기능인가?
□ 과잉 설계는 아닌가? (YAGNI 원칙)
□ 프로젝트 라이선스와 호환되는가? (MIT, Apache 2.0 선호)
□ 보고된 보안 취약점은 없는가? (npm audit, Snyk 등 활용)
□ 커뮤니티가 활성화되어 있고, 지속적으로 유지보수되고 있는가?
□ 번들/빌드 크기에 미치는 영향은 허용 가능한 수준인가?
```

### 🚫 6. 공통 금지사항
- **보안**: 소스 코드 내 비밀번호, API 키 등 민감 정보 하드코딩, 검증되지 않은 사용자 입력값 사용, 민감 정보 로깅.
- **품질**: `any` 타입의 무분별한 사용, `@ts-ignore`, `var`, `==` 비교 연산자, `eval()`, 빈 `catch` 블록, 운영 코드 내 `console.log`.
- **구조**: 순환 참조, 전역 변수 남용, 의미를 알 수 없는 매직 넘버/문자열 사용.

#### **매직 넘버/문자열 개선 예시**
```typescript
// ❌ Bad: 숫자와 문자열의 의미를 파악하기 어렵다.
if (user.role === 1) { // 1: 관리자? 일반 사용자?
  // ...
}
setTimeout(doSomething, 3000);

// ✅ Good: 의미를 명확히 나타내는 상수로 대체한다.
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

## 🎨 프론트엔드 가이드라인

**이 섹션은 사용자 인터페이스(UI)와 사용자 경험(UX)의 품질을 보장하기 위한 가이드라인입니다.**

### 🏗️ 1. 아키텍처 원칙
- **컴포넌트 중심 설계 (Component-Driven Development)**: UI를 독립적이고 재사용 가능한 단위로 설계합니다.
    - **프레젠테이셔널(Presentational) 컴포넌트**: UI 렌더링에만 집중하며, props로 데이터를 전달받습니다.
    - **컨테이너(Container) 컴포넌트**: 비즈니스 로직과 상태를 관리하며, API 호출 및 상태 관리 라이브러리와 연동합니다.
- **폴더 구조**: 도메인 중심으로 파일을 구성하여 응집도를 높입니다. 관련된 컴포넌트, 훅, 타입, API 모듈을 동일한 디렉토리 내에 위치시킵니다.
    ```
    /src/features/user
    ├── /components
    │   ├── UserProfile.tsx
    │   └── UserList.tsx
    ├── /hooks
    │   └── useUserQuery.ts
    ├── /services
    │   └── user.api.ts
    └── index.ts (외부에 노출할 모듈 export)
    ```
- **API 통신 계층화**: 컴포넌트와 API 호출 로직을 분리하기 위해 `services` 또는 `api` 계층을 사용합니다. 컴포넌트는 데이터 fetching의 구체적인 구현을 알 필요가 없습니다.

### 🧠 2. 상태 관리 전략
상태의 범위와 생명주기에 따라 적절한 도구를 선택합니다.

| 상태 유형 | 추천 도구 | 예시 |
| :--- | :--- | :--- |
| **컴포넌트 지역 상태** | `useState`, `ref` | 모달 열림/닫힘, 입력값 |
| **서버 캐시 상태**| `TanStack Query` | 사용자 프로필, 상품 목록 |
| **전역 애플리케이션 상태**| `Zustand`, `Pinia` | 로그인 상태, 테마 |
| **URL 파생 상태** | `Router params/query`| 검색 필터, 페이지 번호 |

### 💻 3. 기술별 구현 가이드 (React/Vue)

#### **React 컴포넌트 구조 예시**
```typescript
// 파일: src/features/user/components/UserProfile.tsx

// 1. 모듈 임포트
import React, { useState, useEffect, useMemo, useCallback, useRef } from 'react';
import { useUserQuery } from '../hooks/useUserQuery';
import { Spinner } from '@/components/common/Spinner';
import { ErrorDisplay } from '@/components/common/ErrorDisplay';

// 2. 타입 및 인터페이스 정의
interface UserProfileProps {
  userId: string;
}

// 3. 컴포넌트 함수 정의
export function UserProfile({ userId }: UserProfileProps) {
  // 4. 상태 및 Ref
  const [isEditing, setIsEditing] = useState(false);
  const nameInputRef = useRef<HTMLInputElement>(null);

  // 5. 커스텀 훅 및 데이터 Fetching
  const { data: user, error, isLoading } = useUserQuery(userId);

  // 6. 메모이제이션된 값
  const userStatus = useMemo(() => {
    return user?.isActive ? '활성' : '비활성';
  }, [user?.isActive]);

  // 7. 이벤트 핸들러
  const handleEditToggle = useCallback(() => {
    setIsEditing(prev => !prev);
  }, []);

  // 8. Side Effect 처리
  useEffect(() => {
    if (isEditing && nameInputRef.current) {
      nameInputRef.current.focus();
    }
  }, [isEditing]);

  // 9. 조건부 렌더링 (Early return)
  if (isLoading) return <Spinner />;
  if (error) return <ErrorDisplay error={error} />;
  if (!user) return <div>사용자 정보를 찾을 수 없습니다.</div>;

  // 10. JSX 렌더링
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

#### **Vue 컴포넌트 구조 예시**
```vue
<!-- 파일: src/features/user/components/UserProfile.vue -->
<script setup lang="ts">
// 1. 모듈 임포트
import { ref, computed, watch, nextTick } from 'vue';
import { useUserQuery } from '../composables/useUserQuery';
import Spinner from '@/components/common/Spinner.vue';

// 2. Props 및 Emits 정의
const props = defineProps<{
  userId: string;
}>();

// 3. 컴포저블 함수 사용
const { data: user, error, isLoading } = useUserQuery(() => props.userId);

// 4. 반응형 상태
const isEditing = ref(false);
const nameInput = ref<HTMLInputElement | null>(null);

// 5. 계산된 속성 (Computed)
const userStatus = computed(() => (user.value?.isActive ? '활성' : '비활성'));

// 6. 메서드
const handleEditToggle = () => {
  isEditing.value = !isEditing.value;
};

// 7. Watcher 및 생명주기
watch(isEditing, async (isNowEditing) => {
  if (isNowEditing) {
    await nextTick(); // DOM 업데이트 대기
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

### 🖌️ 4. 스타일링 전략
- **CSS Modules**: 컴포넌트 단위의 스코프를 가진 스타일을 적용할 때 기본으로 사용합니다. 클래스명 충돌을 방지합니다.
- **CSS-in-JS (e.g., Styled Components)**: 동적 스타일링이나 복잡한 테마 시스템이 필요할 경우 제한적으로 사용합니다.
- **Tailwind CSS**: 유틸리티-우선(Utility-first) 접근 방식으로 빠른 프로토타이핑과 일관된 디자인 시스템 구축 시 고려합니다.
- **선택 기준**: 팀의 숙련도, 프로젝트 규모, 동적 스타일링의 필요성을 종합적으로 고려하여 선택합니다.

### ♿ 5. 웹 접근성 (Accessibility, A11y)
- **시맨틱 HTML**: `nav`, `main`, `button` 등 의미에 맞는 태그를 사용합니다. 스타일링만을 위한 `div`와 `span` 사용을 지향합니다.
- **키보드 네비게이션**: 모든 인터랙티브 요소는 키보드(Tab, Enter, Space)만으로 조작 가능해야 합니다. 포커스 순서는 논리적이어야 합니다.
- **ARIA 속성**: 동적인 UI 변화를 스크린 리더가 인지할 수 있도록 `aria-live`, `aria-expanded` 등의 속성을 적절히 사용합니다.
- **라벨링**: 모든 `input` 요소에는 `label` 태그를 연결하거나 `aria-label` 속성을 제공합니다.
- **색상 대비**: WCAG 2.1 AA 레벨(최소 4.5:1)의 명도 대비를 준수하여 텍스트 가독성을 확보합니다.

### 🚫 6. 프론트엔드 금지 패턴
- **과도한 Props Drilling (3단계 이상)**: `Context API`나 상태 관리 라이브러리, 또는 컴포넌트 합성(Composition)으로 해결합니다.
- **`useEffect` 남용**: 서버 상태 동기화는 `TanStack Query`와 같은 비동기 상태 관리 라이브러리를, 파생 상태 계산은 `useMemo`를 우선적으로 고려합니다.
- **배열 인덱스를 `key`로 사용**: 데이터 변경 시 리렌더링 과정에서 예측 불가능한 오류를 유발할 수 있으므로, 고유 ID를 사용합니다.
- **`!important` 남용**: CSS 명시도(Specificity)를 높이는 방식으로 해결하고, `!important`는 최후의 수단으로만 사용합니다.

---

## ⚙️ 백엔드 가이드라인

**이 섹션은 안정적이고 확장 가능한 백엔드 시스템 구축을 위한 가이드라인입니다.**

### 🏗️ 1. 아키텍처 원칙

#### **계층형 아키텍처 (Layered Architecture) 예시 (Java/Spring)**
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

// === Business Layer (Service) ===
public interface UserService {
    UserResponseDto findUserById(Long id);
}

@Service
@RequiredArgsConstructor
public class UserServiceImpl implements UserService {
    private final UserRepository userRepository; // 구체 클래스가 아닌 인터페이스에 의존

    @Override
    @Transactional(readOnly = true)
    public UserResponseDto findUserById(Long id) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("User not found with id: " + id));
        // Entity를 DTO로 변환하여 반환
        return UserResponseDto.from(user);
    }
}

// === Data Access Layer (Repository & Entity) ===
public interface UserRepository extends JpaRepository<User, Long> {
}

@Entity
@Getter
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    private String password; // 해시된 비밀번호
}

// === Data Transfer Object (DTO) ===
@Getter
@AllArgsConstructor
public class UserResponseDto {
    private Long id;
    private String name;
    private String email;
    // 패스워드와 같은 민감 정보는 절대 포함하지 않음

    public static UserResponseDto from(User user) {
        return new UserResponseDto(user.getId(), user.getName(), user.getEmail());
    }
}
```

### 📡 2. API 설계 표준
- **RESTful 원칙**: 리소스 기반 URL(`GET /users/{id}`), HTTP 메서드의 의미 준수, 표준 HTTP 상태 코드 사용을 원칙으로 합니다.
- **표준 응답 포맷**: `data`, `message`, `error` 필드를 포함하는 일관된 JSON 응답 구조를 사용하여 클라이언트의 처리 용이성을 높입니다.
- **페이지네이션(Pagination)**: 대규모 데이터셋을 반환할 경우, Cursor 기반 또는 Offset 기반 페이지네이션을 구현합니다.

### 💡 3. 기술별 구현 가이드 (Java/Spring & Python)**
#### **Java/Spring**
- **의존성 주입**: 생성자 주입(`@RequiredArgsConstructor` 활용)을 사용하여 불변성을 보장하고 순환 참조를 방지합니다.
- **예외 처리**: `@ControllerAdvice`와 `@ExceptionHandler`를 사용해 전역 예외를 처리하고, 서비스 계층에서는 비즈니스 관련 예외를 명시적으로 던집니다.
- **트랜잭션 관리**: CUD(Create, Update, Delete) 작업에는 `@Transactional`을, 조회 작업에는 `@Transactional(readOnly = true)`을 적용하여 성능을 최적화합니다.

#### **Python (FastAPI/Django)**
- **타입 힌트**: 모든 함수 시그니처와 주요 변수에 타입 힌트를 필수로 사용하여 코드 안정성과 가독성을 높입니다. `mypy`를 이용한 정적 분석을 수행합니다.
- **데이터 유효성 검사**: `Pydantic`(FastAPI) 또는 `Serializer`(Django DRF)를 사용하여 API 경계에서 데이터의 유효성을 철저히 검사합니다.
- **ORM 최적화**: N+1 쿼리 문제를 방지하기 위해 `select_related` 및 `prefetch_related`(Django)를 적극적으로 사용합니다.

### 🔐 4. 보안 및 데이터 관리
- **인증/인가**: Stateless한 JWT 기반 인증을 기본으로 하며, 각 API 엔드포인트에 역할 기반 접근 제어(RBAC)를 적용합니다.
- **입력값 검증**: SQL Injection, XSS 등의 공격을 방지하기 위해 모든 외부 입력값을 검증하고, ORM 사용 시 Parameterized Query가 적용되는지 확인합니다.
- **민감 정보 관리**: 사용자 비밀번호는 `bcrypt`와 같은 단방향 해시 함수로 암호화하여 저장하고, API 키 등은 환경 변수나 Secret Manager를 통해 관리합니다.

### 📊 5. 로깅 및 모니터링
- **구조화된 로깅(Structured Logging)**: 검색 및 분석이 용이하도록 로그를 JSON 형식으로 출력합니다.
- **적절한 로그 레벨 사용**: `DEBUG`, `INFO`, `WARN`, `ERROR` 등 상황에 맞는 로그 레벨을 사용하여 로그의 유용성을 높입니다.
- **핵심 지표 모니터링**: Prometheus, Grafana 등을 활용하여 요청 수, 응답 시간, 에러율 등 핵심 시스템 지표를 수집하고 시각화합니다.

### 🚫 6. 백엔드 금지 패턴
- **N+1 쿼리**: 반복문 내에서 DB 쿼리를 실행하지 않고, join fetch나 배치 조회를 사용합니다.
- **Entity 직접 노출**: Controller에서 Entity 객체를 직접 반환하지 않고, 항상 DTO(Data Transfer Object)로 변환하여 반환합니다.
- **동기 방식의 장기 실행 작업**: 5초 이상 소요될 것으로 예상되는 작업은 Message Queue를 활용한 비동기 처리로 전환합니다.
- **과도하게 넓은 트랜잭션 범위**: 트랜잭션은 외부 API 호출이나 파일 I/O 등과 분리하고, DB 접근 로직에만 최소한의 범위로 적용합니다.

---

## 🤝 협업 및 검증 프로세스

**이 섹션은 효율적인 팀 협업과 코드 품질 유지를 위한 절차와 규약을 정의합니다.**

### 🔄 1. 협업 프로세스
#### **작업 절차**
1.  **착수**: Jira 등 티켓 시스템에서 이슈 확인 및 할당 → 브랜치 생성 → 기존 코드 및 영향 범위 분석
2.  **진행**: 가이드라인 준수하여 코딩 → 단위 테스트 작성 → 셀프 리뷰
3.  **완료**: Pull Request(PR) 생성 → 동료 코드 리뷰 → CI/CD 파이프라인 통과 → 브랜치 병합(Merge) → 이슈 종결

#### **소통 규칙**
- **비동기 소통 우선**: 긴급 사안이 아닐 경우, Slack, Jira 댓글 등 비동기 채널을 우선 사용하여 동료의 업무 흐름을 존중합니다.
- **결정 사항 문서화**: 주요 기술적 결정이나 정책 변경은 반드시 회의록이나 위키 등 문서로 기록하여 추적이 가능하도록 합니다.
- **구체적인 에러 보고**: "안 돼요"가 아닌, "A 기능을 실행했을 때 B 라는 에러가 발생했으며, C 조치를 시도해 보았습니다" 와 같이 상황, 결과, 시도한 내용을 구체적으로 공유합니다.

#### **코드 리뷰 가이드라인**
코드 리뷰는 단순히 결함을 찾는 과정이 아니라, 코드 품질을 개선하고 팀 전체의 지식을 공유하며 함께 성장하는 과정입니다.
- **리뷰어(Reviewer)**: 명령이 아닌 제안의 형태로 의견을 제시합니다. 주관적인 선호보다 가이드라인이나 기술적 근거를 바탕으로 설명합니다. (예: "이렇게 바꾸세요" (X) → "이 부분은 SRP 원칙에 따라 별도 함수로 분리하는 것을 고려해볼 수 있을까요?" (O))
- **작성자(Author)**: 리뷰 의견을 방어적으로 수용하기보다, 더 나은 코드를 위한 제안으로 받아들이고 질문을 통해 의도를 파악합니다. (예: "왜 그렇게 생각하시는지 구체적인 이유를 알 수 있을까요?")

#### **Pull Request 템플릿 예시**
```markdown
### 1. 요약 (Summary)
사용자 프로필 이미지 업로드 기능을 추가했습니다.

### 2. 배경 (Context)
- 사용자가 자신의 프로필을 개인화할 수 있도록 이미지 변경 기능을 제공합니다.
- 관련 이슈: #78

### 3. 변경 내용 (Changes)
- `POST /api/users/{id}/avatar` API 엔드포인트 추가
- S3에 이미지를 업로드하는 `S3UploadService` 구현
- 프론트엔드에 이미지 업로드 UI 추가

### 4. 리뷰 요청 사항 (Points for Review)
- `S3UploadService`의 예외 처리 로직이 견고한지 검토 부탁드립니다.
- 파일 사이즈 제한(5MB) 로직의 적절성에 대한 의견 부탁드립니다.

### 5. 잠재적 리스크 (Potential Risks)
- 악의적인 파일(예: 스크립트) 업로드 시도에 대한 방어 로직 보강이 필요할 수 있습니다.
```

### ✅ 2. 머지 전 검증 체크리스트
코드를 병합하기 전, 아래 체크리스트를 통해 작업을 최종적으로 검토합니다.

#### **공통 품질 체크리스트**
```
□ 작업 범위 명확화 원칙을 준수했는가?
□ 핵심 개발 원칙 5가지(DRY, SRP 등)를 위반하지 않았는가?
□ 네이밍 컨벤션을 준수했으며, 매직 넘버/문자열을 사용하지 않았는가?
□ 예외 상황을 고려하여 명확한 오류 처리를 구현했는가?
□ 신규/수정된 로직에 대한 테스트 코드를 작성했는가?
```
#### **프론트엔드 추가 체크리스트**
```
□ 모든 인터랙티브 요소는 키보드로 조작 가능한가? (웹 접근성)
□ 불필요한 리렌더링으로 인한 성능 저하는 없는가? (성능)
□ 모바일 환경에서 UI 레이아웃이 정상적으로 표시되는가? (반응형 디자인)
```
#### **백엔드 추가 체크리스트**
```
□ 모든 외부 입력값에 대한 유효성 검사를 수행했는가? (보안)
□ N+1 쿼리와 같은 비효율적인 데이터베이스 조회가 없는가? (성능)
□ 민감 정보가 로그나 API 응답에 노출되지 않는가? (보안)
```

### 🤖 3. AI 협업 키워드 (AI Collaboration Keywords)
AI 어시스턴트에게 특정 작업을 명확하게 지시하기 위해 사용하는 표준 키워드입니다.

- **공통**: `최적화`, `리팩토링`, `테스트작성`, `문서화`
- **프론트엔드**: `컴포넌트분리`, `접근성개선`, `반응형적용`
- **백엔드**: `API설계`, `쿼리최적화`, `보안강화`, `캐싱전략적용`

**사용 예시**:
- `"기존 UserProfile 컴포넌트의 기능이 과도하게 많습니다. '컴포넌트분리'를 실행하여 관심사에 따라 여러 하위 컴포넌트로 나눠주세요."`
- `"사용자 생성 API의 응답 속도가 느립니다. '쿼리최적화'를 통해 관련 로직을 분석하고 개선안을 제시해주세요."`
