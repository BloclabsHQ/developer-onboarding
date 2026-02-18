# JavaScript & TypeScript

> TypeScript always. JavaScript never (in new code).

---

## TypeScript Over JavaScript

TypeScript adds types to JavaScript. Types catch bugs at compile time, enable better tooling, and serve as documentation.

```typescript
// ❌ JavaScript — what is user? What fields does it have?
function processUser(user) {
    return user.name.toUpperCase();  // Crashes if name is undefined
}

// ✅ TypeScript — self-documenting, compile-time safety
interface User {
    id: string;
    name: string;
    email: string;
    createdAt: Date;
}

function processUser(user: User): string {
    return user.name.toUpperCase();  // TypeScript ensures name exists
}
```

---

## Setup

```bash
# New project
mkdir my-project && cd my-project
npm init -y
npm install -D typescript @types/node
npx tsc --init

# tsconfig.json essentials
{
    "compilerOptions": {
        "strict": true,              // Enable ALL strict checks
        "target": "ES2022",
        "module": "ESNext",
        "moduleResolution": "bundler",
        "outDir": "./dist",
        "rootDir": "./src",
        "esModuleInterop": true,
        "skipLibCheck": true
    }
}
```

**`strict: true`** is mandatory. It catches the bugs that matter.

---

## Core Patterns

### Interfaces Over Classes
```typescript
// ✅ Interface — defines shape
interface Transaction {
    id: string;
    amount: number;
    chain: Chain;
    status: 'pending' | 'confirmed' | 'failed';
    createdAt: Date;
}

// Use with type narrowing
function isConfirmed(tx: Transaction): boolean {
    return tx.status === 'confirmed';
}
```

### Union Types & Type Guards
```typescript
type ApiResponse<T> = 
    | { success: true; data: T }
    | { success: false; error: string };

function handleResponse<T>(response: ApiResponse<T>): T {
    if (!response.success) {
        throw new AppError(response.error);
    }
    return response.data;  // TypeScript knows this is { success: true, data: T }
}
```

### Async/Await
```typescript
// Always use async/await, not .then() chains
async function fetchTransactions(userId: string): Promise<Transaction[]> {
    const response = await fetch(`/api/users/${userId}/transactions`);
    if (!response.ok) {
        throw new ApiError(`HTTP ${response.status}`, response.status);
    }
    return response.json();
}
```

---

## React Patterns (Frontend)

```typescript
// Functional components with TypeScript
interface Props {
    transaction: Transaction;
    onConfirm: (id: string) => void;
}

function TransactionCard({ transaction, onConfirm }: Props) {
    return (
        <div className="card">
            <h3>{transaction.amount} {transaction.chain}</h3>
            <span className={`badge ${transaction.status}`}>
                {transaction.status}
            </span>
            {transaction.status === 'pending' && (
                <button onClick={() => onConfirm(transaction.id)}>
                    Confirm
                </button>
            )}
        </div>
    );
}
```

### State Management
```typescript
// React Query (TanStack Query) for server state
const { data, isLoading, error } = useQuery({
    queryKey: ['transactions', userId],
    queryFn: () => fetchTransactions(userId),
});

// useState for local UI state
const [isModalOpen, setIsModalOpen] = useState(false);
```

---

## Tooling

```bash
# Linting
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin

# Formatting
npm install -D prettier

# Package managers (prefer pnpm for speed)
npm install -g pnpm
pnpm install        # 2-3x faster than npm
```

---

## Checklist

- [ ] TypeScript with `strict: true` on all projects
- [ ] ESLint + Prettier configured
- [ ] No `any` types (use `unknown` if truly unknown)
- [ ] Async/await over Promise chains
- [ ] Error handling on all async operations
- [ ] Interfaces for data shapes, types for unions

---

*← [Rust at BlocLabs](02-rust-at-bloclabs.md) · [Python for Developers →](04-python-for-devs.md)*
