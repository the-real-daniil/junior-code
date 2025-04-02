# Джун пилит код

## Не обрабатывает ошибки (try-catch)

❌ **Плохо:**

```js
const res = await fetch("/api/user");
const user = await res.json(); // падает, если 500
```

✅ **Хорошо:**

```js
try {
  const res = await fetch("/api/user");

  if (!res.ok) throw new Error("Ошибка запроса");

  const user = await res.json();
} catch (err) {
  console.error("Ошибка загрузки пользователя:", err);

  showErrorToast("Не удалось загрузить данные");
}
```

## Не обрабатывает краевые случаи (edge cases)

❌ **Плохо:**

```tsx
function UserCard({ user }) {
  return <p>{user.name}</p>; // если user = null → 💥
}
```

✅ **Хорошо:**

```tsx
function UserCard({ user }) {
  if (!user) return <SkeletonUser />;

  return <p>{user.name}</p>;
}
```

## Хардкодит переменные окружения

❌ **Плохо:**

```js
const API_URL = "https://api.productionserver.com";
```

✅ **Хорошо:**

```js
const API_URL = process.env.API_URL;
```

.env:

```
API_URL=https://api.productionserver.com
```

## Дублирует код

❌ **Плохо:**

```tsx
{
  user.role === "admin" && (
    <div className="card admin">
      <h3>{user.name}</h3>
      <p>Уровень доступа: максимальный</p>
      <p>Email: {user.email}</p>
    </div>
  );
}

{
  user.role === "editor" && (
    <div className="card editor">
      <h3>{user.name}</h3>
      <p>Уровень доступа: средний</p>
      <p>Email: {user.email}</p>
    </div>
  );
}

{
  user.role === "viewer" && (
    <div className="card viewer">
      <h3>{user.name}</h3>
      <p>Уровень доступа: минимальный</p>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

✅ **Хорошо:**

```tsx
const roleConfig = {
  admin: { className: "admin", access: "максимальный" },
  editor: { className: "editor", access: "средний" },
  viewer: { className: "viewer", access: "минимальный" },
} as const;

const { className, access } = roleConfig[user.role];

return (
  <div className={`card ${className}`}>
    <h3>{user.name}</h3>
    <p>Уровень доступа: {access}</p>
    <p>Email: {user.email}</p>
  </div>
);
```

## Использует magic numbers и magic strings

❌ **Плохо:**

```ts
if (user.role === "admin") {
  setAccessLevel(3);
}
```

✅ **Хорошо:**

```ts
const ROLES = {
  ADMIN: "admin",
} as const;

const ACCESS_LEVEL = {
  ADMIN: 3,
} as const;

if (user.role === ROLES.ADMIN) {
  setAccessLevel(ACCESS_LEVEL.ADMIN);
}
```

## Пишет велосипеды

❌ **Плохо:**

```ts
function debounce(fn: Function, delay: number) {
  let timer: any;
  return function (...args: any[]) {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
```

✅ **Хорошо:**

```ts
import debounce from "lodash.debounce";

const onSearch = debounce(handleSearch, 300);
```

## Использует односложные и не информативные название переменных

❌ **Плохо:**

```ts
const a = getData();
const b = a.user;
```

✅ **Хорошо:**

```ts
const response = getData();
const user = response.user;
```
