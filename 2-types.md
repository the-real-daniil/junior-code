# Джун пилит типизацию

## Использует any, as, @ts-ignore

❌ **Плохо:**

```ts
const data: any = getData();

const user = data as User;

function renderUserCard(data: Data) {
  // ...
}

// @ts-ignore
const card = renderUserCard(user);
```

✅ **Хорошо:**

```ts
interface User {
  id: string;
  name: string;
}

const data = getData() as unknown;

if (isUser(data)) {
  const user: User = data;
}

function isUser(obj: unknown): obj is User {
  return (
    typeof obj === "object" && obj !== null && "id" in obj && "name" in obj
  );
}
```

## Использует типы с бэкенда на фронтенде без маппинга

❌ **Плохо:**

```ts
// Тип с бэка:
type BackendUser = {
  user_id: number;
  user_name: string;
};

// прямо его и используем на фронте:
const user: BackendUser = fetchUser();
```

✅ **Хорошо:**

```ts
type BackendUser = {
  user_id: number;
  user_name: string;
  created_at: string;
};

interface FrontendUser {
  id: number;
  name: string;
  createdAt: Date;
}

function mapUser(data: BackendUser): FrontendUser {
  return {
    id: data.user_id,
    name: data.user_name,
    createdAt: new Date(data.created_at),
  };
}

const user = mapUser(await fetchUser());
```

## Ошибки eslint, ts. CI падает. Локально не настроены плагины
