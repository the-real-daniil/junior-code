# Джун пилит архитектуру

## Компоненты на 400 строк, отвечают за все и сразу (Single responsibility идет лесом)

❌ **Плохо:**

```tsx
// App.tsx
function App() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    setLoading(true);
    fetch("/api/user")
      .then((res) => res.json())
      .then(setUser)
      .finally(() => setLoading(false));
  }, []);

  return (
    <div>
      {loading && <p>Загрузка...</p>}
      {user && (
        <div>
          <h1>{user.name}</h1>
          <p>{user.email}</p>
        </div>
      )}
      <form>...</form>
      {/* ещё 300 строк логики */}
    </div>
  );
}
```

✅ **Хорошо:**

```tsx
function App() {
  const { user, loading } = useUser();

  return (
    <main>
      {loading ? <Loading /> : <UserCard user={user} />}
      <ContactForm />
    </main>
  );
}
```

## Абстракции протекают (смешивается высокоуровневый и низкоуровневый код)

❌ **Плохо:**

```ts
function handleLoginSuccess(user: User) {
  // высокоуровневое
  toast.success(`Добро пожаловать, ${user.name}!`);
  navigate("/dashboard");
  sendAnalytics("login_success");

  // низкоуровневое
  localStorage.setItem("token", user.token);
  const el = document.getElementById("main-title");
  if (el) el.style.color = "green";

  // ещё немного логики
  if (window.innerWidth < 768) {
    alert("Переверни телефон");
  }
}
```

✅ **Хорошо:**

```ts
function handleLoginSuccess(user: User) {
  saveUser(user);
  showSuccessUI(user.name);
  trackLogin();
}

function saveUser(user: User) {
  localStorage.setItem("token", user.token);
}

function showSuccessUI(name: string) {
  toast.success(`Добро пожаловать, ${name}!`);
  navigate("/dashboard");
  highlightTitle(); // отдельно
}

function highlightTitle() {
  const el = document.getElementById("main-title");
  if (el) el.style.color = "green";
}

function trackLogin() {
  sendAnalytics("login_success");
  if (window.innerWidth < 768) {
    alert("Переверни телефон");
  }
}
```

## Отсутствуют контракты между модулями и сущностями

❌ **Плохо:**

```ts
// fileStorage.ts
export class FileStorage {
  save(path: string, content: string) {
    // сохраняем файл в файловую систему
  }

  read(path: string): string {
    // читаем файл
    return "";
  }
}

// s3Storage.ts
export class S3Storage {
  upload(key: string, body: string) {
    // загружаем на S3
  }

  download(key: string): string {
    // получаем с S3
    return "";
  }
}
```

✅ **Хорошо:**

```ts
// storage.interface.ts
export interface Storage {
  write(key: string, content: string): void;
  read(key: string): string;
}
```

```ts
// fileStorage.ts
export class FileStorage implements Storage {
  write(path: string, content: string) {
    // сохранить в файл
  }

  read(path: string): string {
    // прочитать из файла
    return "";
  }
}
```

```ts
// s3Storage.ts
export class S3Storage implements Storage {
  write(key: string, body: string) {
    // сохранить в S3
  }

  read(key: string): string {
    // получить из S3
    return "";
  }
}
```

```ts
// usage.ts
function backupData(storage: Storage) {
  storage.write("backup.txt", "Данные");
}
```

## Винегрет вместо слоеного пирога (отсутствие слоев в архитектуре)

❌ **Плохо:**

```
src/
├── App.tsx
├── api.ts
├── button.jsx
├── userStyles.js
├── utils.ts
├── formLogic.ts
```

✅ **Хорошо:**

```
src/
├── pages/
│   └── HomePage.tsx
├── features/
│   └── user/
│       ├── components/
│       ├── hooks/
│       ├── services/
│       └── types/
├── shared/
│   └── ui/
│   └── utils/
```

```
src/
├── pages/
│   └── HomePage.tsx
│   └── LoginPage.tsx
├── components/
│   └── user/
│   └── loginForm/
├── services/
│   └── users/
│   └── auth/
├── store/
│   └── users/
│   └── auth/
├── shared/
│   └── ui/
│   └── utils/
│   └── hooks/
```

## Импортный ад ../../../utils/helpers/formatDate.js, нет index.js файлов

❌ **Плохо:**

```ts
import formatDate from "../../../utils/helpers/formatDate";
import Button from "../../../../../shared/components/ui/Button";
```

✅ **Хорошо:**

```ts
// utils/index.ts
export * from "./helpers/formatDate";
```

```ts
// components/index.ts
export * from "./components/ui/Button";
```

```ts
import { formatDate } from "@shared/utils";
import { Button } from "@shared/components";
```
