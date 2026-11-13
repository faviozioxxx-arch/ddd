# PyBridge

[![Build](https://github.com/YOUR_NAME/PyBridge/actions/workflows/build.yml/badge.svg)](https://github.com/YOUR_NAME/PyBridge/actions/workflows/build.yml)
![Paper](https://img.shields.io/badge/Paper-1.21.11-blue)
![Java](https://img.shields.io/badge/Java-21-orange)
![License](https://img.shields.io/badge/license-MIT-green)

Плагин для **Paper 1.21.11**, который позволяет управлять сервером из **Python**: вы пишете команды в Python-скрипте, а они выполняются на сервере.

```python
from pybridge import Minecraft

mc = Minecraft(token="ВАШ_ТОКЕН")
mc.say("Привет из Python!")
mc.give("Steve", "diamond", 64)
print(mc.cmd("list"))   # любая команда сервера
```

## Быстрый старт (готовая сборка)

1. Откройте раздел **Releases** и скачайте `PyBridge.jar` и `pybridge.py`.
   Если релизов ещё нет, jar можно взять из **Actions → Build → последний запуск → Artifacts**.
2. Положите `PyBridge.jar` в папку `plugins` сервера Paper 1.21.11 и запустите сервер.
3. Откройте `plugins/PyBridge/config.yml` и скопируйте `token` (он создаётся автоматически).
4. Положите `pybridge.py` рядом со своим скриптом и подключайтесь (пример выше).

Требуется Paper 1.21.11 и Java 21. Для клиента нужен Python 3.8+, сторонние библиотеки не нужны.

## Как собрать самому

### Вариант 1. На GitHub (ничего не нужно устанавливать)

1. Загрузите проект в свой репозиторий на GitHub (см. ниже).
2. Откройте вкладку **Actions → Build → Run workflow**, либо просто сделайте `git push`.
3. После завершения сборки откройте запуск и скачайте jar из блока **Artifacts** (`PyBridge-plugin`).

Готовый релиз с файлами для скачивания:

```
git tag v1.0.0
git push origin v1.0.0
```

Через 1–2 минуты в **Releases** появятся `PyBridge.jar` и `pybridge.py`.

### Вариант 2. На своём компьютере

Нужны **JDK 21** и **Maven 3.9+**.

```
mvn clean package
```

Готовый файл: `target/PyBridge-1.0.0.jar`. Версию можно задать так: `mvn clean package -Drevision=1.2.3`.

### Как залить проект на GitHub

1. Создайте на GitHub пустой репозиторий `PyBridge` (без README и лицензии).
2. В папке проекта выполните:
   ```
   git add -A
   git commit -m "Initial commit"
   git remote add origin https://github.com/ВАШ_НИК/PyBridge.git
   git push -u origin main
   ```
3. Замените `YOUR_NAME` в `README.md` и `src/main/resources/plugin.yml` на свой ник.

### Другая версия Paper

Версия API задана в `pom.xml` в свойстве `paper.version` (сейчас `1.21.11-R0.1-SNAPSHOT`). Можно собрать и под другую:

```
mvn clean package -Dpaper.version=1.21.4-R0.1-SNAPSHOT
```

## Использование

```python
from pybridge import Minecraft

with Minecraft(token="ТОКЕН", host="127.0.0.1", port=25580) as mc:
    mc.tp("Steve", 0, 100, 0)
    mc.time_set("day")
    mc.weather("clear")
    print(mc.cmd("whitelist list"))
```

Интерактивная консоль (команды без `/`):

```
python pybridge.py ТОКЕН
mc> list
mc> gamemode creative Steve
```

Готовые методы: `say`, `tell`, `give`, `tp`, `gamemode`, `time_set`, `weather`, `setblock`, `kick`, `op`, `deop`, `players`. Всё остальное делается через `cmd("...")`.

## Настройка (`plugins/PyBridge/config.yml`)

| Параметр | По умолчанию | Описание |
|---|---|---|
| `bind` | `127.0.0.1` | Адрес прослушивания. `0.0.0.0` открывает доступ извне |
| `port` | `25580` | Порт |
| `token` | генерируется | Пароль для подключения |
| `timeout-seconds` | `10` | Сколько ждать выполнения одной команды |

## Безопасность

Подключившийся клиент выполняет команды **от имени консоли**, то есть получает полный контроль над сервером.

- Оставляйте `bind: 127.0.0.1`, если Python запущен на той же машине.
- Если открываете порт наружу, держите токен в секрете и ограничьте порт файрволом.
- Не публикуйте `config.yml` с токеном.

## Протокол

Текстовый, UTF-8, одна строка = одно сообщение:

```
клиент -> AUTH <token>        сервер -> OK | ERR
клиент -> <команда>           сервер -> OK <base64 вывода> | ERR <base64 вывода>
```

## Структура проекта

```
pom.xml                          сборка Maven
src/main/java/dev/pybridge/      код плагина
src/main/resources/              plugin.yml и config.yml
python/pybridge/                 Python-клиент
examples/example.py              пример
.github/workflows/               автосборка и релизы
```

## Лицензия

[MIT](LICENSE)
