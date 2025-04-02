# Джун пилит UI

## Пишет неконсистентный CSS

- использует то `margin`, то `padding` в аналогичных ситуациях

  ❌ **Плохо:**

  ```jsx
  <div style={{marginRight: '10px'}}>Block 1</div>
  <div style={{paddingRight: '10px'}}>Block 2</div>
  <div>Block 3</div>
  ```

- использует то `flex`, то `grid` в аналогичных ситуациях

  ❌ **Плохо:**

  ```jsx
  // Первый компонент
  const CardListFlex = () => (
  	<div className="card-list-flex">
  		<div className="card">...</div>
  		<div className="card">...</div>
  	</div>
  );

  /* styles.css */
  .card-list-flex {
  	display: flex;
  	gap: 16px;
  }

  // Второй компонент
  const CardListGrid = () => (
  	<div className="card-list-grid">
  		<div className="card">...</div>
  		<div className="card">...</div>
  	</div>
  );

  /* styles.css */
  .card-list-grid {
  	display: grid;
  	grid-template-columns: repeat(2, 1fr);
  	gap: 16px;
  }
  ```

- для стилизации совмещает сразу три подхода: `inline`, `css-modules`, `styled-components`

  ❌ **Плохо:**

  ```jsx
  <div style={{ marginTop: 10 }}>Inline</div>
  <div className={styles.block}>CSS Modules</div>
  <StyledButton>Styled-components</StyledButton>
  ```

## Игнорирует токены из дизайн-системы (глобальные css-переменные)

❌ **Плохо:**

```css
.button {
  color: #1e90ff;
  font-size: 17px;
  padding: 13px 24px;
}
```

✅ **Хорошо (с токенами):**

```css
.button {
  color: var(--color-primary);
  font-size: var(--font-size-m);
  padding: var(--spacing-m) var(--spacing-l);
}
```

## Игнорирует принципы доступности a11y (aria-атрибуты, скрин-ридеры)

❌ **Плохо:**

```jsx
<button onClick={submit}>Отправить</button>
```

```html
<img src="/logo.png" />
```

✅ **Хорошо:**

```jsx
<button onClick={submit} aria-label="Отправить форму">
  📤
</button>
```

```html
<img src="/logo.png" alt="Логотип компании" />
```

## Не следит за семантикой html-тегов (везде использует div)

❌ **Плохо:**

```jsx
<div onClick={goHome}>Главная</div>
<div>Заголовок страницы</div>
<div>
    <div>Элемент списка 1</div>
    <div>Элемент списка 2</div>
</div>
```

✅ **Хорошо:**

```jsx
<button onClick={goHome}>Главная</button>
<h1>Заголовок страницы</h1>
<ul>
    <li>Элемент списка 1</li>
    <li>Элемент списка 2</li>
</ul>
```
