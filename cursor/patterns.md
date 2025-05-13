# 🎯 디자인 패턴 예시 (Design Pattern Examples)

## 1. Composite 패턴 (Component Composition)

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

## 5. 과도한 패턴 사용 예시 (Overuse Examples)

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
