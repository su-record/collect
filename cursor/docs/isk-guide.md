# ISK 모드 가이드

## 개요

ISK(Identified Secure Key)는 외부 시스템에서 인증된 사용자의 신원을 식별하는 보안 토큰입니다.

## 구현 내용

### 1. 미들웨어 처리

`src/middleware.ts`에서 ISK 값을 처리합니다.

```typescript
// ... 기존 코드 ...
const iskValue = request.nextUrl.searchParams.get("isk");

if (iskValue) {
  const decodedIskValue = decodeURIComponent(iskValue);
  headers.set("x-isk", decodedIskValue);
  headers.set(
    "Set-Cookie",
    `isk=${decodedIskValue}; Path=/; HttpOnly; SameSite=None; Secure`,
  );
}
// ... 기존 코드 ...
```

### 2. API 클라이언트 처리

`src/lib/client.ts`에서 ISK 값을 API 요청에 포함시킵니다.

```typescript
// ... 기존 코드 ...
const iskValue = document.cookie
  .split("; ")
  .find((row) => row.startsWith("isk="))
  ?.split("=")[1];

const url = config.url || "";
if (ISK_API_PATHS.includes(url) && iskValue) {
  config.url = `/isk${url}`;
  config.params = { ...config.params, iskValue };
  config.headers.set("x-isk", iskValue);
}
// ... 기존 코드 ...
```

### 3. API URL 패턴

- 일반 API: `/api/example`
- ISK 모드 API: `/isk/api/example?iskValue=%61%62%63%31%32%33`

### 4. 주요 특징

- ISK 값은 URI 인코딩된 형태로 전달되며, 미들웨어에서 자동으로 디코딩
- 보안을 위해 HttpOnly, SameSite=None, Secure 옵션 적용
- ISK 모드가 적용되는 API는 `ISK_API_PATHS`에 정의된 경우에만 변환

## 사용 방법

```
https://example.com?isk=YOUR_ISK_VALUE
```

## 주의사항

1. ISK 값은 민감한 정보이므로 안전하게 관리
2. 프로덕션 환경에서는 반드시 HTTPS 사용
3. ISK 모드가 적용되는 API는 `ISK_API_PATHS`에 정의된 경우에만 변환
4. URL에 ISK 파라미터가 있는 경우는 이미 인증된 사용자로 간주하므로, 로그인 페이지로 리다이렉트하지 않도록 주의

## 관련 파일

- `src/middleware.ts`: ISK 모드 미들웨어 구현
- `src/lib/client.ts`: API 클라이언트 구현
- `src/lib/isk-api.ts`: ISK 모드가 적용되는 API 경로 정의
