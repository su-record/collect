# 🚀 AI 코딩 가이드 v3.0

**AI가 프론트엔드/백엔드 개발 시 즉시 적용할 수 있는 완전한 가이드입니다.**

---

## 📋 목차
1. [핵심 원칙](#핵심-원칙)
2. [공통 가이드](#공통-가이드)
3. [프론트엔드 가이드](#프론트엔드-가이드)
4. [백엔드 가이드](#백엔드-가이드)
5. [검증 체크리스트](#검증-체크리스트)

---

## 🎯 핵심 원칙

### 🥇 제1원칙: 외과수술적 정밀함

> **요청받지 않은 코드는 절대 변경하거나 삭제하지 않습니다.**

```
✅ 명시적으로 요청한 파일과 코드만 수정
✅ 동작하는 기존 코드는 절대 임의로 변경 금지
✅ 기존 코드의 네이밍, 포맷팅, 주석 스타일 유지
```

### ⚡ Quick Start - 즉시 적용 원칙

```
1. 🇰🇷 한국어로 응답
2. 📉 Less code = Less debt (최소한의 코드)
3. 🚫 DRY - Don't Repeat Yourself (중복 금지)
4. 🎯 단일 책임 원칙 (SRP) 준수
5. 🙏 YAGNI - You Aren't Gonna Need It
6. 🔐 API 우선 설계 및 Stateless 지향 (백엔드)
```

### 📦 체크 포인트

```
# 새 패키지/의존성 추가 전
□ 기존 기능으로 해결 가능한가?
□ 정말 필요한가? (YAGNI)
□ 번들 사이즈 또는 애플리케이션 용량 영향은?
□ 보안 취약점은 없는가?

# 파일 생성 시
□ 사용될 위치 확인 및 의존성 즉시 연결
□ 데이터베이스 스키마 또는 API 계약 영향 분석
□ 순환 참조 체크
□ 네이밍 컨벤션 준수
```

---

## 🌐 공통 가이드

### 🎨 코드 품질의 기준

- **가독성**: 코드는 인간을 위한 것
- **예측 가능성**: 놀라움 없는 코드
- **유지보수성**: 미래의 나를 위한 배려
- **테스트 가능성**: 검증 가능한 구조

### 🏗️ 아키텍처 원칙

```
✅ 적재적소 패턴 적용
✅ 과도한 추상화 경계 (3단계 이상 wrapper 금지)
✅ 순환 참조 방지 (모듈 A → 모듈 B → 모듈 A ❌)
✅ 레이어 분리 (Presentation → Business → Data)
```

### 📖 네이밍 규칙

**공통**
```
상수: UPPER_SNAKE_CASE (MAX_RETRY_COUNT, API_TIMEOUT)
불리언: is/has/can 접두사 (isLoading, hasError, canEdit)
환경변수: UPPER_SNAKE_CASE (DATABASE_URL, JWT_SECRET)
```

**TypeScript/JavaScript**
```
변수: camelCase 명사 (userList, userData)
함수: camelCase 동사+명사 (fetchData, findUserById)
클래스/컴포넌트: PascalCase (UserProfile, UserService)
이벤트: handle 접두사 (handleClick) / on 접두사 (onUserCreated)
React Hook: use 접두사 (useUserData, useAuth)
```

**백엔드 추가**
```
DTO: Dto 접미사 (CreateUserDto, UpdateUserDto)
Entity: Entity 접미사 (UserEntity, OrderEntity)
Module: Module 접미사 (AuthModule, UserModule)
```

### 🏗️ 코드 구조 규칙

**함수 분리 기준**
- 함수가 **20줄** 초과 → 단일 책임에 따라 분리
- 중첩 깊이 **3단계** 초과 → 로직 재구성
- **3개 이상의 책임** → 별도 함수/모듈로 분리

**자동 변환 규칙**
```
# 매직 넘버/문자열 → 상수
❌ setTimeout(() => {}, 3000)
✅ const ANIMATION_DELAY_MS = 3000

# 복잡한 조건 → 명확한 변수
❌ if (user && user.age >= 18 && user.email && !user.suspended)
✅ const isEligibleUser = /* 조건 */

# 중첩 삼항 연산자 → Early Return
❌ return loading ? <Spinner /> : error ? <Error /> : <Data />
✅ if (loading) return <Spinner />
   if (error) return <Error />
   return <Data />
```

### 🚫 공통 금지사항

**보안**
```
❌ 하드코딩된 시크릿 → ✅ 환경변수
❌ 평문 비밀번호 → ✅ bcrypt/argon2로 해시
❌ 신뢰하지 않는 입력 → ✅ 검증 & 새니타이즈
❌ 민감 정보 로그 노출 → ✅ 마스킹 또는 제외
```

**품질**
```
❌ any 타입 → ✅ unknown + 타입 가드
❌ @ts-ignore → ✅ 타입 문제 해결
❌ var → ✅ const/let
❌ == → ✅ ===
❌ eval() → ✅ Function constructor
❌ 빈 catch 블록 → ✅ 적절한 에러 처리
```

### 📋 작업 프로세스

```
1. 작업 전
   [x] 최우선 원칙 준수: 요청 범위만 수정
   [ ] 기존 코드 스타일 파악
   [ ] 영향 범위 분석

2. 작업 중
   [ ] 네이밍 규칙 준수
   [ ] 구조 패턴 적용
   [ ] 금지 패턴 회피

3. 작업 후
   [ ] 보안 취약점 체크
   [ ] 성능 이슈 확인
   [ ] 테스트 가능 구조
```

---

## 💻 프론트엔드 가이드

### 🎨 TypeScript 규칙

**타입 정의**
```typescript
// ✅ 명시적 타입
interface UserData {
  id: string;
  name: string;
  role: 'admin' | 'user';
}

// ✅ 타입 가드
function isUserData(data: unknown): data is UserData {
  return typeof data === 'object' && 
         data !== null &&
         'id' in data && 
         'name' in data;
}
```

### ⚡ Next.js 패턴

**파일 구조**
```
app/
├── (routes)/          # 라우트 그룹
├── _components/       # 내부 컴포넌트  
├── layout.tsx        # 레이아웃
└── page.tsx          # 페이지

components/           # 공통 컴포넌트
lib/                 # 유틸리티
hooks/               # 커스텀 훅
types/               # 타입 정의
```

**컴포넌트 패턴**
```typescript
// 서버 컴포넌트 (기본)
export default async function Page() {
  const data = await fetchData();
  return <div>{/* UI */}</div>;
}

// 클라이언트 컴포넌트 (필요시만)
'use client';
export default function Interactive() {
  const [state, setState] = useState();
  return <div>{/* UI */}</div>;
}
```

### 🎯 Nuxt.js 패턴

**Composition API**
```vue
<script setup lang="ts">
// 데이터 페칭
const { data } = await useFetch('/api/data')

// 반응형 상태
const count = ref(0)
const doubled = computed(() => count.value * 2)

// 이벤트 핸들러
const handleClick = () => {
  count.value++
}
</script>
```

### 🧠 프론트엔드 상태 관리

```
단순 UI 상태 → useState/ref
복잡한 로컬 상태 → useReducer
전역 UI 상태 → Context API/Pinia
전역 앱 상태 → Zustand/Redux/Pinia
서버 상태 → TanStack Query/useFetch
폼 상태 → React Hook Form/VeeValidate
```

### 📋 컴포넌트 구조 (순서 엄수)

```
1. Imports
2. Type definitions
3. Props/Emits definition
4. State & Refs
5. Custom Hooks
6. Event Handlers  
7. Effects/Lifecycle
8. Early returns
9. Main return JSX/Template
```

### ♿ 접근성 체크리스트

```
□ 시맨틱 HTML 사용
□ ARIA 레이블 제공
□ 키보드 네비게이션 지원
□ 포커스 관리
□ 색상 대비 준수
```

### 🚀 성능 최적화

```
□ 큰 리스트 → 가상화 (react-window)
□ 무거운 연산 → useMemo/computed
□ 콜백 props → useCallback
□ 큰 번들 → 동적 import
□ 이미지 → next/image, NuxtImage
```

### 🚫 프론트엔드 금지사항

```
React/Vue
❌ dangerouslySetInnerHTML → ✅ 안전한 렌더링
❌ Props Drilling (3+) → ✅ Context/Composition
❌ 직접 DOM 조작 → ✅ Ref 사용

CSS
❌ !important 남용 → ✅ 구체적 선택자
❌ 인라인 스타일 남용 → ✅ CSS 클래스/모듈
```

---

## 🖥️ 백엔드 가이드

### 🔐 보안과 안정성 원칙

- **API 입력값 검증**: 모든 외부 입력은 검증
- **민감 정보 보호**: 암호화 저장, 로그 제외
- **에러 처리**: 명확한 에러 코드와 메시지
- **데이터 무결성**: 트랜잭션으로 일관성 유지

### ☕ Java/Spring Boot 패턴

**레이어드 아키텍처**
```java
// Controller - 요청/응답만
@RestController
@RequestMapping("/api/users")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;
    
    @GetMapping("/{id}")
    public ResponseEntity<UserDto> getUser(@PathVariable Long id) {
        return ResponseEntity.ok(userService.findById(id));
    }
}

// Service - 비즈니스 로직
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;
    
    public UserDto findById(Long id) {
        return userRepository.findById(id)
            .map(UserMapper::toDto)
            .orElseThrow(() -> new BusinessException(ErrorCode.USER_NOT_FOUND));
    }
}

// Repository - 데이터 접근
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

### 🐍 Python 패턴

**FastAPI**
```python
# Router
@router.get("/users/{user_id}", response_model=UserResponse)
async def get_user(
    user_id: int,
    service: UserService = Depends()
) -> UserResponse:
    return await service.get_user(user_id)

# Service
class UserService:
    def __init__(self, repo: UserRepository = Depends()):
        self.repo = repo
    
    async def get_user(self, user_id: int) -> User:
        user = await self.repo.find_by_id(user_id)
        if not user:
            raise HTTPException(404, "User not found")
        return user

# Repository
class UserRepository:
    async def find_by_id(self, user_id: int) -> User | None:
        # DB 쿼리
```

**Django/DRF**
```python
# ViewSet
class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    permission_classes = [IsAuthenticated]
    
    def get_queryset(self):
        # N+1 방지
        return super().get_queryset().select_related('profile')

# Serializer
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']
        read_only_fields = ['id']
```

### 🏗️ 백엔드 구조 규칙

**서비스/컨트롤러 구조**
```
1. 의존성 주입 (DI)
2. Public API 메서드
3. 비즈니스 로직 헬퍼 (Private)
4. 데이터 유효성 검사
```

### 📡 API 설계 표준

**RESTful 라우트**
```
GET    /api/resources      # 목록
GET    /api/resources/{id} # 단건
POST   /api/resources      # 생성
PUT    /api/resources/{id} # 전체 수정
PATCH  /api/resources/{id} # 부분 수정
DELETE /api/resources/{id} # 삭제
```

**표준 응답 형식**
```json
// 성공
{
  "data": { /* 페이로드 */ },
  "message": "요청이 성공적으로 처리되었습니다",
  "error": null
}

// 에러
{
  "data": null,
  "message": "사용자를 찾을 수 없습니다",
  "error": {
    "code": "USER_NOT_FOUND",
    "details": { "userId": 123 }
  }
}

// 페이지네이션
{
  "data": [...],
  "pagination": {
    "page": 1,
    "size": 20,
    "total": 100,
    "totalPages": 5
  }
}
```

**HTTP 상태 코드**
```
200 OK               # 성공
201 Created          # 생성 성공
204 No Content       # 삭제 성공
400 Bad Request      # 잘못된 요청
401 Unauthorized     # 인증 실패
403 Forbidden        # 권한 없음
404 Not Found        # 리소스 없음
409 Conflict         # 충돌 (중복 등)
422 Unprocessable    # 검증 실패
500 Internal Error   # 서버 오류
```

### 🧠 백엔드 데이터 관리

```
휘발성 캐시 → Redis/Memcached
관계형 데이터 → PostgreSQL/MySQL
비정형 데이터 → MongoDB/DynamoDB
전문 검색 → Elasticsearch
메시지 큐 → RabbitMQ/Kafka
파일 저장 → S3/MinIO
```

### 🚫 백엔드 금지사항

**Database**
```
❌ N+1 쿼리 → ✅ Join/Eager Loading/DataLoader
❌ SELECT * → ✅ 필요한 컬럼만 명시
❌ 인덱스 없는 대량 쿼리 → ✅ 인덱스 설계
❌ 트랜잭션 부재 → ✅ 중요 CUD는 트랜잭션
```

**API**
```
❌ 비밀번호 응답 포함 → ✅ DTO로 필터링
❌ 동기 대량 처리 → ✅ 비동기/큐 처리
❌ 무제한 요청 → ✅ Rate Limiting
❌ SQL Injection → ✅ Parameterized Query
```

**General**
```
❌ System.out.println → ✅ Logger 사용
❌ 로그에 민감 정보 → ✅ 마스킹/제외
❌ 동기 I/O 블로킹 → ✅ 비동기 I/O (Node.js)
```

---

## ✅ 검증 체크리스트

### 🔍 코드 품질 체크

```typescript
const codeQualityCheck = {
  // 핵심 원칙
  obeysTheGoldenRule: true,      // ✅ 요청 범위만 수정
  preservesWorkingCode: true,    // ✅ 기존 코드 보존
  
  // 공통 품질
  singleResponsibility: true,    // ✅ 단일 책임
  consistentNaming: true,        // ✅ 일관된 네이밍
  noMagicNumbers: true,          // ✅ 매직 넘버 없음
  
  // 타입 안정성
  noAnyType: true,              // ✅ any 타입 없음
  strictNullCheck: true,        // ✅ null/undefined 체크
  
  // 에러 처리
  robustErrorHandling: true,    // ✅ 적절한 에러 처리
  properHttpStatus: true,       // ✅ 올바른 HTTP 상태
  
  // 프론트엔드
  semanticHTML: true,           // ✅ 시맨틱 태그
  keyboardAccessible: true,     // ✅ 키보드 접근
  noUnnecessaryRenders: true,   // ✅ 불필요한 리렌더 없음
  
  // 백엔드
  secureAgainstVulns: true,     // ✅ OWASP 취약점 방어
  hasDataValidation: true,      // ✅ 입력 검증
  optimizedQueries: true,       // ✅ 쿼리 최적화
  hasStructuredLogging: true,   // ✅ 구조화된 로깅
  gracefulShutdown: true,       // ✅ 프로세스 종료 처리
};
```

### 🏗️ 프로젝트 체크

```typescript
const projectCheck = {
  // 의존성
  noUnusedDeps: true,           // ✅ 미사용 패키지 없음
  securityAudited: true,        // ✅ 보안 취약점 체크
  
  // 구조
  consistentStructure: true,    // ✅ 일관된 폴더 구조
  noCircularDeps: true,         // ✅ 순환 참조 없음
  
  // 프론트엔드
  treeShaking: true,            // ✅ 트리 쉐이킹
  codeSplitting: true,          // ✅ 코드 스플리팅
  
  // 백엔드/인프라
  hasCI_CD: true,               // ✅ CI/CD 파이프라인
  hasContainerization: true,    // ✅ Dockerfile
  envVarManagement: true,       // ✅ 환경변수 분리
};
```

---

## 💬 커뮤니케이션 가이드

### 📝 코드 제공 형식

```markdown
### 🎯 작업 범위
"요청하신 UserProfile 컴포넌트와 User API의 응답 형식을 수정했습니다."

### 📋 변경사항 요약
- **프론트엔드**: 주문 상태 업데이트에 낙관적 업데이트 적용
- **백엔드**: 사용자 조회 시 불필요한 password 필드 제외

### 💻 코드
```[언어]
// 파일: src/components/UserProfile.tsx
[전체 코드]
```

### ⚠️ 주의사항
- 에러 발생 시 자동 롤백됩니다
- 네트워크 재시도는 최대 3회 수행됩니다
- Redis 캐시 TTL은 1시간으로 설정되어 있습니다
```

### 🔍 리뷰 응답 형식

```markdown
### ✅ 개선점
1. **[성능]** user.posts 조회 시 N+1 쿼리 발생 가능
2. **[안정성]** 이메일 전송 실패 시 에러 처리 누락

### 💡 권장사항
- findAll 쿼리에서 posts를 JOIN으로 함께 조회
- 이메일 전송을 try-catch로 감싸고 재시도 큐 추가
```

---

## 🎮 특수 명령어

**공통**
- `최적화`: 성능 개선
- `타입강화`: any 제거, 타입 안정성
- `클린업`: 불필요 코드 정리 (요청 시만)
- `테스트`: 단위/통합 테스트 추가

**프론트엔드**
- `접근성`: ARIA, 키보드 지원
- `반응형`: 모바일/태블릿 대응
- `SEO`: 메타태그, 구조화 데이터

**백엔드**
- `보안강화`: 취약점 패치, 인증/인가
- `캐싱`: Redis/메모리 캐시 추가
- `확장성`: 수평 확장 준비
- `모니터링`: 로깅, 메트릭 추가

---

**Remember**: 
> "코드는 한 번 작성되지만, 수십 번 읽힌다. 미래의 당신과 동료를 위해 명확하게 작성하라." 🚀
