# Налаштування статистики відвідувань сайту

## 1. Google Analytics (Рекомендований)

### Крок 1: Реєстрація
1. Перейдіть на https://analytics.google.com
2. Увійдіть за допомогою Google акаунта
3. Натисніть "Почати вимірювання"

### Крок 2: Налаштування властивості
1. Введіть назву акаунта (наприклад: "Мої ножі")
2. Виберіть "Веб" як тип властивості
3. Введіть URL сайту: `https://yourusername.github.io/my-knives`
4. Введіть назву сайту: "Галерея ножів ручної роботи"

### Крок 3: Отримання коду
1. Скопіюйте ваш Measurement ID (типу: G-XXXXXXXXXX)
2. У файлі `index.html` замініть `YOUR_GA_ID` на ваш реальний ID

### Що ви отримаєте:
- 📊 Кількість відвідувачів
- 🌍 Географія відвідувачів
- 📱 Тип пристроїв (мобільні/десктоп)
- ⏱️ Час проведений на сайті
- 📈 Популярні сторінки

## 2. Безкоштовні лічильники (без реєстрації)

### ClustrMaps
1. Перейдіть на https://clustrmaps.com
2. Введіть URL вашого сайту
3. Оберіть дизайн лічильника
4. Скопіюйте код та замініть `YOUR_CLUSTRMAPS_ID`

### Free-Counters.org
1. Перейдіть на https://www.free-counters.org
2. Створіть лічильник
3. Замініть URL у коді на отриманий

### StatCounter
1. Перейдіть на https://statcounter.com
2. Зареєструйтеся безкоштовно
3. Додайте ваш сайт
4. Скопіюйте код відстеження

## 3. Простий власний лічильник

### Варіант A: localStorage (тільки для одного браузера)

```javascript
// Простий лічильник у localStorage (рахує тільки відвідування в одному браузері)
let visits = localStorage.getItem('visits') || 0;
visits++;
localStorage.setItem('visits', visits);
document.getElementById('visitor-count').textContent = visits;
```

**Переваги:** Швидко, без реєстрації
**Недоліки:** Рахує тільки відвідування в одному браузері/пристрої

### Варіант B: GitHub API + JSON файл (реальна статистика)

Цей метод використовує GitHub API для збереження статистики у JSON файлі.

#### Крок 1: Створіть файл статистики
Додайте файл `stats.json` до репозиторію:
```json
{
  "visits": 0,
  "lastUpdated": "2025-06-20"
}
```

#### Крок 2: Створіть GitHub Personal Access Token
1. Перейдіть на GitHub → Settings → Developer settings → Personal access tokens
2. Створіть новий token з правами на ваш репозиторій
3. Збережіть token (показується тільки один раз!)

#### Крок 3: Додайте JavaScript код
```javascript
// Лічильник з GitHub API
async function updateVisitCounter() {
  const repo = 'yourusername/my-knives'; // Замініть на ваш репозиторій
  const token = 'YOUR_GITHUB_TOKEN'; // Замініть на ваш token
  const apiUrl = `https://api.github.com/repos/${repo}/contents/stats.json`;
  
  try {
    // Отримуємо поточну статистику
    const response = await fetch(apiUrl, {
      headers: {
        'Authorization': `token ${token}`,
        'Accept': 'application/vnd.github.v3+json'
      }
    });
    
    const data = await response.json();
    const currentStats = JSON.parse(atob(data.content));
    
    // Збільшуємо лічильник
    currentStats.visits++;
    currentStats.lastUpdated = new Date().toISOString().split('T')[0];
    
    // Оновлюємо файл на GitHub
    await fetch(apiUrl, {
      method: 'PUT',
      headers: {
        'Authorization': `token ${token}`,
        'Accept': 'application/vnd.github.v3+json',
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        message: 'Update visit counter',
        content: btoa(JSON.stringify(currentStats, null, 2)),
        sha: data.sha
      })
    });
    
    // Показуємо результат
    document.getElementById('visitor-count').textContent = currentStats.visits;
    
  } catch (error) {
    console.log('Помилка оновлення лічильника:', error);
    // Fallback до localStorage
    let visits = localStorage.getItem('visits') || 0;
    visits++;
    localStorage.setItem('visits', visits);
    document.getElementById('visitor-count').textContent = visits;
  }
}

// Викликаємо при завантаженні сторінки
updateVisitCounter();
```

#### Крок 4: Додайте HTML елемент
```html
<p>Відвідувань: <span id="visitor-count">0</span></p>
```

### Варіант C: Використання безкоштовного API

#### CountAPI.xyz (найпростіший)
```javascript
// Використання CountAPI.xyz
async function updateCounterAPI() {
  try {
    const response = await fetch('https://api.countapi.xyz/hit/yourdomain.com/visits');
    const data = await response.json();
    document.getElementById('visitor-count').textContent = data.value;
  } catch (error) {
    document.getElementById('visitor-count').textContent = 'N/A';
  }
}

updateCounterAPI();
```

#### GoatCounter (безкоштовний, з приватністю)
1. Зареєструйтеся на https://www.goatcounter.com
2. Додайте один рядок коду:
```html
<script data-goatcounter="https://YOURCODE.goatcounter.com/count" 
        async src="//gc.zgo.at/count.js"></script>
```

### Варіант D: Netlify Functions (якщо хостинг на Netlify)

```javascript
// netlify/functions/counter.js
exports.handler = async (event, context) => {
  // Код для збереження в базі даних або файлі
  return {
    statusCode: 200,
    body: JSON.stringify({ visits: 42 })
  };
};
```

### Рекомендації по вибору:

1. **localStorage** - для тестування, не реальна статистика
2. **GitHub API** - складно налаштувати, але безкоштовно
3. **CountAPI.xyz** - найпростіший реальний лічильник
4. **GoatCounter** - найкращий баланс простоти та функціональності
5. **Google Analytics** - найпотужніший, але складніший

### Безпека:

⚠️ **Важливо:** Ніколи не додавайте GitHub token прямо в код!
Використовуйте GitHub Secrets або environment variables.

### Приклад готового рішення з CountAPI:

```html
<div style="text-align: center; margin: 20px 0;">
  <p style="color: #666; font-size: 0.9em;">
    👁️ Переглядів: <span id="visitor-count" style="font-weight: bold;">завантаження...</span>
  </p>
</div>

<script>
async function loadCounter() {
  try {
    const response = await fetch('https://api.countapi.xyz/hit/bass85.github.io.my-knives/visits');
    const data = await response.json();
    document.getElementById('visitor-count').textContent = data.value;
  } catch (error) {
    document.getElementById('visitor-count').textContent = '∞';
  }
}
loadCounter();
</script>
```

## Рекомендації:

1. **Google Analytics** - найкраще для детальної аналітики
2. **ClustrMaps** - показує карту відвідувачів
3. **StatCounter** - проста альтернатива GA

## Після налаштування:

1. Замініть placeholder коди на реальні
2. Перевірте роботу в інкогніто режимі
3. Подивіться статистику через 24-48 годин

## Приватність:

Додайте в footer інформацію про використання cookies:
"Сайт використовує Google Analytics для збору анонімної статистики відвідувань"
