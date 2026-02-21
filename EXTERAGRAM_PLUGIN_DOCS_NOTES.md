# exteraGram Plugins: изучение документации (EXT-8)

Источник: `https://plugins.exteragram.app/docs/*` (официальная документация exteraGram Plugins).

## Что изучено

Разделы, которые были просмотрены:
- Setup
- First Plugin
- Plugin Class
- Plugin Settings
- Xposed Method Hooking
- Android Utilities
- Client Utilities
- Markdown Parser
- Hook Utilities (Reflection)
- File Utilities
- Alert Dialog Builder
- Bulletin Helper
- Available Libraries
- Common Telegram Classes

## Ключевые выводы по платформе плагинов

1. **Базовая структура плагина**
   - Плагин — это файл с расширением `.plugin`.
   - Обязателен класс, наследуемый от `BasePlugin`.
   - Метаданные задаются строковыми переменными (`__id__`, `__name__`, `__description__`, `__author__`, `__version__`, `__icon__`, `__min_version__`).

2. **Требования к метаданным и идентификатору**
   - В `Plugin Class` явно указано, что мета-поля должны быть **plain strings** (без форматирования/конкатенации), так как движок читает их через AST.
   - Обязательные поля: как минимум `__id__` и `__name__`.
   - `__id__` имеет ограничения по длине/формату: 2–32 символа, начинается с буквы, содержит латиницу/цифры/`-`/`_`.

3. **Настройки плагина**
   - Экран настроек создаётся через `create_settings`.
   - Используются компоненты из `ui.settings` (например: `Header`, `Input`, `Divider`, `Switch`, `Selector`, `Text`, `EditText`).

4. **Hooking (Xposed)**
   - Документация выделяет два основных подхода:
     - `MethodHook` — код до/после вызова оригинального метода.
     - `MethodReplacement` — полная замена оригинальной реализации.
   - Ключевой объект — `param` (`XC_MethodHook.MethodHookParam`), через который доступны `thisObject`, `args`, `getResult()`, `method`.

5. **Служебные модули и утилиты**
   - `android_utils`: работа с UI-потоком, Runnable-прокси (`R`), логирование.
   - `client_utils`: фоновые очереди/таски, клиентские helper-функции.
   - `markdown_utils`: преобразование markdown в текст + Telegram entities (`TLRPC.MessageEntity`).
   - `hook_utils`: reflection-инструменты (`find_class` и т.п.), с предупреждением о хрупкости.
   - `file_utils`: операции с файлами/директориями для плагина.
   - UI helpers: `AlertDialogBuilder` и `BulletinHelper`.

6. **Ограничения рантайма**
   - В `Available Libraries` указана среда Python **3.11**.
   - Приведён список предустановленных библиотек (в т.ч. `requests`, `beautifulsoup4`, `lxml`, `pillow`, `PyYAML`, `debugpy`, `packaging`).
   - Документация отдельно акцентирует, что произвольная установка pip-пакетов в рантайме не поддерживается.

7. **Ориентиры по исходникам Telegram**
   - Раздел `Common Telegram Classes` даёт список ключевых классов/путей (например: `LaunchActivity`, `ProfileActivity`, `ChatActivity`, `MessageObject`, `ChatMessageCell`, `AndroidUtilities`) как точки входа для анализа и hook-ов.

## Практический чеклист для разработки ExteraRSS

- Проверять, что мета-поля оформлены строковыми литералами без вычислений.
- Держать `__id__` в валидном формате (2–32, латиница/цифры/`-`/`_`).
- Любые долгие сетевые операции выносить в фон (`client_utils` очереди), UI-обновления выполнять на UI thread.
- Для форматированного вывода сообщений использовать `markdown_utils` + entities, а не ручные regex-конвертации.
- При reflection/hook логировать ошибки и иметь fallback, так как поведение может ломаться после обновлений клиента.
- Использовать только доступные runtime-библиотеки; внешние зависимости проектировать с учётом ограничений среды.
