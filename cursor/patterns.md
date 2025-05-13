# 🎯 디자인 패턴 예시 (Design Pattern Examples)

## 1. Composite 패턴 (Component Composition)

### 설명

- 컴포넌트를 트리 구조로 구성하여 부분-전체 계층을 표현
- 개별 객체와 복합 객체를 동일하게 다룰 수 있음
- 컴포넌트의 재사용성과 유연성 향상

### 장점

- 컴포넌트의 일관된 인터페이스 제공
- 새로운 컴포넌트 추가가 용이
- 복잡한 UI 구조를 단순화

### 단점

- 너무 깊은 계층 구조는 성능 저하 가능성
- 모든 컴포넌트에 동일한 인터페이스 강제

```tsx
// 좋은 예시: 컴포넌트 합성
const Card = ({ children }) => <div className="card">{children}</div>;
const CardHeader = ({ children }) => (
  <div className="card-header">{children}</div>
);
const CardBody = ({ children }) => <div className="card-body">{children}</div>;

// 사용 예시
<Card>
  <CardHeader>제목</CardHeader>
  <CardBody>내용</CardBody>
</Card>;
```

## 2. Observer 패턴 (State Management)

### 설명

- 상태 변경을 구독자들에게 알리는 패턴
- 컴포넌트 간의 느슨한 결합 유지
- 상태 관리의 중앙화

### 장점

- 상태 변경의 일관성 유지
- 컴포넌트 간 의존성 감소
- 상태 관리 로직의 재사용

### 단점

- 과도한 리렌더링 가능성
- 메모리 누수 주의 필요
- 디버깅이 복잡할 수 있음

```tsx
// 좋은 예시: 상태 관리
const useCounter = () => {
  const [count, setCount] = useState(0);
  const increment = () => setCount((prev) => prev + 1);
  const decrement = () => setCount((prev) => prev - 1);
  return { count, increment, decrement };
};

// 사용 예시
const Counter = () => {
  const { count, increment, decrement } = useCounter();
  return (
    <div>
      <button onClick={decrement}>-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  );
};
```

## 3. Factory 패턴 (Component Creation)

### 설명

- 컴포넌트 생성 로직을 캡슐화
- 조건에 따른 컴포넌트 생성 처리
- 컴포넌트 생성의 유연성 제공

### 장점

- 컴포넌트 생성 로직의 중앙화
- 새로운 컴포넌트 타입 추가가 용이
- 조건부 렌더링 로직 단순화

### 단점

- 팩토리 로직이 복잡해질 수 있음
- 과도한 추상화 가능성
- 테스트가 복잡해질 수 있음

```tsx
// 좋은 예시: 컴포넌트 팩토리
const ButtonFactory = {
  primary: (props) => <button className="btn-primary" {...props} />,
  secondary: (props) => <button className="btn-secondary" {...props} />,
  danger: (props) => <button className="btn-danger" {...props} />,
};

// 사용 예시
const Button = ({ type = "primary", ...props }) => {
  const ButtonComponent = ButtonFactory[type];
  return <ButtonComponent {...props} />;
};
```

## 4. Decorator 패턴 (Component Extension)

### 설명

- 기존 컴포넌트에 새로운 기능을 동적으로 추가
- 컴포넌트의 기능 확장을 유연하게 처리
- 코드 재사용성 향상

### 장점

- 기존 코드 수정 없이 기능 확장
- 여러 데코레이터 조합 가능
- 단일 책임 원칙 준수

### 단점

- 데코레이터 체인이 복잡해질 수 있음
- 디버깅이 어려울 수 있음
- 성능 오버헤드 가능성

```tsx
// 좋은 예시: 컴포넌트 데코레이터
const withLoading = (WrappedComponent) => {
  return ({ isLoading, ...props }) => {
    if (isLoading) return <div>로딩 중...</div>;
    return <WrappedComponent {...props} />;
  };
};

// 사용 예시
const UserProfile = ({ user }) => <div>{user.name}</div>;
const UserProfileWithLoading = withLoading(UserProfile);
```

## 5. Strategy 패턴 (Behavioral Pattern)

### 설명

- 알고리즘을 캡슐화하여 런타임에 교체 가능
- 조건문을 객체로 대체
- 동일한 인터페이스로 다양한 전략 구현

### 장점

- 알고리즘의 독립적인 변경 가능
- 새로운 전략 추가가 용이
- 조건문 감소로 코드 가독성 향상

### 단점

- 전략 객체가 많아질 수 있음
- 클라이언트가 모든 전략을 알아야 함
- 객체 생성 오버헤드

```tsx
// 좋은 예시: 정렬 전략
const SortStrategy = {
  ascending: (a, b) => a - b,
  descending: (a, b) => b - a,
  alphabetical: (a, b) => a.localeCompare(b),
};

// 사용 예시
const SortedList = ({ items, strategy = "ascending" }) => {
  const sortedItems = [...items].sort(SortStrategy[strategy]);
  return (
    <ul>
      {sortedItems.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
};
```

## 6. Adapter 패턴 (Structural Pattern)

### 설명

- 호환되지 않는 인터페이스를 함께 작동하도록 변환
- 기존 코드의 재사용성 향상
- 외부 라이브러리 통합 용이

### 장점

- 기존 코드 수정 없이 통합 가능
- 단일 책임 원칙 준수
- 코드 재사용성 향상

### 단점

- 추가적인 추상화 레이어
- 성능 오버헤드 가능성
- 코드 복잡도 증가

```tsx
// 좋은 예시: 외부 라이브러리 어댑터
const ExternalChartAdapter = {
  adapt: (data) => ({
    labels: data.map((item) => item.name),
    datasets: [
      {
        data: data.map((item) => item.value),
      },
    ],
  }),
};

// 사용 예시
const Chart = ({ data }) => {
  const chartData = ExternalChartAdapter.adapt(data);
  return <ExternalChartLibrary data={chartData} />;
};
```

## 7. 과도한 패턴 사용 예시 (Overuse Examples)

### 설명

- 디자인 패턴은 도구일 뿐, 모든 상황에 적용할 필요는 없음
- 단순한 문제는 단순하게 해결
- 패턴 적용 시 코드 복잡도 고려

```tsx
// 나쁜 예시: 과도한 패턴 사용
const ButtonFactory = {
  create: (type) => {
    const ButtonComponent = ({ children, ...props }) => {
      const style = {
        primary: "btn-primary",
        secondary: "btn-secondary",
      }[type];
      return (
        <button className={style} {...props}>
          {children}
        </button>
      );
    };
    return ButtonComponent;
  },
};

// 좋은 예시: 단순한 구현
const Button = ({ type = "primary", children, ...props }) => {
  const style = type === "primary" ? "btn-primary" : "btn-secondary";
  return (
    <button className={style} {...props}>
      {children}
    </button>
  );
};
```

## 8. 문제 해결 예시 (Problem Solving Examples)

### 1. 상태 관리 문제

```tsx
// 문제: 여러 컴포넌트에서 동일한 상태 공유 필요
// 해결: Context API와 Custom Hook 활용
const UserContext = createContext();

const UserProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);

  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
};

const useUser = () => {
  const context = useContext(UserContext);
  if (!context) throw new Error("useUser must be used within UserProvider");
  return context;
};
```

### 2. 성능 최적화 문제

```tsx
// 문제: 불필요한 리렌더링 발생
// 해결: React.memo와 useMemo 활용
const ExpensiveComponent = React.memo(({ data }) => {
  const processedData = useMemo(() => {
    return data.map((item) => ({
      ...item,
      processed: complexCalculation(item),
    }));
  }, [data]);

  return <div>{/* 렌더링 로직 */}</div>;
});
```

### 3. 에러 처리 문제

```tsx
// 문제: 비동기 작업의 에러 처리
// 해결: Error Boundary와 Custom Hook 활용
const useAsync = (asyncFunction) => {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);

  const execute = async (...args) => {
    try {
      setLoading(true);
      const result = await asyncFunction(...args);
      setData(result);
    } catch (err) {
      setError(err);
    } finally {
      setLoading(false);
    }
  };

  return { data, error, loading, execute };
};
```
