# skill-cycling

Плагин для [Agloom](https://docs.agloom.sh) — система **spec-driven development** с оркестрированными циклами агентов. Транспилируется в `CLAUDE.md`, `AGENTS.md`, `.opencode/` и другие форматы AI coding-ассистентов.

## Что внутри

Плагин поставляет три готовых skill-оркестратора и набор субагентов для фаз **specify → test → implement** с quality gates между фазами:

| Skill            | Назначение                                                           |
| ---------------- | -------------------------------------------------------------------- |
| `spec-cycle`     | Полный цикл spec-driven development (6 агентов: writer/reviewer × 3) |
| `research-cycle` | Цикл проведения исследований с ревью                                 |
| `cycle-creator`  | Мета-skill для проектирования новых агентных циклов                  |

Каждая фаза выполняется **отдельным агентом с изолированным контекстом**, а reviewer может вернуть работу на любую более раннюю фазу через `returnTo`. Архитектура и протоколы описаны в [`AGLOOM.md`](AGLOOM.md) и [`docs/cycling/`](docs/cycling/).

## Установка

Плагин подключается через `.agloom/config.yml` в consumer-проекте:

```yaml
plugins:
  - git@github.com:cusxy/skill-cycling
```

Для фиксации версии используй `#ref`:

```yaml
plugins:
  - git@github.com:cusxy/skill-cycling#v0.1.0
```

После подключения запусти транспиляцию в consumer-проекте:

```bash
agloom transpile
```

Agloom склонирует плагин в локальный кеш, смерджит его артефакты с проектными и сгенерирует файлы для выбранных адаптеров (Claude Code, OpenCode, Codex и т. д.).

## Использование

В consumer-проекте, где установлен плагин, оркестраторы доступны как skill'ы соответствующего AI-ассистента. Пример с Claude Code:

```text
/spec-cycle реализовать функциональность X в модуле Y
```

Skill последовательно запустит `spec-writer → spec-reviewer → test-deriver → test-reviewer → spec-implementer → impl-reviewer`, отправляя работу на возврат при замечаниях.

## Разработка

Этот репозиторий содержит **только markdown-инструкции и YAML-схемы** — собственного build/test-пайплайна нет. Валидация выполняется хост-инструментом:

```bash
agloom format --check   # проверка форматирования и линтинг
agloom format           # авто-исправление
```

Канонический контекст для AI-агентов, работающих над самим плагином, — [`AGLOOM.md`](AGLOOM.md). Критерии качества инструкций и антипаттерны — [`docs/agents-md-quality-criteria.md`](docs/agents-md-quality-criteria.md).

## Лицензия

Apache License 2.0 — см. [`LICENSE`](LICENSE).
