# Портфолио тестовых заданий | LD LATTE

Решение трёх тестовых заданий на позицию специалиста по автоматизации в компанию **LD LATTE**.  
В проектах реализованы: динамический Telegram-бот на n8n Cloud, архитектурный план интеграции с Instagram Graph API v22.0 и веб-приложение на React/Vite с изоляцией данных через Supabase Row Level Security.

---

## Сводная таблица решений

| Задача | Что сделано | Ключевые решения | Ссылки и материалы |
| :--- | :--- | :--- | :--- |
| **1. Telegram-бот с динамическим меню** | Telegram-бот на n8n Cloud с формированием inline-кнопок из Google Таблицы | • Динамическая сборка `reply_markup` в Code-ноде без правок воркфлоу при изменении услуг<br>• Проверка актуальности услуги при клике<br>• Снятие часиков через `answerCallbackQuery`<br>• Обработка пустого списка | • [@wm9k_servicedemo_bot](https://t.me/wm9k_servicedemo_bot)<br>• [README задачи](1-telegram-menu-bot/README.md)<br>• [Google Таблица](https://docs.google.com/spreadsheets/d/1cOWvfi8D-Kyd-eLu8oHBrlbB7rGw_leULPt8JSDrCHE/edit?usp=sharing)<br>• [workflow.json](1-telegram-menu-bot/workflow.json) |
| **2. План подключения Instagram Graph API** | Комплексный план сбора статистики для N аккаунтов | • Аутентификация через System User Token (бессрочный, без привязки к сотруднику)<br>• Учёт Meta API v20–v22 (переход с `impressions` на `views`)<br>• Пошаговое разделение зон ответственности<br>• Rate-limits и обработка ошибок | • [README с планом](2-instagram-api-plan/README.md)<br>• [Архитектурная схема](2-instagram-api-plan/draw.png) |
| **3. Веб-приложение «Заявки на автоматизацию»** | SPA на React 19 + TypeScript + Supabase + GitHub Pages | • Строгий RLS в PostgreSQL (SELECT/INSERT/UPDATE/DELETE только по `auth.uid()`)<br>• Идемпотентность создания (UUID на клиенте, защита от дублей)<br>• Debounce поиска 300 мс и защита от race conditions<br>• Быстрая смена статуса из карточки<br>• Полный адаптив (320–1920 px) | • [Онлайн-демо](https://1wm9k.github.io/automation-requests/)<br>• [Репозиторий проекта](https://github.com/1wm9k/automation-requests)<br>• [Карточка задачи](3-automation-requests/README.md) |

---

## С чего начать проверку

1. **Задача 3 (Веб-сервис):**
   - Откройте живое [Онлайн-демо](https://1wm9k.github.io/automation-requests/).
   - Можно войти под тестовым аккаунтом: `testuser@proton.me` / `testtest` (или зарегистрировать свой).
   - Попробуйте создать заявку, сменить статус прямо из карточки списка, отфильтровать по названию и статусу.
   - Исходный код и тесты RLS доступны в репозитории [1wm9k/automation-requests](https://github.com/1wm9k/automation-requests).
2. **Задача 1 (Telegram-бот):**
   - Ознакомьтесь с логикой в [1-telegram-menu-bot/README.md](1-telegram-menu-bot/README.md).
   - Посмотрите тестовую [Google Таблицу](https://docs.google.com/spreadsheets/d/1cOWvfi8D-Kyd-eLu8oHBrlbB7rGw_leULPt8JSDrCHE/edit?usp=sharing) и галерею работы бота на 2, 5 и 9 активных услугах.
   - Файл [workflow.json](1-telegram-menu-bot/workflow.json) готов к импорту в n8n (секреты очищены).
3. **Задача 2 (Instagram API):**
   - Откройте [2-instagram-api-plan/README.md](2-instagram-api-plan/README.md) и схему потоков данных [draw.png](2-instagram-api-plan/draw.png).

---

## Ограничения и что улучшил бы

1. **Telegram-бот (Задача 1):**
   - *Текущее состояние:* чтение Google Sheets выполняется на каждый запрос (`/start` и каждый клик), что при высокой нагрузке упирается в квоты Google API.
   - *Что улучшил бы:* заменил бы Google Sheets на PostgreSQL или добавил кэширование списка услуг в Redis с TTL (1–3 минуты), инвалидируя кэш через вебхуки таблицы.
2. **Instagram ETL (Задача 2):**
   - *Текущее состояние:* описан поллинг через планировщик (Cron / Airflow).
   - *Что улучшил бы:* для сбора Stories (исчезающих через 24 часа) подключил бы Meta Webhooks на события публикаций вместо периодического опроса.
3. **Заявки на автоматизацию (Задача 3):**
   - *Текущее состояние:* пагинация реализована ограничением в 50 последних заявок; удаление заявки доступно через политику RLS, но кнопка не вынесена в UI, чтобы избежать случайных удалений без подтверждения.
   - *Что улучшил бы:* добавил бы серверную курсорную пагинацию («Загрузить ещё»), восстановление пароля через Email OTP и soft-delete (архивацию заявок).
