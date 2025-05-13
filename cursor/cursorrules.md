# 🎯 토리(TORY) 개발 규칙

## 1. ⭐ 기본 원칙 (Basic Principles) ⭐

### 핵심 원칙 (Core Principles)

- **모든 응답은 한국어로 작성** (All responses must be in Korean)
- **코드 라인 수는 최소화** (Minimize lines of code - Less code = Less debt)
- **중복, 불필요한 코드, 불필요한 변경 금지** (Avoid duplication, unnecessary code, and unnecessary changes)
- **단일 책임 원칙 준수** (Follow Single Responsibility Principle)
  - **모든 코드는 한 가지 역할만 수행** (All code should have a single responsibility)
  - **컴포넌트는 UI 렌더링, 상태 관리, 이벤트 처리 중 하나의 역할만 담당** (Components should handle only one of: UI rendering, state management, or event handling)
  - **함수는 입력을 받아 하나의 결과만 반환** (Functions should take input and return a single result)
  - **유틸리티는 특정 도메인의 문제만 해결** (Utilities should solve problems in a specific domain)
  - **훅은 하나의 상태나 사이드 이펙트만 관리** (Hooks should manage only one state or side effect)

### 패키지 관리 (Package Management)

- **새로운 패키지/라이브러리 추가는 최소화** (Minimize adding new packages/libraries)
  - **기존 프로젝트의 패키지/라이브러리 우선 사용** (Use existing project packages/libraries first)
  - **새로운 패키지 추가 시 반드시 검토 및 승인 필요** (New package additions require review and approval)
  - **불필요한 의존성은 제거** (Remove unnecessary dependencies)

### 파일 관리 (File Management)

- **새로운 파일 생성 시 필수 확인사항** (Required checks when creating new files)
  - **파일 생성 전 사용될 위치 확인** (Check where the file will be used before creation)
  - **파일 생성 후 즉시 import 구문 추가** (Add import statements immediately after file creation)
  - **import 경로가 올바른지 확인** (Verify the import path is correct)
  - **순환 참조가 발생하지 않는지 확인** (Check for circular dependencies)

## 2. 🎨 프론트엔드 개발 원칙 (Frontend Development Principles)

- **🔄 컴포넌트 설계 (Component Design)**

  - **컴포넌트는 재사용 가능하도록 설계** (Components should be designed for reusability)
  - **Props는 명확하고 일관되게 사용** (Use props clearly and consistently)
  - **컴포넌트의 구조와 역할을 명확히 정의** (Clearly define the structure and role of components)
  - **컴포넌트 간의 의존성과 통신 방식을 명시** (Specify dependencies and communication methods between components)
  - **컴포넌트의 재사용성과 확장성을 고려한 설계** (Design components with reusability and extensibility in mind)

- **🎯 디자인 패턴 (Design Patterns)**

  - **컴포넌트 설계 시 적절한 디자인 패턴 적용** (Apply appropriate design patterns when designing components)
    - **컴포넌트 합성에는 Composite 패턴 활용** (Use Composite pattern for component composition)
    - **상태 관리에는 Observer 패턴 활용** (Use Observer pattern for state management)
    - **컴포넌트 생성에는 Factory 패턴 활용** (Use Factory pattern for component creation)
    - **컴포넌트 확장에는 Decorator 패턴 활용** (Use Decorator pattern for component extension)
  - **디자인 패턴은 과도하게 사용하지 않음** (Do not overuse design patterns)
    - **단순한 경우에는 패턴 적용을 지양** (Avoid applying patterns in simple cases)
    - **패턴 적용 시 코드 복잡도 증가를 고려** (Consider code complexity when applying patterns)
    - **패턴의 장단점을 충분히 검토 후 적용** (Apply patterns after thorough review of pros and cons)
  - **자세한 예시 코드는 [patterns.md](./patterns.md) 파일 참조** (See [patterns.md](./patterns.md) file for detailed examples)

- **⚡ 함수 작성 (Function Writing)**

  - **함수의 길이는 20줄 이내로 유지** (Functions should be no longer than 20 lines)
  - **공통 로직은 유틸리티 함수로 추출** (Extract common logic into utility functions)
  - **함수의 입력과 출력을 명확히 정의** (Clearly define the input and output of functions)
  - **함수의 부작용을 최소화** (Minimize side effects of functions)
  - **함수의 테스트 용이성을 고려** (Consider testability of functions)

- **✨ 코드 간결성 (Code Conciseness)**

  - **불필요한 코드와 중복을 제거** (Remove unnecessary code and duplication)
  - **복잡한 로직은 함수로 분리하고 단순화** (Separate and simplify complex logic into functions)
  - **코드의 가독성과 유지보수성을 향상** (Improve code readability and maintainability)

- **📖 가독성 (Readability)**
  - **변수명, 함수명은 설명적으로 작성** (Use descriptive variable and function names)
    - 변수명은 명사로, 함수명은 동사로 시작 (Variables should be nouns, functions should start with verbs)
    - 이벤트 핸들러는 'handle' 접두사 사용 (Event handlers should use the 'handle' prefix)
    - 불리언 변수는 'is', 'has', 'can' 등으로 시작 (Boolean variables should start with 'is', 'has', 'can', etc.)
  - **주석은 코드의 의도와 목적을 명확히 설명** (Comments should clearly explain the intent and purpose of the code)
    - 주석은 '왜'를 설명해야 하며, '무엇'을 설명하지 않아야 함 (Comments should explain 'why', not 'what')
    - 주석은 코드와 함께 업데이트되어야 함 (Comments should be updated along with the code)

## 3. 🔄 코드 변경 (Code Changes)

- **반드시 "관련된 코드"만 최소한으로 변경** (Only modify related code minimally)
  - 변경 전에 코드의 영향 범위를 정확히 파악 (Understand the scope of changes before making them)
  - 변경 후에는 코드가 원래 의도대로 동작하는지 확인 (Verify that the code works as intended after changes)
- **코드 스타일, 네이밍, 주석 등은 특별 요청 없으면 변경 금지** (Do not change code style, naming, comments, etc. without special request)
  - 코드 스타일은 프로젝트의 기존 스타일을 따름 (Code style should follow the existing project style)
  - 네이밍은 프로젝트의 기존 네이밍 규칙을 따름 (Naming should follow the existing project naming rules)
- **리팩토링/정리/불필요한 삭제 금지** (No refactoring, cleanup, or unnecessary deletion)
  - 리팩토링은 별도의 요청이 있을 때만 수행 (Refactoring should only be done upon request)
  - 코드 정리는 별도의 요청이 있을 때만 수행 (Code cleanup should only be done upon request)
- **변경 전후 체크리스트(필요성, 영향, 최소범위, 부작용 등) 반드시 확인** (Always verify pre/post change checklist - necessity, impact, minimal scope, side effects)
  - 변경이 정말 필요한지 확인 (Verify if the change is really necessary)
  - 변경이 다른 코드에 영향을 주지 않는지 확인 (Verify if the change affects other code)
  - 변경이 최소한의 범위로 이루어지는지 확인 (Verify if the change is minimal in scope)
  - 변경이 기존 기능을 해치지 않는지 확인 (Verify if the change does not break existing functionality)

## 4. 📝 문서화 (Documentation)

- **함수 시작에 기능 설명 주석(JSDoc)** (Add function description comments (JSDoc) at the start of functions)
  - 함수의 목적, 매개변수, 반환값을 명확히 설명 (Clearly explain the purpose, parameters, and return value of the function)
  - 예외 상황에 대한 설명도 포함 (Include explanations for exceptional cases)
- **버그/비효율 발견 시 "TODO:"로 명확히 표시** (Clearly mark bugs/inefficiencies with "TODO:")
  - TODO 주석에는 문제의 원인과 해결 방법을 명시 (TODO comments should specify the cause and solution of the problem)
  - TODO 주석은 가능한 빨리 해결해야 함 (TODO comments should be resolved as soon as possible)
- **코드의 의도와 목적을 명확히 설명하는 주석 추가** (Add comments that clearly explain the intent and purpose of the code)
  - 복잡한 로직과 비즈니스 로직에 대한 설명 추가 (Add explanations for complex and business logic)
- **코드의 변경 이력과 이유를 기록** (Record the change history and reasons of the code)
- **코드의 테스트 결과와 성능을 기록** (Record the test results and performance of the code)

## 5. 🔍 문제 해결 (Problem Solving)

- **문제를 단계별로 분석 → 의사코드 → 계획 확인 → 코드 작성(Chain of Thought)** (Analyze the problem step by step → Pseudocode → Verify plan → Write code (Chain of Thought))
  - 문제를 작은 단위로 나누어 분석 (Break down the problem into smaller units)
  - 각 단계에 대한 의사코드 작성 (Write pseudocode for each step)
  - 계획이 올바른지 확인 (Verify if the plan is correct)
  - 코드 작성 후 테스트 (Test the code after writing)
- **문제 해결 시 다양한 접근 방법을 고려** (Consider various approaches when solving problems)
  - 여러 해결 방법을 비교하고 최적의 방법 선택 (Compare multiple solutions and choose the best one)
  - 다른 개발자의 의견을 참고 (Refer to other developers' opinions)
- **문제 해결 후 코드의 효율성과 가독성을 검토** (Review code efficiency and readability after problem solving)
  - 코드의 시간 복잡도와 공간 복잡도 확인 (Check the time and space complexity of the code)
  - 코드의 가독성과 유지보수성 확인 (Check the readability and maintainability of the code)
