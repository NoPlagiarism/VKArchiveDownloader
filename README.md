# VKArchiveDownloader

Утилита для скачивания данных по ссылкам, которые можно получить из [архива](https://vk.com/faq18145) аккаунта [VKontakte](https://vk.com/feed).

- [VKArchiveDownloader](#vkarchivedownloader)
  - [1. Описание работы](#1-описание-работы)
  - [2. Результат работы](#2-результат-работы)
    - [2.1 JSON файл с ссылками](#21-json-файл-с-ссылками)
    - [2.2 Папки с данными](#22-папки-с-данными)
    - [2.3 Логи работы утилиты](#23-логи-работы-утилиты)
  - [3. Использование утилиты](#3-использование-утилиты)
    - [3.1 Использование `Сookie` файлов браузера](#31-использование-сookie-файлов-браузера)
    - [3.2 Запуск утилиты, используя исходный код](#32-запуск-утилиты-используя-исходный-код)
  - [4. Вопросы и ответы](#4-вопросы-и-ответы)
    - [4.1 Почему нет поддержки скачивания видео?](#41-почему-нет-поддержки-скачивания-видео)
    - [4.2 Почему нет поддержки скачивания понравившихся фото?](#42-почему-нет-поддержки-скачивания-понравившихся-фото)

## 1. Описание работы

С помощью данной утилиты возможно скачать данные по ссылкам, которые, так или иначе разбросаны по архиву [VKontakte](https://vk.com/feed).

Информация о поддерживаемых данных, которые можно скачать:
| Тип данных   |   Поддержка   | Примечание                                                                                    | Папка хранения      |
| ------------ | :-----------: | --------------------------------------------------------------------------------------------- | ------------------- |
| `Сообщения`  |  **Полная**   | Полная поддержка **[*]**, [кроме видео](#41-почему-нет-поддержки-скачивания-видео)            | `./output/messages` |
| `Стена`      | **Нет нужды** | Все фото со стены скачиваются из `photos` (`Фотографии`)                                      | `./output/photos`   |
| `👍 -> Фото`  | **Частичная** | [Только список ссылок в `JSON` файле](#42-почему-нет-поддержки-скачивания-понравившихся-фото) | `-`                 |
| `Фотографии` |  **Полная**   | Полная поддержка                                                                              | `./output/photos`   |
| `Файлы`      |    **Нет**    | [Необходим парсер данных](https://github.com/DvaMishkiLapa/VKArchiveDownloader/issues/14)     | `-`                 |
| `Музыка`     |    **Нет**    | Архив хранит в себе только название песен, но не ссылки на них                                | `-`                 |

**[*]** - [Если включен доступ к `Сookie` файлам.](#31-использование-сookie-файлов-браузера)

## 2. Результат работы

Результат работы утилиты будет находится в директории `output`.

### 2.1 JSON файл с ссылками

В папке `output`, после работы утилиты, будет находится файл `links_info.json`. Его содержание зависит от того, какие данные были запрошены в архиве.

Пример `links_info.json`, где были запрошены все поддерживаемые типы данных:

```json
{
    "messages": {
        "id диалога/беседы": {
            "name": "Имя диалога/беседы",
            "dialog_link": "Ссылка на диалог/беседу",
            "Тип данных": ["Ссылки, связные с типом данных"]
        }
    },
    "photos": {
        "photo-albums": {
            "Тип данных": ["Ссылки, связные с типом данных"]
        },
        "photo-comments": {
            "Тип данных": ["Ссылки, связные с типом данных"]
        }
    },
    "likes_photo": {
        "not_parse": ["Ссылки, связные с типом данных"]
    }

}
```

Для каждой папки из архива, в которой происходит поиск, все ссылки отсортированы по типу контента или их поведения.

Возможные типы разделения ссылок:

- Разделение на основе информации о том, на что ссылается ссылка. Информация содержится в заголовке ответного запроса `content-type`, о котором [подробнее можно прочитать тут](https://developer.mozilla.org/ru/docs/Web/HTTP/Basics_of_HTTP/MIME_types). Чаще всего, встречаются следующие типы:
  - `image/jpeg`: изображения в формате `jpeg`;
  - `image/png`: изображения в формате `png`;
  - `image/gif`: анимационные изображения в формате `gif`;
  - `video/mp4`: видеофайлы в формате `mp4`;
  - `audio/ogg`: аудиофайлы в формате `ogg`. Обычно, голосовые сообщения;
  - `application/octet-stream`: базовый тип бинарных данных. Может быть чем угодно;
  - `application/pdf`: файлы `PDF`;
  - `application/msword`: файлы Microsoft Word, а именно, файлы `doc`;
  - `application/vnd.openxmlformats`: файлы Microsoft Word, а именно, файлы `docx`;
  - `application/zip`: `ZIP` архивы;
  - `application/x-rar-compressed`: `RAR` архивы;
  - `text/plain`: текстовые файлы `txt`;
- `vk_contact`: ссылки на профили людей или сообществ [VKontakte](https://vk.com/feed);
- `vk_video`: ссылка на видео [VKontakte](https://vk.com/feed);
- `vk_story`': ссылка на истории [VKontakte](https://vk.com/feed);
- `telegram_contact`: ссылки на профили [мессенджера Telegram](https://telegram.org/);
- `github_link`: ссылки, связанные с [GitHub](https://github.com/);
- `aliexpress_link`: ссылки, связанные с [AliExpress](https://aliexpress.ru/);
- `pastebin_link`: ссылки, связанные с [Pastebin](https://pastebin.com/);
- `gdrive_link`: ссылки, связанные с [Google Drive](https://drive.google.com/);
- `google_link`: ссылки, связанные с [Google.com](https://www.google.com/);
- `wikipedia_link`: ссылки, связанные с [Wikipedia](https://www.wikipedia.org/);
- `🍓`: ссылки, связанные с [PornHub](https://www.pornhub.com/);
- `dns_shop_link`: ссылки, связанные с магазином [DNS](https://www.dns-shop.ru/);
- `error`: ссылки [VKontakte](https://vk.com/feed), переход по котором приводит к ошибке. Например, ошибке отсутствия файла на серверах;
- `not_parse`: ссылки, парсинг которых был вообще неудачен или пропущен.

### 2.2 Папки с данными

В папке `output`, после работы утилиты, будут находится папки с данными, которые удалось получить.

Возможные папки при условии, если присутствуют исходные данные в архиве, можно просмотреть в [таблице поддерживаемых типов данных](#1-описание-работы).

### 2.3 Логи работы утилиты

В процессе работы утилиты, весь процесс работы логируется и хранится в папке `logs`.

## 3. Использование утилиты

Перед использованием утилиты необходимо запросить [данные архива на официальной странице VKontakte](https://vk.com/data_protection?section=rules&scroll_to_archive=1).

[VKontakte](https://vk.com/feed) дает возможность архивировать разные данные, в зависимости от пожеланий и терпения. Чем больше данных выбрано, тем больше времени будет формироваться архив.
Утилита сможет забрать любые данные, которые найдет, вне зависимости от выбора.

![Диалог выбора данных для архивирования](./readme_img/vk_archive.png)

### 3.1 Использование `Сookie` файлов браузера

Утилита поддерживает использование `Сookie` файлов некоторых браузеров. Это может понадобится для скачивания контента, для которого необходима авторизация на сайте [VKontakte](https://vk.com/feed).

Пример таких данных (сложно перечислить их всех, проще сказать, что все, кроме сжатых фото и голосовых сообщений):

- Бинарные данные (`.doc`, `.docx`, `.pdf`, etc);
- `ZIP` архивы;
- Текстовые файлы (`.txt`, `.ini`, `.py`, etc);
- Фото, которые были отправлены документом, чтобы избежать сжатия;
- etc.

Поддерживается множество популярных браузеров и ОС. Их список можно найти на [странице используемого для этого модуля `browser_cookie3`](https://github.com/borisbabic/browser_cookie3#testing-dates--ddmmyy).

Для того, что утилита могла использовать такие `Сookie` файлы, достаточно авторизоваться в учетной записи [VKontakte](https://vk.com/feed), для которой был сформирован архив.

> :warning: **`Сookie` файлы аккаунта будут использованы исключительно для доступа к ресурсам [VKontakte](https://vk.com/feed) и не будут переданы на сторонние ресурсы.**

*В будущем будет введен файл конфигурации для отключения использования `Сookie` файлов, если это необходимо.*

### 3.2 Запуск утилиты, используя исходный код

Для запуска утилиты из исходного кода в системе должен быть установлен [Python 3.10.5 или выше](https://www.python.org/).

Необходимо скачать или клонировать репозиторий в удобное для работы место.

Клонирование репозитория:

```bash
git clone https://github.com/DvaMishkiLapa/VKArchiveDownloader.git
```

В клонированную или скачанную директорию необходимо поместить директорию архива [VKontakte](https://vk.com/feed) `Archive`.

После клонирования или скачивания репозитория и перемещения папки `Archive`, необходимо в директории репозитория развернуть виртуальное окружение `venv`.

Для ОС Windows:

```bash
py -m venv .venv
```

```bash
.\.venv\Scripts\activate
```

Для ОС Linux:

```bash
python3 -m venv .venv
```

```bash
source ./.venv/bin/activate
```

После разворачивания виртуального окружения необходимо установить зависимости:

```bash
pip install -r requirements.txt
```

После всех шагов необходимо запустить утилиту и ждать ее завершения:

```bash
python main.py
```

## 4. Вопросы и ответы

### 4.1 Почему нет поддержки скачивания видео?

Есть несколько причин, по которым это не реализовано:

1. **Странное поведения доступа к ссылке на само видео.** Не ясно почему, но я не всегда могу получить ссылку на исходник видео в максимальном качестве.
2. **Скачивание займет много времени и займет большое пространство на постоянном носителе.** Время - не самый критичный параметр. Но размер самого видео - уже более весомый аргумент, учитывая, что не все видео, в итоге, будут нужны после скачивания.

Как альтернативу данному вопросу, после работы утилиты, в [JSON файле с ссылками](#21-json-файл-с-ссылками) будет отдельная категория `vk_video` со всеми ссылками на видео [VKontakte](https://vk.com/feed).

### 4.2 Почему нет поддержки скачивания понравившихся фото?

[VKontakte](https://vk.com/feed) отдает по ссылкам, которые указаны в архиве, не совсем прямые ссылки на фото. Это ссылка на фото в сообществе, которая динамически подгружается через `JavaScript`.
При попытке получить от туда изображение, я всегда получаю ошибку [429 Too Many Requests](https://developer.mozilla.org/ru/docs/Web/HTTP/Status/429). Обойти это так и не удалось.

[Связанный с этой проблемой вопрос в GitHub Issues.](https://github.com/DvaMishkiLapa/VKArchiveDownloader/issues/11)

Пока что остановлюсь на том, что бы ссылки на понравившихся фото собирались в [файл JSON](#21-json-файл-с-ссылками).
