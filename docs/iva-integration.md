# Интеграция с Iva

Документ фиксирует границы интеграции и точки входа в исходниках Iva. Все пути ниже указаны относительно корня репозитория Iva.

Проверено на Iva `0.3.18`, commit [`806ac535d99e`](https://github.com/smixs/iva/commit/806ac535d99e42858ea08ae84f6032c577115aa8).

## Граница проекта

`codebase-mentor` должен оставаться скиллом, а не форком или модулем ядра Iva.

- Публичный пакет устанавливается в `data/custom/agent/skills/codebase-mentor/`.
- Код скилла не изменяет файлы `agent/` в Iva.
- Скрипты скилла не импортируют внутренние модули `agent/lib/*` и `scripts/lib/*`.
- Анализ выполняется через публичный CLI `ast-index`.
- Путь к репозиторию, vault и состояние передаются через настройки, а не зашиваются в код.
- Результат скилла — Markdown. Выбор Telegram-транспорта остаётся за Iva.
- Расписание принадлежит Iva. Скилл предоставляет режим запуска, но не собственный cron.

Так основная логика остаётся переносимой, а адаптер Iva — тонким.

## Карта исходников Iva

### Расширения

| Путь | Зачем читать |
|---|---|
| [`docs/extending.md`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/docs/extending.md) | Официальный способ добавления скиллов, инструментов и инструкций. |
| [`scripts/lib/custom-layer.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/scripts/lib/custom-layer.ts) | Какие пользовательские каталоги входят в custom-layer. |
| [`scripts/lib/authored-paths.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/scripts/lib/authored-paths.ts) | Точная проверка разрешённых пользовательских путей. |
| [`scripts/lib/version-update.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/scripts/lib/version-update.ts) | Как custom-layer переносится между версиями Iva. |
| [`agent/skills/morning-digest.md`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/skills/morning-digest.md) | Минимальный пример скилла с вызовом инструмента и форматом ответа. |

### Анализ репозитория

| Путь | Зачем читать |
|---|---|
| [`agent/tools/bash.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/tools/bash.ts) | Запуск `ast-index` из скилла и ограничения shell-команд. |
| [`agent/tools/read_file.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/tools/read_file.ts) | Чтение найденных исходников. |
| [`agent/tools/glob.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/tools/glob.ts) | Поиск файлов, когда структурного индекса недостаточно. |
| [`agent/tools/grep.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/tools/grep.ts) | Поиск строк и комментариев, которые не видны через AST. |

Приоритет поиска: `ast-index` → чтение найденных файлов → `grep` только для строк, комментариев и отсутствующих в индексе данных.

### Память

| Путь | Зачем читать |
|---|---|
| [`docs/memory.md`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/docs/memory.md) | Общая модель памяти Iva и структура vault. |
| [`agent/tools/memory_search.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/tools/memory_search.ts) | BM25, optional hybrid-поиск и ограничение через `scope`. |
| [`agent/tools/write_card.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/tools/write_card.ts) | Схема стандартных карточек и поля `domain`, `tags`, `related`. |
| [`agent/tools/write_file.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/tools/write_file.ts) | Запись файлов вне стандартных карточек. |
| [`agent/lib/card-index.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/lib/card-index.ts) | Какие поля карточек участвуют в поиске и эмбеддингах. |

Память проекта следует хранить в отдельной поддиректории vault и всегда передавать её в `memory_search.scope`. Поле `domain` само по себе не обеспечивает строгую фильтрацию.

### Rich-сообщения

| Путь | Зачем читать |
|---|---|
| [`agent/skills/rich-post/SKILL.md`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/skills/rich-post/SKILL.md) | Поддерживаемая разметка и правила отправки rich message. |
| [`agent/skills/rich-post/scripts/send_rich.py`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/skills/rich-post/scripts/send_rich.py) | Готовый транспорт для плановых rich-сообщений. |
| [`agent/channels/telegram.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/channels/telegram.ts) | Отправка и fallback обычных ответов Telegram. |
| [`agent/lib/telegram-format.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/lib/telegram-format.ts) | Определение rich-разметки и преобразование Markdown. |
| [`agent/lib/outbox.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/lib/outbox.ts) | Общий маршрут rich → HTML → plain text. |

Скилл должен формировать содержимое карточки, но не дублировать этот транспорт.

### Расписание и запуск агента

| Путь | Зачем читать |
|---|---|
| [`agent/schedules/digest.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/schedules/digest.ts) | Эталон тонкого планового обработчика Iva. |
| [`agent/lib/schedule-table.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/lib/schedule-table.ts) | Расписания и работа с часовым поясом процесса. |
| [`agent/lib/schedule-runner.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/lib/schedule-runner.ts) | Блокировки, защита от повторов, таймауты и статус запуска. |
| [`scripts/daily-digest.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/scripts/daily-digest.ts) | Создание агентной сессии и запрос на загрузку скилла. |
| [`agent/lib/settings.ts`](https://github.com/smixs/iva/blob/806ac535d99e42858ea08ae84f6032c577115aa8/agent/lib/settings.ts) | Чтение пользовательских настроек во время запуска. |

В Iva `0.3.18` каталог `agent/schedules/` не входит в custom-layer. Поэтому первая версия скилла должна быть полностью работоспособна при ручном запуске и иметь стабильный контракт для вызова из расписания. Поддержку установки пользовательского расписания следует реализовывать в Iva или её официальном адаптере, не внутри ядра скилла.

## Контракт скилла

Минимальный публичный пакет:

```text
skill/
├── SKILL.md
├── references/
└── scripts/
```

`SKILL.md` выбирает режим и управляет рассуждением. `scripts/` содержит только переносимые операции: вызов `ast-index`, чтение конфигурации, идентификацию репозитория, блокировку, проверку и обновление состояния. `references/` описывает формат карты, учебной карточки и правила выбора темы.

Зависимость от Iva не должна проникать глубже входного запроса, путей из окружения и Markdown-ответа.
