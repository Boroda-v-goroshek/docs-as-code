# Docs as Code

Практическая работа по настройке среды Docs as Code.

Автор: Шестаков Никита

## Локальный запуск

```bash
uv venv
uv pip install mkdocs pymdown-extensions
.venv/bin/mkdocs serve
```

Если `.venv` уже есть, достаточно последней команды. Сайт откроется на http://127.0.0.1:8000.

Чтобы проверить API, вставьте содержимое `docs/api/openapi.yaml` в [Swagger Editor](https://editor.swagger.io/).
