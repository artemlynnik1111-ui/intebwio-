# Решение проблемы API ошибки после 40 секунд

## 🔴 Проблема
После 40 секунд генерации страницы вы получаете "API error"

## ✅ Решение

### Шаг 1: Проверить API ключ
Запустите диагностический скрипт:
```bash
php /workspaces/intebwio-/public_html/diagnose-api.php
```

Этот скрипт проверит:
- ✓ Загружен ли API ключ
- ✓ Корректен ли формат ключа  
- ✓ Отвечает ли Gemini API

### Шаг 2: Добавить реальный API ключ

**Если скрипт выдал ошибку про "YOUR_GEMINI_API_KEY_HERE":**

1. Откройте файл `/public_html/includes/apikeys.php`

2. Найдите строку ~41-47:
```php
// Gemini API Key (Google)
// PASTE YOUR ACTUAL API KEY HERE:
$geminiKey = getenv('GEMINI_API_KEY');
if (!$geminiKey || strpos($geminiKey, 'YOUR_') !== false) {
    // OPTION: Uncomment and paste your real API key:
    // $geminiKey = 'AIzaSy_YOUR_ACTUAL_KEY_HERE';
```

3. Раскомментируйте строку с `$geminiKey =` и вставьте ваш ключ:
```php
// OPTION: Uncomment and paste your real API key:
$geminiKey = 'AIzaSy...ВАШ_РЕАЛЬНЫЙ_КЛЮЧ...';
```

4. Сохраните файл

### Шаг 3: Проверить снова
Запустите диагностику еще раз:
```bash
php /workspaces/intebwio-/public_html/diagnose-api.php
```

Должны увидеть:
```
✓ API Key loaded successfully
✓ Gemini API responded successfully!
=== ✓ All tests passed! ===
```

### Шаг 4: Проверить логи

Если все еще получаете ошибку, проверьте логи:

**Создайте лог директорию:**
```bash
mkdir -p /workspaces/intebwio-/public_html/logs
chmod 777 /workspaces/intebwio-/public_html/logs
```

**Посмотрите логи:**
```bash
tail -50 /workspaces/intebwio-/public_html/logs/intebwio.log
# Или в реальном времени:
tail -f /workspaces/intebwio-/public_html/logs/intebwio.log
```

## 🧐 Что означают разные ошибки

### ❌ "HTTP 403" с "leaked"
```
API key has been reported as leaked and is disabled!
```
**Решение:** Получите новый ключ на https://aistudio.google.com/apikey

### ❌ "HTTP 429"
```
Rate Limited - Too many requests
```
**Решение:** Подождите минуту и попробуйте снова. Или проверьте квоту на https://console.cloud.google.com

### ❌ "HTTP 500" или "HTTP 503"
```
Server Error - Google API may be down
```
**Решение:** Это на стороне Google. Попробуйте позже.

### ❌ "timed out"
```
cURL Error: Operation timed out
```
**Решение:** 
- Проверьте интернет соединение
- Убедитесь что API ключ не заблокирован
- Попробуйте простой запрос (1-2 слова)

## 🔧 Важные настройки

В `/public_html/includes/config.php`:
- **max_execution_time = 600s** (10 минут) - достаточно для генерации
- **default_socket_timeout = 180s** (3 минуты) - для API вызовов
- **Gemini API timeout = 300s** (5 минут) - для обработки

В `/public_html/index.html`:
- **fetch timeout = 120s** (2 минуты) - для фронтенда

## 📊 Типичное время выполнения

```
0s - 5s:    Проверка кэша в БД
5s - 20s:   Подготовка запроса к Gemini API  
20s - 50s:  Обработка запроса Gemini (может быть долго!)
50s - 60s:  Сохранение в БД и ответ клиенту
```

**Если видите 60s+ - это нормально!** Gemini иногда медленнее обрабатывает сложные запросы.

## 🚀 Оптимизация

Чтобы возращение было быстрее:

1. **Используйте простые запросы:**
   - ✓ "Python" 
   - ✓ "AI"
   - ✗ "What is the future of artificial intelligence in healthcare"

2. **Кэш работает автоматически:**
   - Первый запрос: долго
   - Второй такой же запрос: быстро (из БД)

3. **Проверьте API квоту:**
   - https://console.cloud.google.com/apis/dashboard
   - Посмотрите Gemini API usage

## 📞 Если ничего не помогло

1. Сделайте скриншот полного вывода `diagnose-api.php`
2. Вывод `tail -50 /workspaces/intebwio-/public_html/logs/intebwio.log`
3. Проверьте консоль браузера (F12 > Console)

И поделитесь информацией для дальнейшей диагностики.
