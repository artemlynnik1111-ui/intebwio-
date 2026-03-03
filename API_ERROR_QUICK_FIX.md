# 🔧 Исправление "API error" после 40 секунд - Быстрый старт

## 📋 Что я сделал для исправления

### 1. ✅ Улучшены таймауты JavaScript (index.html)
- Добавлен 2-минутный таймаут вместо стандартного
- Лучшая обработка сетевых ошибок
- Понятные сообщения об ошибках

### 2. ✅ Расширена диагностика (AIService.php)
- Подробное логирование каждого шага
- Специфические ошибки для разных HTTP кодов
- Советы по исправлению прямо в логах

### 3. ✅ Лучший лог в API (api/generate.php)
- Пошаговое логирование генерации
- Ловит ошибки базы данных
- Подсказки в ответе клиенту

### 4. ✅ Диагностический скрипт   
- Файл: `/public_html/diagnose-api.php`
- Проверяет API ключ и связь с Gemini

### 5. ✅ Гибкая загрузка ключа (apikeys.php)
- Поддерживает переменные окружения
- Позволяет вставить ключ директно в файл
- Утилита для отладки

---

## 🚀 Что делать сейчас

### Шаг 1️⃣: Запустить диагностику
```bash
php /public_html/diagnose-api.php
```

**Если видите:** `✓ All tests passed!`
→ Значит API работает, проблема в другом. Перейти к Шагу 3.

**Если видите:** `Your_GEMINI_API_KEY_HERE` или ошибка API
→ Перейти к Шагу 2.

### Шаг 2️⃣: Добавить API ключ

1. Откройте: `/public_html/includes/apikeys.php`

2. Найдите ~41-47 строка и измените:
```php
// ДО (не работает):
$geminiKey = getenv('GEMINI_API_KEY');
if (!$geminiKey || strpos($geminiKey, 'YOUR_') !== false) {
    // $geminiKey = 'AIzaSy_YOUR_ACTUAL_KEY_HERE';
    if (!$geminiKey) {
        $geminiKey = 'YOUR_GEMINI_API_KEY_HERE';
    }
}

// ПОСЛЕ (работает):
$geminiKey = getenv('GEMINI_API_KEY');
if (!$geminiKey || strpos($geminiKey, 'YOUR_') !== false) {
    // Раскомментируйте и вставьте ВАШ реальный ключ:
    $geminiKey = 'AIzaSy_ВСТАВЬТЕ_ВАШ_КЛЮЧ_СЮДА';
    
    if (!$geminiKey) {
        $geminiKey = 'YOUR_GEMINI_API_KEY_HERE';
    }
}
```

3. Сохраните файл (Ctrl+S)

### Шаг 3️⃣: Тестировать снова
```bash
php /public_html/diagnose-api.php
```

Должно быть: `✓ All tests passed!`

### Шаг 4️⃣: Тестировать в браузере

1. Откройте `index.html`
2. Введите простой запрос: "Python" или "AI"  
3. Смотрите сообщения статуса

#### Что смотреть в браузере (F12 > Console)
```
API keys loaded from server    ← Хорошо
Loading...                      ← Работает
✓ Success                       ← Готово!
```

#### Что смотреть при ошибке
```
❌ Error message               ← Если ошибка
```

---

## 📊 Где смотреть логи

### На сервере:
```bash
# Последние 50 строк логов
tail -50 /public_html/logs/intebwio.log

# В реальном времени (Ctrl+C для выхода)
tail -f /public_html/logs/intebwio.log

# Поиск ошибок
grep "ERROR\|❌" /public_html/logs/intebwio.log | tail -20
```

### В браузере:
- Нажмите F12
- Вкладка "Console"
- Вкладка "Network"

---

## 🐛 Возможные проблемы и решения

### Проблема: "HTTP 403" с "leaked"
```
API key has been reported as leaked and is disabled!
```
✅ **Решение:** Получите новый ключ
https://aistudio.google.com/apikey

### Проблема: "HTTP 429" Rate Limited
```
Too many requests
```
✅ **Решение:** 
- Подождите 1-2 минуты
- Проверьте квоту: https://console.cloud.google.com

### Проблема: "timed out"
```
cURL Error: Operation timed out
```
✅ **Решение:**
- Проверьте интернет
- Убедитесь API ключ не заблокирован
- Попробуйте простой запрос (1-2 слова)

### Проблема: "Database Error"
```
Failed to store page in database
```
✅ **Решение:**
- Проверьте credentials в `/public_html/includes/config.php`
- БД должна быть запущена и доступна

### Проблема: 40+ секунд, потом ошибка
Это нормально! Gemini иногда медленно обрабатывает:
- ✓ Простые запросы (1-2 слова): 10-20s
- ✓ Средние запросы: 20-40s
- ✓ Сложные запросы: 40-60s

Если больше 60s - может быть таймаут. Попробуйте проще.

---

## 📝 Контрольный список

- [ ] Запустил `diagnose-api.php` ✓
- [ ] Добавил реальный API ключ в `apikeys.php`
- [ ] Перезагрузил браузер (Ctrl+Shift+R)
- [ ] Тестировал простой запрос в index.html
- [ ] Проверил логи на ошибки

---

## 🆘 Если все еще не работает

Сделайте и поделитесь:

1. **Вывод диагностики:**
```bash
php /public_html/diagnose-api.php 2>&1 | head -50
```

2. **Последние логи:**
```bash
tail -30 /public_html/logs/intebwio.log
```

3. **Ошибка в браузере (F12 > Console)**

4. **Какой конкретно запрос вы делали?** ("Python", "AI", что-то другое?)

Поделитесь этой информацией для дальнейшей диагностики! 🔍
