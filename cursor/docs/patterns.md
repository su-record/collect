# 🎯 디자인 패턴 가이드 (Design Pattern Guide)

## 📋 빠른 참조 (Quick Reference)

### 우선순위별 패턴 (Priority-based Patterns)

**🔥 높은 우선순위 (High Priority)**

- **Composite 패턴** - 컴포넌트 합성 (Component Composition)
- **Observer 패턴** - 상태 관리 (State Management)

**⚡ 중간 우선순위 (Medium Priority)**

- **Strategy 패턴** - 조건부 로직 처리 (Conditional Logic)
- **Decorator 패턴** - 기능 확장 (Feature Extension)

**💡 낮은 우선순위 (Low Priority)**

- **Factory 패턴** - 복잡한 컴포넌트 생성 (Complex Component Creation)
- **Adapter 패턴** - 외부 라이브러리 통합 (External Library Integration)

### 사용 가이드라인 (Usage Guidelines)

- **단순한 경우 (Simple Cases)**: 패턴 사용 지양, 직접 구현
- **복잡한 경우 (Complex Cases)**: 적절한 패턴 선택 후 적용
- **성능 고려 (Performance Consideration)**: 패턴 적용 시 성능 영향 검토
- **테스트 용이성 (Testability)**: 패턴이 테스트를 복잡하게 만들지 않는지 확인

---

## 1. Composite 패턴 (Component Composition) 🔥

### 설명 (Description)

- **컴포넌트를 트리 구조로 구성 (Tree Structure Composition)**하여 부분-전체 계층을 표현
- **개별 객체와 복합 객체를 동일하게 다룰 수 있음 (Uniform Treatment)**
- **컴포넌트의 재사용성과 유연성 향상 (Enhanced Reusability and Flexibility)**

### 장점 (Advantages)

- **컴포넌트의 일관된 인터페이스 제공 (Consistent Interface)**
- **새로운 컴포넌트 추가가 용이 (Easy Component Addition)**
- **복잡한 UI 구조를 단순화 (Simplified Complex UI Structure)**

### 단점 (Disadvantages)

- **너무 깊은 계층 구조는 성능 저하 가능성 (Performance Issues with Deep Hierarchy)**
- **모든 컴포넌트에 동일한 인터페이스 강제 (Forced Uniform Interface)**

### 언제 사용하지 말아야 하는가 (When NOT to Use)

- **단순한 UI 구조 (Simple UI Structure)**인 경우
- **성능이 중요한 리스트 렌더링 (Performance-Critical List Rendering)**
- **컴포넌트 간 강한 결합이 필요한 경우 (Strong Coupling Required)**

```tsx
// 좋은 예시: 컴포넌트 합성
interface CardProps {
  children: React.ReactNode;
}

const Card: React.FC<CardProps> = ({ children }) => (
  <div className="card">{children}</div>
);

const CardHeader: React.FC<CardProps> = ({ children }) => (
  <div className="card-header">{children}</div>
);

const CardBody: React.FC<CardProps> = ({ children }) => (
  <div className="card-body">{children}</div>
);

// 사용 예시
<Card>
  <CardHeader>제목</CardHeader>
  <CardBody>내용</CardBody>
</Card>;
```

## 2. Observer 패턴 (State Management) 🔥

### 설명 (Description)

- **상태 변경을 구독자들에게 알리는 패턴 (State Change Notification Pattern)**
- **컴포넌트 간의 느슨한 결합 유지 (Loose Coupling Maintenance)**
- **상태 관리의 중앙화 (Centralized State Management)**

### 장점 (Advantages)

- **상태 변경의 일관성 유지 (Consistent State Changes)**
- **컴포넌트 간 의존성 감소 (Reduced Component Dependencies)**
- **상태 관리 로직의 재사용 (Reusable State Management Logic)**

### 단점 (Disadvantages)

- **과도한 리렌더링 가능성 (Excessive Re-rendering Risk)**
- **메모리 누수 주의 필요 (Memory Leak Concerns)**
- **디버깅이 복잡할 수 있음 (Complex Debugging)**

### 언제 사용하지 말아야 하는가 (When NOT to Use)

- **단순한 로컬 상태 (Simple Local State)**만 필요한 경우
- **성능이 중요한 실시간 업데이트 (Performance-Critical Real-time Updates)**
- **상태 변경이 거의 없는 경우 (Rare State Changes)**

```tsx
// 좋은 예시: 상태 관리
interface CounterState {
  count: number;
  increment: () => void;
  decrement: () => void;
}

const useCounter = (): CounterState => {
  const [count, setCount] = useState<number>(0);
  const increment = (): void => setCount((prev) => prev + 1);
  const decrement = (): void => setCount((prev) => prev - 1);
  return { count, increment, decrement };
};

// 사용 예시
const Counter: React.FC = () => {
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

## 3. Strategy 패턴 (Behavioral Pattern) ⚡

### 설명 (Description)

- **알고리즘을 캡슐화하여 런타임에 교체 가능 (Runtime Algorithm Replacement)**
- **조건문을 객체로 대체 (Replace Conditionals with Objects)**
- **동일한 인터페이스로 다양한 전략 구현 (Multiple Strategies with Same Interface)**

### 장점 (Advantages)

- **알고리즘의 독립적인 변경 가능 (Independent Algorithm Changes)**
- **새로운 전략 추가가 용이 (Easy Strategy Addition)**
- **조건문 감소로 코드 가독성 향상 (Improved Readability by Reducing Conditionals)**

### 단점 (Disadvantages)

- **전략 객체가 많아질 수 있음 (Many Strategy Objects)**
- **클라이언트가 모든 전략을 알아야 함 (Client Must Know All Strategies)**
- **객체 생성 오버헤드 (Object Creation Overhead)**

### 언제 사용하지 말아야 하는가 (When NOT to Use)

- **전략이 2-3개 이하 (Few Strategies)**인 단순한 경우
- **전략이 자주 변경되지 않는 경우 (Infrequent Strategy Changes)**
- **성능이 매우 중요한 경우 (Performance-Critical Cases)**

```tsx
// 좋은 예시: 정렬 전략
type SortFunction<T> = (a: T, b: T) => number;
type StrategyType = "ascending" | "descending" | "alphabetical";

const SortStrategy: Record<StrategyType, SortFunction<any>> = {
  ascending: (a: number, b: number) => a - b,
  descending: (a: number, b: number) => b - a,
  alphabetical: (a: string, b: string) => a.localeCompare(b),
};

// 사용 예시
interface SortedListProps {
  items: (string | number)[];
  strategy?: StrategyType;
}

const SortedList: React.FC<SortedListProps> = ({
  items,
  strategy = "ascending",
}) => {
  const sortedItems = [...items].sort(SortStrategy[strategy]);
  return (
    <ul>
      {sortedItems.map((item, index) => (
        <li key={index}>{item}</li>
      ))}
    </ul>
  );
};
```

## 4. Decorator 패턴 (Component Extension) ⚡

### 설명 (Description)

- **기존 컴포넌트에 새로운 기능을 동적으로 추가 (Dynamic Feature Addition)**
- **컴포넌트의 기능 확장을 유연하게 처리 (Flexible Feature Extension)**
- **코드 재사용성 향상 (Enhanced Code Reusability)**

### 장점 (Advantages)

- **기존 코드 수정 없이 기능 확장 (Feature Extension without Code Modification)**
- **여러 데코레이터 조합 가능 (Multiple Decorator Combinations)**
- **단일 책임 원칙 준수 (Single Responsibility Principle Compliance)**

### 단점 (Disadvantages)

- **데코레이터 체인이 복잡해질 수 있음 (Complex Decorator Chains)**
- **디버깅이 어려울 수 있음 (Difficult Debugging)**
- **성능 오버헤드 가능성 (Performance Overhead Risk)**

### 언제 사용하지 말아야 하는가 (When NOT to Use)

- **단순한 조건부 렌더링 (Simple Conditional Rendering)**
- **기능이 고정적인 컴포넌트 (Fixed-Feature Components)**
- **성능이 중요한 리스트 아이템 (Performance-Critical List Items)**

```tsx
// 좋은 예시: 컴포넌트 데코레이터
interface WithLoadingProps {
  isLoading?: boolean;
}

type ComponentWithLoading<P = {}> = React.FC<P & WithLoadingProps>;

const withLoading = <P extends object>(
  WrappedComponent: React.ComponentType<P>,
): ComponentWithLoading<P> => {
  return ({ isLoading, ...props }) => {
    if (isLoading) return <div>로딩 중...</div>;
    return <WrappedComponent {...(props as P)} />;
  };
};

// 사용 예시
interface UserProfileProps {
  user: {
    name: string;
  };
}

const UserProfile: React.FC<UserProfileProps> = ({ user }) => (
  <div>{user.name}</div>
);

const UserProfileWithLoading = withLoading(UserProfile);
```

## 5. Factory 패턴 (Component Creation) 💡

### 설명 (Description)

- **컴포넌트 생성 로직을 캡슐화 (Encapsulated Component Creation Logic)**
- **조건에 따른 컴포넌트 생성 처리 (Conditional Component Creation)**
- **컴포넌트 생성의 유연성 제공 (Flexible Component Creation)**

### 장점 (Advantages)

- **컴포넌트 생성 로직의 중앙화 (Centralized Creation Logic)**
- **새로운 컴포넌트 타입 추가가 용이 (Easy Component Type Addition)**
- **조건부 렌더링 로직 단순화 (Simplified Conditional Rendering Logic)**

### 단점 (Disadvantages)

- **팩토리 로직이 복잡해질 수 있음 (Complex Factory Logic)**
- **과도한 추상화 가능성 (Over-abstraction Risk)**
- **테스트가 복잡해질 수 있음 (Complex Testing)**

### 언제 사용하지 말아야 하는가 (When NOT to Use)

- **컴포넌트 타입이 2-3개 이하 (Few Component Types)**
- **생성 로직이 단순한 경우 (Simple Creation Logic)**
- **타입이 자주 변경되지 않는 경우 (Infrequent Type Changes)**

```tsx
// 좋은 예시: 컴포넌트 팩토리
type ButtonType = "primary" | "secondary" | "danger";

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  children: React.ReactNode;
}

const ButtonFactory: Record<ButtonType, React.FC<ButtonProps>> = {
  primary: (props) => <button className="btn-primary" {...props} />,
  secondary: (props) => <button className="btn-secondary" {...props} />,
  danger: (props) => <button className="btn-danger" {...props} />,
};

// 사용 예시
interface CustomButtonProps extends ButtonProps {
  type?: ButtonType;
}

const Button: React.FC<CustomButtonProps> = ({
  type = "primary",
  ...props
}) => {
  const ButtonComponent = ButtonFactory[type];
  return <ButtonComponent {...props} />;
};
```

## 6. Adapter 패턴 (Structural Pattern) 💡

### 설명 (Description)

- **호환되지 않는 인터페이스를 함께 작동하도록 변환 (Interface Compatibility Conversion)**
- **기존 코드의 재사용성 향상 (Enhanced Code Reusability)**
- **외부 라이브러리 통합 용이 (Easy External Library Integration)**

### 장점 (Advantages)

- **기존 코드 수정 없이 통합 가능 (Integration without Code Modification)**
- **단일 책임 원칙 준수 (Single Responsibility Principle Compliance)**
- **코드 재사용성 향상 (Enhanced Code Reusability)**

### 단점 (Disadvantages)

- **추가적인 추상화 레이어 (Additional Abstraction Layer)**
- **성능 오버헤드 가능성 (Performance Overhead Risk)**
- **코드 복잡도 증가 (Increased Code Complexity)**

### 언제 사용하지 말아야 하는가 (When NOT to Use)

- **인터페이스가 이미 호환되는 경우 (Already Compatible Interfaces)**
- **단순한 데이터 변환 (Simple Data Transformation)**
- **성능이 매우 중요한 경우 (Performance-Critical Cases)**

```tsx
// 좋은 예시: 외부 라이브러리 어댑터
interface DataItem {
  name: string;
  value: number;
}

interface ChartData {
  labels: string[];
  datasets: Array<{
    data: number[];
  }>;
}

interface ExternalChartLibraryProps {
  data: ChartData;
}

const ExternalChartAdapter = {
  adapt: (data: DataItem[]): ChartData => ({
    labels: data.map((item) => item.name),
    datasets: [
      {
        data: data.map((item) => item.value),
      },
    ],
  }),
};

// 사용 예시
interface ChartProps {
  data: DataItem[];
}

// 외부 라이브러리 컴포넌트 (예시)
declare const ExternalChartLibrary: React.FC<ExternalChartLibraryProps>;

const Chart: React.FC<ChartProps> = ({ data }) => {
  const chartData = ExternalChartAdapter.adapt(data);
  return <ExternalChartLibrary data={chartData} />;
};
```

## 7. 과도한 패턴 사용 예시 (Overuse Examples)

### 설명 (Description)

- **디자인 패턴은 도구일 뿐, 모든 상황에 적용할 필요는 없음** (Design patterns are just tools, not necessary for every situation)
- **단순한 문제는 단순하게 해결** (Solve simple problems simply)
- **패턴 적용 시 코드 복잡도 고려** (Consider code complexity when applying patterns)

```tsx
// 나쁜 예시: 과도한 패턴 사용
type ButtonType = "primary" | "secondary";

interface ButtonFactoryProps {
  children: React.ReactNode;
}

const ButtonFactory = {
  create: (type: ButtonType) => {
    const ButtonComponent: React.FC<ButtonFactoryProps> = ({
      children,
      ...props
    }) => {
      const style: Record<ButtonType, string> = {
        primary: "btn-primary",
        secondary: "btn-secondary",
      };
      return (
        <button className={style[type]} {...props}>
          {children}
        </button>
      );
    };
    return ButtonComponent;
  },
};

// 좋은 예시: 단순한 구현
interface SimpleButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  type?: ButtonType;
  children: React.ReactNode;
}

const Button: React.FC<SimpleButtonProps> = ({
  type = "primary",
  children,
  ...props
}) => {
  const style = type === "primary" ? "btn-primary" : "btn-secondary";
  return (
    <button className={style} {...props}>
      {children}
    </button>
  );
};
```

## 8. 문제 해결 예시 (Problem Solving Examples)

### 1. 상태 관리 문제 (State Management Problem)

```tsx
// 문제: 여러 컴포넌트에서 동일한 상태 공유 필요
// 해결: Context API와 Custom Hook 활용
interface User {
  id: string;
  name: string;
  email: string;
}

interface UserContextType {
  user: User | null;
  login: (userData: User) => void;
  logout: () => void;
}

const UserContext = createContext<UserContextType | undefined>(undefined);

interface UserProviderProps {
  children: React.ReactNode;
}

const UserProvider: React.FC<UserProviderProps> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);
  const login = (userData: User): void => setUser(userData);
  const logout = (): void => setUser(null);

  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
};

const useUser = (): UserContextType => {
  const context = useContext(UserContext);
  if (!context) throw new Error("useUser must be used within UserProvider");
  return context;
};
```

### 2. 성능 최적화 문제 (Performance Optimization Problem)

```tsx
// 문제: 불필요한 리렌더링 발생
// 해결: React.memo와 useMemo 활용
interface DataItem {
  id: string;
  value: number;
  processed?: number;
}

interface ExpensiveComponentProps {
  data: DataItem[];
}

const complexCalculation = (item: DataItem): number => {
  // 복잡한 계산 로직
  return item.value * 2;
};

const ExpensiveComponent: React.FC<ExpensiveComponentProps> = React.memo(
  ({ data }) => {
    const processedData = useMemo(() => {
      return data.map((item) => ({
        ...item,
        processed: complexCalculation(item),
      }));
    }, [data]);

    return <div>{/* 렌더링 로직 */}</div>;
  },
);
```

### 3. 에러 처리 문제 (Error Handling Problem)

```tsx
// 문제: 비동기 작업의 에러 처리
// 해결: Error Boundary와 Custom Hook 활용 (useReducer 사용)
interface AsyncState<T> {
  data: T | null;
  error: Error | null;
  loading: boolean;
}

type AsyncAction<T> =
  | { type: "LOADING" }
  | { type: "SUCCESS"; payload: T }
  | { type: "ERROR"; payload: Error }
  | { type: "RESET" };

const asyncReducer = <T,>(
  state: AsyncState<T>,
  action: AsyncAction<T>,
): AsyncState<T> => {
  switch (action.type) {
    case "LOADING":
      return { ...state, loading: true, error: null };
    case "SUCCESS":
      return { data: action.payload, loading: false, error: null };
    case "ERROR":
      return { ...state, loading: false, error: action.payload };
    case "RESET":
      return { data: null, loading: false, error: null };
    default:
      return state;
  }
};

interface UseAsyncReturn<T> extends AsyncState<T> {
  execute: (...args: any[]) => Promise<void>;
  reset: () => void;
}

const useAsync = <T,>(
  asyncFunction: (...args: any[]) => Promise<T>,
): UseAsyncReturn<T> => {
  const [state, dispatch] = useReducer(asyncReducer<T>, {
    data: null,
    loading: false,
    error: null,
  });

  const execute = async (...args: any[]): Promise<void> => {
    try {
      dispatch({ type: "LOADING" });
      const result = await asyncFunction(...args);
      dispatch({ type: "SUCCESS", payload: result });
    } catch (err) {
      dispatch({ type: "ERROR", payload: err as Error });
    }
  };

  const reset = (): void => {
    dispatch({ type: "RESET" });
  };

  return { ...state, execute, reset };
};
```
