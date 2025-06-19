# **📜 AI Development Guideline**

## 🗂️ Table of Contents

This guideline is a single document composed of five main sections. Click on an item to navigate to the corresponding section.

1.  [**🏁 Overview**](#-overview)
2.  [**🌐 Universal Principles**](#-universal-principles)
3.  [**🎨 Frontend Guideline**](#-frontend-guideline)
4.  [**⚙️ Backend Guideline**](#-backend-guideline)
5.  [**🤝 Collaboration and Verification Process**](#-collaboration-and-verification-process)

---

## 🏁 Overview

**This document defines the technical standards and procedures to enhance consistency, quality, and collaboration efficiency across all AI development projects.** This is not just a recommendation but a shared commitment to quality, collaboration, and sustainable growth.

### 🎯 Core Principle

#### **Principle 1: Scope Clarity**

> **Never touch code outside the requested scope.**
> This is the foundation of trust and a predictable development environment. Clearly recognize the boundaries of your assigned task and refrain from making modifications beyond that scope.

### 🔑 The Five Core Principles

> 1.  **DRY (Don't Repeat Yourself)**: Avoid duplication. Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.
> 2.  **SRP (Single Responsibility Principle)**: A module should have only one reason to change. This means every module should have responsibility over a single part of the functionality.
> 3.  **YAGNI (You Ain't Gonna Need It)**: Do not add functionality until it is deemed necessary. Implement the simplest thing that could possibly work right now.
> 4.  **Readability First**: Code is read more often by humans than by machines. Clarity is superior to cleverness.
> 5.  **Secure by Default**: Security is not an afterthought; it's a foundational principle that begins at the design phase of every piece of code.

---
> **"Well-written code is its own best documentation."**

---

## 🌐 Universal Principles

**This section defines development principles that apply universally to all code, regardless of language or platform.**

### ✨ 1. Code Quality Principles
- **Readability**: The intent of the code should be over 90% clear from variable and function names alone. The code shows *how*, and comments explain *why*.
- **Predictability**: A function should do exactly what its name suggests. Minimize surprising side effects. Strive for pure functions.
- **Maintainability**: Code should be easy to change. Aim for a structure that can accommodate new requirements with minimal modifications by lowering coupling and increasing cohesion.
- **Testability**: Code with low dependency and composed of pure functions is easy to test. Dependency Injection (DI) is a key technique to improve testability.

### 🏗️ 2. Common Code Structure
- **Functions/Methods**: Should adhere to the Single Responsibility Principle, with a recommended maximum of 20 lines and 3 levels of nesting.
- **Files/Modules**: One file should encapsulate one primary concept (e.g., a class, component). Aim to keep file length under 300 lines.
- **Layer Separation**: Clearly separate layers—Presentation, Business, and Data Access—to define distinct responsibilities.

### 🌿 3. Git Policy
#### **Branching Strategy**
- `feat/{feature-name}`: For new feature development (e.g., `feat/user-authentication`)
- `fix/{issue-number}`: For bug fixes (e.g., `fix/123-login-error`)
- `refactor/{target}`: For code refactoring (e.g., `refactor/user-service`)
- `docs/{document-name}`: For documentation changes (e.g., `docs/api-guide`)
- `chore/{task-name}`: For build tasks, configuration, etc. (e.g., `chore/update-dependencies`)

#### **Commit Messages (Conventional Commits Standard)**
Commit messages must clearly convey the "what" and "why" of a change.
- `feat`: A new feature
- `fix`: A bug fix
- `docs`: Documentation only changes
- `style`: Changes that do not affect the meaning of the code (formatting, etc.)
- `refactor`: A code change that neither fixes a bug nor adds a feature
- `test`: Adding missing tests or correcting existing tests
- `chore`: Changes to the build process or auxiliary tools

**Good Example**:
```
feat(auth): Implement JWT-based login

Adds an API endpoint that issues an access token and a refresh token
when a user logs in with an email and password.
The access token has a 15-minute validity, and the refresh token is valid for 7 days.

Fixes #45
```
**Bad Example**: `bug fix`, `work done`

### 🏷️ 4. Naming Conventions
- `camelCase`: For variables and functions (`userList`, `fetchData`)
- `PascalCase`: For classes, components, types, and interfaces (`UserService`, `UserProfile`)
- `UPPER_SNAKE_CASE`: For constants and environment variables (`MAX_RETRY_COUNT`, `DATABASE_URL`)
- `is/has/can`: Prefixes for boolean values (`isLoading`, `hasPermission`)

### 📦 5. Package/Dependency Management
#### **Checklist Before Adding a Library**
```
[ ] Is this functionality impossible to achieve with the core library/framework?
[ ] Is it truly necessary? (YAGNI)
[ ] Is its license compatible with the project? (MIT, Apache 2.0 preferred)
[ ] Are there any known security vulnerabilities? (Use npm audit, Snyk)
[ ] Is the community active and is it well-maintained? (Check GitHub stars, recent commits)
[ ] Is the impact on the bundle/build size acceptable?
```

### 🚫 6. Universal Prohibitions
- **Security**: Hardcoded secrets, plaintext passwords, unvalidated user input, logging sensitive information.
- **Quality**: Reckless use of `any` type, `@ts-ignore`, `var`, `==` operator, `eval()`, empty `catch` blocks, `console.log` in production code.
- **Structure**: Circular dependencies, excessive use of global variables, magic numbers/strings.

#### **Magic Number/String Refactoring Example**
```typescript
// ❌ Bad: The meaning of the number and string is unclear.
if (user.role === 1) { // Is 1 an admin?
  // ...
}
setTimeout(doSomething, 3000);

// ✅ Good: Replaced with constants that carry semantic meaning.
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

## 🎨 Frontend Guideline

**This section provides in-depth guidelines for building high-quality user interfaces (UI) and user experiences (UX).**

### 🏗️ 1. Architecture Principles
- **Component-Driven Development (CDD)**: Think of the UI as a composition of independent, reusable parts.
    - **Presentational (Dumb) Components**: Focus solely on UI rendering. Receive data only via props.
    - **Container (Smart) Components**: Manage logic and state. Responsible for API calls and state management integration.
- **Folder Structure**: Organize files by domain (feature) to increase cohesion. Related components, hooks, types, and services should be co-located.
    ```
    /src/features/user
    ├── /components
    │   ├── UserProfile.tsx
    │   └── UserList.tsx
    ├── /hooks
    │   └── useUserQuery.ts
    ├── /services
    │   └── user.api.ts
    └── index.ts (Public API export for the feature)
    ```
- **API Communication Layer**: Use a dedicated `services` or `api` layer to completely decouple components from API call logic.

### 🧠 2. State Management Strategy
Choose the right tool based on the state's scope and lifecycle.

| State Type | Recommended Tool | Example |
| :--- | :--- | :--- |
| **Local UI State** | `useState`, `ref` | Modal open/close, input value |
| **Server Cache State**| `TanStack Query` | User profile, product list |
| **Global App State**| `Zustand`, `Pinia` | Auth status, theme |
| **URL State** | `Router params/query`| Search filters, page number |

### 💻 3. Tech-Specific Implementation (React/Vue)

#### **React Component Structure Example**
```typescript
// File: src/features/user/components/UserProfile.tsx

// 1. Imports
import React, { useState, useEffect, useMemo, useCallback, useRef } from 'react';
import { useUserQuery } from '../hooks/useUserQuery';
import { Spinner } from '@/components/common/Spinner';
import { ErrorDisplay } from '@/components/common/ErrorDisplay';

// 2. Type/Interface Definitions
interface UserProfileProps {
  userId: string;
}

// 3. Component Function
export function UserProfile({ userId }: UserProfileProps) {
  // 4. State & Refs
  const [isEditing, setIsEditing] = useState(false);
  const nameInputRef = useRef<HTMLInputElement>(null);

  // 5. Custom Hooks & Data Fetching
  const { data: user, error, isLoading } = useUserQuery(userId);

  // 6. Memoized Values
  const userStatus = useMemo(() => {
    return user?.isActive ? 'Active' : 'Inactive';
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

  // 9. Early Returns
  if (isLoading) return <Spinner />;
  if (error) return <ErrorDisplay error={error} />;
  if (!user) return <div>User not found.</div>;

  // 10. Main Render (JSX)
  return (
    <div className="user-profile">
      <h1>{user.name}</h1>
      <p>Email: {user.email}</p>
      <p>Status: {userStatus}</p>
      <button onClick={handleEditToggle}>{isEditing ? 'Save' : 'Edit'}</button>
      {isEditing && (
        <input ref={nameInputRef} defaultValue={user.name} />
      )}
    </div>
  );
}
```

#### **Vue Component Structure Example**
```vue
<!-- File: src/features/user/components/UserProfile.vue -->
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

// 4. Reactive State
const isEditing = ref(false);
const nameInput = ref<HTMLInputElement | null>(null);

// 5. Computed Properties
const userStatus = computed(() => (user.value?.isActive ? 'Active' : 'Inactive'));

// 6. Methods
const handleEditToggle = () => {
  isEditing.value = !isEditing.value;
};

// 7. Watchers & Lifecycle
watch(isEditing, async (isNowEditing) => {
  if (isNowEditing) {
    await nextTick(); // Wait for DOM update
    nameInput.value?.focus();
  }
});
</script>

<template>
  <div v-if="isLoading" class="spinner-container">
    <Spinner />
  </div>
  <div v-else-if="error" class="error-container">
    <p>An error occurred: {{ error.message }}</p>
  </div>
  <div v-else-if="user" class="user-profile">
    <h1>{{ user.name }}</h1>
    <p>Email: {{ user.email }}</p>
    <p>Status: {{ userStatus }}</p>
    <button @click="handleEditToggle">{{ isEditing ? 'Save' : 'Edit' }}</button>
    <input v-if="isEditing" ref="nameInput" :value="user.name" />
  </div>
  <div v-else>
    <p>User not found.</p>
  </div>
</template>
```

### 🖌️ 4. Styling Strategy
- **CSS Modules**: The default choice for component-scoped styles, preventing class name collisions.
- **CSS-in-JS (e.g., Styled Components)**: Use sparingly for cases requiring complex dynamic styling or theming.
- **Tailwind CSS**: Consider for rapid prototyping and building consistent design systems with a utility-first approach.
- **Selection Criteria**: Choose based on team proficiency, project scale, and the need for dynamic styles.

### ♿ 5. Web Accessibility (A11y)
- **Semantic HTML**: Use tags for their meaning (`nav`, `main`, `button`). `div` and `span` are a last resort.
- **Keyboard Navigation**: All interactive elements must be operable via keyboard (Tab, Enter, Space). The focus order must be logical.
- **ARIA Attributes**: Use attributes like `aria-live` and `aria-expanded` to inform screen readers of dynamic UI changes.
- **Labeling**: Every `input` must have a corresponding `label` or an `aria-label`.
- **Color Contrast**: Adhere to WCAG 2.1 AA standards (minimum 4.5:1 contrast ratio) to ensure text readability.

### 🚫 6. Frontend Forbidden Patterns
- **Excessive Prop Drilling (3+ levels)**: Solve with Context API, state management libraries, or component composition.
- **`useEffect` Abuse**: Prefer `TanStack Query` for server state synchronization and `useMemo` for derived state. Use `useEffect` judiciously only for true synchronization needs.
- **Using Array Index as a `key`**: This can cause unpredictable bugs during re-renders. Always use a unique, stable ID.
- **`!important` Abuse**: Understand and leverage CSS Specificity. Use `!important` only as a final, unavoidable measure.

---

## ⚙️ Backend Guideline

**This section provides guidelines for building robust, scalable, and secure backend systems.**

### 🏗️ 1. Architecture Principles

#### **Layered Architecture Example (Java/Spring)**
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
    private final UserRepository userRepository; // Depend on interfaces, not concrete classes

    @Override
    @Transactional(readOnly = true)
    public UserResponseDto findUserById(Long id) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("User not found with id: " + id));
        // Convert Entity to DTO before returning
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
    private String password; // Hashed password
}

// === Data Transfer Object (DTO) ===
@Getter
@AllArgsConstructor
public class UserResponseDto {
    private Long id;
    private String name;
    private String email;
    // Never include sensitive fields like password

    public static UserResponseDto from(User user) {
        return new UserResponseDto(user.getId(), user.getName(), user.getEmail());
    }
}
```

### 📡 2. API Design Standards
- **RESTful Principles**: Use resource-oriented URLs (`GET /users/{id}`), proper HTTP methods, and standard HTTP status codes.
- **Standard Response Format**: Use a consistent JSON response structure (e.g., with `data`, `message`, `error` fields) to improve client-side handling.
- **Pagination**: Implement cursor-based or offset-based pagination for endpoints that return large datasets.

### 💡 3. Tech-Specific Guides (Java/Spring & Python)**
#### **Java/Spring**
- **Dependency Injection**: Use constructor injection (via `@RequiredArgsConstructor`) to ensure immutability and prevent circular dependencies.
- **Exception Handling**: Implement global exception handling with `@ControllerAdvice`. Throw business-specific exceptions from the service layer.
- **Transaction Management**: Use `@Transactional` for CUD operations and `@Transactional(readOnly = true)` for read operations to optimize performance.

#### **Python (FastAPI/Django)**
- **Type Hinting**: Mandatory for all function signatures and key variables. Perform static analysis with `mypy`.
- **Data Validation**: Use `Pydantic` (FastAPI) or `Serializers` (Django DRF) to rigorously validate data at the API boundary.
- **ORM Optimization**: Actively use `select_related` and `prefetch_related` (Django) to prevent N+1 query problems.

### 🔐 4. Security and Data Management
- **Authentication/Authorization**: Default to stateless JWT-based authentication. Apply Role-Based Access Control (RBAC) to endpoints.
- **Input Validation**: Sanitize and validate all user input to prevent SQL Injection, XSS, etc. Use parameterized queries (default in most ORMs).
- **Sensitive Information**: Hash passwords with `bcrypt`. Manage API keys and other secrets via environment variables or a secret manager.

### 📊 5. Logging and Monitoring
- **Structured Logging**: Output logs in JSON format for easier parsing and analysis.
- **Appropriate Log Levels**: Use `DEBUG`, `INFO`, `WARN`, `ERROR` levels correctly to make logs meaningful.
- **Metrics Collection**: Monitor key metrics like request latency, error rates, and throughput using tools like Prometheus and Grafana.

### 🚫 6. Backend Forbidden Patterns
- **N+1 Queries**: Do not execute queries inside a loop. Use eager loading (join fetch) or batch fetching.
- **Exposing Entities Directly**: Always convert entities to DTOs before returning them from a controller.
- **Synchronous Long-Running Tasks**: Offload tasks expected to take more than a few seconds to a message queue for asynchronous processing.
- **Overly Broad Transaction Scopes**: Keep transactions as short as possible, and separate them from external I/O like network calls.

---

## 🤝 Collaboration and Verification Process

**This section defines the protocols for effective teamwork and maintaining code quality.**

### 🔄 1. Collaboration Process
#### **Workflow**
1.  **Initiation**: Assign/take an issue from the ticket system → Create a branch → Analyze existing code and impact scope.
2.  **Development**: Code according to guidelines → Write unit tests → Perform a self-review.
3.  **Completion**: Create a Pull Request (PR) → Peer code review → Pass CI/CD pipeline → Merge branch → Close issue.

#### **Communication Rules**
- **Async-First Communication**: For non-urgent matters, use async channels like Slack or Jira comments to respect your colleagues' focus time.
- **Document Decisions**: All significant technical decisions must be documented in a wiki or meeting notes for traceability.
- **Specific Error Reporting**: Instead of "It's not working," provide specifics: "When I do A, I get B error. I have already tried C."

#### **Code Review Guideline**
A code review is not about finding flaws; it's a collaborative process to improve code quality and share knowledge.
- **For the Reviewer**: Use suggestions, not commands. Base feedback on principles and evidence, not personal preference. Praise good work. (e.g., "Change this" (X) -> "What do you think about extracting this into a separate function to follow SRP?" (O))
- **For the Author**: Separate yourself from your code. Instead of being defensive, ask questions to understand the feedback more deeply. (e.g., "Could you elaborate on why you think that's a better approach?")

#### **Pull Request Template Example**
```markdown
### 1. Summary
Adds the ability for users to upload a profile picture.

### 2. Context
- This feature allows users to personalize their profiles.
- Related Issue: #78

### 3. Changes
- Added `POST /api/users/{id}/avatar` API endpoint.
- Implemented `S3UploadService` for uploading images to S3.
- Added a file upload UI to the frontend.

### 4. Points for Review
- Please review the exception handling logic in `S3UploadService` for robustness.
- Looking for feedback on whether the 5MB file size limit is appropriate.

### 5. Potential Risks
- The system may need additional defense against malicious file uploads (e.g., executable scripts).
```

### ✅ 2. Pre-Merge Verification Checklist
Review your work against this checklist before merging.

#### **Common Quality Checklist**
```
[ ] Did I adhere to the Scope Clarity principle?
[ ] Did I violate any of the Five Core Principles (DRY, SRP, etc.)?
[ ] Did I follow naming conventions and avoid magic numbers/strings?
[ ] Is error handling explicit and are failure scenarios considered?
[ ] Have I written or updated tests for the new/modified logic?
```
#### **Frontend-Specific Checklist**
```
[ ] Are all interactive elements keyboard-accessible? (Accessibility)
[ ] Are there any unnecessary re-renders causing performance issues? (Performance)
[ ] Does the UI render correctly on mobile devices? (Responsive Design)
```
#### **Backend-Specific Checklist**
```
[ ] Is all external input validated? (Security)
[ ] Are there any inefficient queries like N+1? (Performance)
[ ] Is any sensitive information exposed in logs or API responses? (Security)
```

### 🤖 3. AI Collaboration Keywords
Use these standard keywords to give clear instructions to an AI assistant.

- **Common**: `optimize`, `refactor`, `write_tests`, `document`
- **Frontend**: `separate_component`, `improve_accessibility`, `apply_responsive_design`
- **Backend**: `design_api`, `optimize_query`, `enhance_security`, `apply_caching_strategy`

**Usage Examples**:
- `"The existing UserProfile component is too large. Execute 'separate_component' to break it down into smaller, single-purpose components."`
- `"The user creation API is slow. Execute 'optimize_query' to analyze the related logic and suggest improvements."`
