# Рабочая среда

Для курса подойдёт любой из трёх вариантов:

| Вариант              | GPU                                      |
| -------------------- | ---------------------------------------- |
| **Kaggle Notebooks** | Около 30 часов в неделю бесплатно        |
| **Google Colab**     | Есть, но квоту Google не гарантирует     |
| **Локально**         | Только если у вас есть видеокарта NVIDIA |

Лабы 1–3 идут на CPU, видеокарта в них не нужна. С лабы 4 мы обучаем нейросети, поэтому желательно завести аккаунт в Kaggle или Colab.

Версия Python для курса: **3.11**. Подойдут 3.10, 3.11 и 3.12. Python 3.13 и новее мы не проверяли, часть пакетов под них может не поставиться.

## Содержание

- [Kaggle Notebooks](#kaggle-notebooks)
- [Google Colab](#google-colab)
- [Локальная установка](#локальная-установка)
- [Почему два файла с зависимостями](#почему-два-файла-с-зависимостями)
- [Проверка, что все работает](#проверка-что-все-работает)
- [Если что-то сломалось](#если-что-то-сломалось)

---

## Kaggle Notebooks

### 1. Регистрация и телефон

1. Зайдите на [kaggle.com](https://www.kaggle.com) и нажмите **Register**. Удобнее всего войти через Google-аккаунт.
2. Откройте свой профиль (аватар справа сверху) → **Settings**.
3. Найдите блок **Phone verification** и подтвердите номер телефона кодом из SMS. 
4. Если подтвердить номер не получается, пройдите верификацию по лицу по инструкции внутри Kaggle, это занимает меньше минуты.

### 2. Новый ноутбук

1. В левом меню выберите **Code** → **New Notebook**. Откроется редактор, похожий на Jupyter.
2. Переименуйте ноутбук: клик по названию сверху, например `lab01`.
3. Справа откройте панель **Session options** (если её не видно, нажмите на значок с тремя точками или на стрелку у правого края).

### 3. Интернет и GPU

В панели **Session options**:

- **Internet**: переключите в положение **On**. Переключатель неактивен, если телефон не подтверждён.
- **Accelerator**: для лаб 1–3 оставьте **None** (CPU). С лабы 4 выберите **GPU T4 x2** или **GPU P100**. Для наших задач разницы почти нет. Если одна из них занята, берите другую.

Смена ускорителя перезапускает сессию: переменные и скачанные файлы пропадут, ячейки придётся выполнить заново.

### 4. Квоты

- **GPU**: около 30 часов в неделю, квота обновляется раз в неделю. Сколько часов осталось, Kaggle показывает в панели справа в редакторе ноутбука. Часы тратятся, пока сессия с GPU запущена, даже если код не выполняется.
- **Длительность сессии**: не больше 12 часов подряд. Если долго не трогать вкладку, сессия остановится раньше.
- **Диск**: папка `/kaggle/working` сохраняется вместе с версией ноутбука (до 20 ГБ). Всё, что вы скачали в интерактивной сессии и не сохранили, пропадает после её остановки.

Как экономить GPU: пишите и отлаживайте код на CPU с маленьким кусочком данных, включайте GPU только на полное обучение и выключайте сессию кнопкой **Stop session**, когда закончили.

### 5. Данные варианта

Выполните в первой ячейке ноутбука (вместо `7` подставьте номер своего варианта):

```python
%cd /kaggle/working
!git clone --depth 1 https://github.com/ykvnkm/computer-vision-course.git
%cd computer-vision-course
!python data/get_data.py --variant 7
```

После этого данные лежат в `/kaggle/working/computer-vision-course/data/variant_07/`. Что внутри папки, описано в [data/README.md](../data/README.md).

В каждой новой сессии эту ячейку нужно выполнить заново: интерактивная сессия Kaggle не хранит файлы между запусками.

### 6. Как забрать результат

Ноутбук: меню **File** → **Download notebook** (скачается `.ipynb`). Картинки и таблицы из `/kaggle/working`: панель **Output** справа, рядом с файлом значок скачивания. Потом загрузите всё в свой репозиторий через веб-интерфейс GitHub, как описано в [git.md](git.md#загрузка-файлов-через-сайт-github).

### 7. Пакеты

В Kaggle уже стоят numpy, pandas, OpenCV, scikit-learn, torch, torchvision, timm и transformers. Если какого-то пакета нет (например, `imagehash` или `ultralytics`), поставьте его в ноутбуке:

```python
!pip install -q imagehash ultralytics
```

Ставить весь `requirements.txt` в Kaggle не нужно, а `torch` переустанавливать нельзя: вы потеряете сборку под GPU.

---

## Google Colab

### Открыть ноутбук с GitHub

Способ 1. Зайдите на [colab.research.google.com](https://colab.research.google.com), меню **File** → **Open notebook** → вкладка **GitHub**, вставьте адрес репозитория или ноутбука и выберите файл.

Способ 2. Замените в адресе ноутбука `https://github.com/` на `https://colab.research.google.com/github/`. Например, ноутбук

```
https://github.com/ykvnkm/computer-vision-course/blob/main/labs/lab01/starter.ipynb
```

откроется в Colab по адресу

```
https://colab.research.google.com/github/ykvnkm/computer-vision-course/blob/main/labs/lab01/starter.ipynb
```

### GPU

Меню **Runtime** → **Change runtime type** → **T4 GPU** → **Save**. Если Colab пишет, что GPU недоступен, вы исчерпали квоту: подождите несколько часов или переходите в Kaggle.

### Данные варианта

```python
%cd /content
!git clone --depth 1 https://github.com/ykvnkm/computer-vision-course.git
%cd computer-vision-course
!python data/get_data.py --variant 7
```

### Сохранение

Ноутбук, открытый с GitHub, Colab не сохраняет сам. Сразу после открытия выберите **File** → **Save a copy in Drive**, и дальше работайте с копией. Готовый ноутбук скачайте через **File** → **Download** → **Download .ipynb** и загрузите в свой репозиторий.

Есть пункт **File** → **Save a copy in GitHub**, он сохраняет ноутбук прямо в ваш репозиторий. Colab попросит доступ к аккаунту GitHub; если вы не уверены, что даёте, пользуйтесь загрузкой через сайт.

### Ограничения

- Файлы в `/content` пропадают, когда сессия закрывается. Данные варианта придётся качать в каждой сессии.
- Бесплатная сессия живёт до 12 часов, а при бездействии отключается быстрее (обычно через 1–1,5 часа).
- Объём GPU-квоты Google не публикует и не гарантирует. В часы пик T4 может не выдаться вовсе.

---

## Локальная установка

### 1. Python 3.11

**Windows**

1. Скачайте установщик Python 3.11 с [python.org/downloads/windows](https://www.python.org/downloads/windows/) (ищите строку *Windows installer (64-bit)* у последней версии 3.11.x).
2. На первом экране установщика поставьте галочку **Add python.exe to PATH**, затем **Install Now**.
3. Откройте новое окно PowerShell и проверьте: `py -3.11 --version`.

**macOS**

1. Скачайте *macOS 64-bit universal2 installer* для Python 3.11 с [python.org/downloads/macos](https://www.python.org/downloads/macos/) и установите.
2. Проверьте в Терминале: `python3.11 --version`.

**Linux**

Проще всего поставить Python через uv (ниже). Если хотите системный пакет: в Ubuntu 22.04 Python 3.10 уже есть и он подходит, в Ubuntu 24.04 стоит 3.12, он тоже подходит. Для виртуальных окружений в Ubuntu нужен пакет `python3-venv`: `sudo apt install python3-venv`.

**Через uv (любая система)**

[uv](https://docs.astral.sh/uv/getting-started/installation/) ставит нужную версию Python сам и работает быстрее pip. Установка:

```bash
# macOS и Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

```powershell
# Windows, PowerShell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### 2. Папка проекта

> **Windows: путь без кириллицы и пробелов.** `cv2.imread` не умеет читать файлы, в пути к которым есть русские буквы: он молча возвращает `None`, и следующая строка падает с непонятной ошибкой. Пробелы в пути ломают команды в терминале, если забыть кавычки. Папка `C:\Users\Иван\Рабочий стол\КЗ` не подойдёт. Создайте `C:\cv` и работайте в ней.

Склонируйте или скачайте сюда репозиторий курса и свой личный репозиторий (как это сделать, описано в [git.md](git.md)):

```
C:\cv\
├── computer-vision-course\   ← репозиторий курса: задания, скрипт данных
└── cv-2026-ivanov\           ← ваш репозиторий: решения
```

### 3. Виртуальное окружение и пакеты

Виртуальное окружение (venv) отделяет пакеты курса от остального Python на компьютере. Создаёте его один раз, а потом только активируете.

Через pip:

```bash
cd computer-vision-course
python -m venv .venv            # на Windows: py -3.11 -m venv .venv
```

Активация:

```bash
# Windows, PowerShell
.venv\Scripts\Activate.ps1
# Windows, cmd
.venv\Scripts\activate.bat
# macOS и Linux
source .venv/bin/activate
```

После активации в начале строки терминала появится `(.venv)`. Поставьте зависимости:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Через uv то же самое выглядит так:

```bash
cd computer-vision-course
uv venv --python 3.11
# активируйте окружение командой из блока выше
uv pip install -r requirements.txt
```

Если PowerShell пишет, что выполнение сценариев отключено, один раз выполните `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` и ответьте `Y`.

### 4. Нейросети (к лабе 4)

```bash
pip install -r requirements-dl.txt
```

На Windows и macOS pip ставит подходящую сборку torch сам. На Linux без видеокарты NVIDIA сначала поставьте CPU-сборку, иначе pip скачает больше 2 ГБ библиотек CUDA:

```bash
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
pip install -r requirements-dl.txt
```

Если видеокарта NVIDIA есть, подберите команду под свою версию CUDA на [pytorch.org/get-started](https://pytorch.org/get-started/locally/).

### 5. Jupyter или VS Code

**Jupyter Lab.** В активированном окружении выполните `jupyter lab`. Откроется браузер, слева будут файлы текущей папки.

**VS Code.** Поставьте [VS Code](https://code.visualstudio.com/) и расширения **Python** и **Jupyter** (от Microsoft). Откройте папку `C:\cv` через **File** → **Open Folder**. Откройте `.ipynb`, справа сверху нажмите **Select Kernel** → **Python Environments** и выберите окружение `.venv` из папки курса.

### 6. Данные варианта

```bash
cd computer-vision-course
python data/get_data.py --variant 7
```

Данные появятся в `computer-vision-course/data/variant_07/`. Git их не видит: папка `data/variant_*/` записана в `.gitignore`.

---

## Почему два файла с зависимостями

| Файл | Для каких лаб | Что внутри | Сколько весит |
| --- | --- | --- | --- |
| [requirements.txt](../requirements.txt) | 1–3 | numpy, pandas, matplotlib, OpenCV, Pillow, scikit-image, scikit-learn, imagehash, albumentations, Jupyter | около 600 МБ |
| [requirements-dl.txt](../requirements-dl.txt) | 4–6 | всё из базового плюс torch, torchvision, timm, ultralytics, segmentation-models-pytorch, transformers | от 1 до 4 ГБ в зависимости от системы |

Первые три лабы обходятся без нейросетей. Если бы всё лежало в одном файле, в первую неделю каждому пришлось бы качать torch на пару гигабайт, а на медленном интернете и слабом ноутбуке установка срывалась бы ещё до первой ячейки. Поэтому Правила курса ссылаются на базовый `requirements.txt`, а `requirements-dl.txt` понадобится только с недели 6.

В файлах указаны только нижние границы версий. Мы проверили, что базовый набор ставится на Python 3.11 (macOS), а полный набор разрешается без конфликтов на Python 3.10 и 3.12 для Windows, macOS и Linux.

OpenCV стоит в варианте `opencv-python-headless`: без окон и без Qt. В Jupyter, Kaggle и Colab окна `cv2.imshow` всё равно не работают, картинки мы показываем через matplotlib. Обычный `opencv-python` на серверах без экрана падает с ошибкой про `libGL`.

---

## Проверка, что все работает

Выполните эту ячейку в Kaggle, Colab или локальном Jupyter. Она печатает версии и проверяет, что OpenCV читает картинку.

```python
import sys, platform
print("Python", sys.version.split()[0], "|", platform.system())

import numpy as np, pandas as pd, matplotlib, cv2, PIL, skimage, sklearn
for name, mod in [("numpy", np), ("pandas", pd), ("matplotlib", matplotlib),
                  ("opencv", cv2), ("pillow", PIL), ("scikit-image", skimage),
                  ("scikit-learn", sklearn)]:
    print(f"{name:14s} {mod.__version__}")

for name in ["imagehash", "albumentations", "torch", "torchvision",
             "timm", "ultralytics", "segmentation_models_pytorch", "transformers"]:
    try:
        mod = __import__(name)
        print(f"{name:14s} {getattr(mod, '__version__', 'ok')}")
    except ImportError:
        print(f"{name:14s} не установлен")

try:
    import torch
    print("GPU:", torch.cuda.get_device_name(0) if torch.cuda.is_available() else "нет, работаем на CPU")
except ImportError:
    pass

img = np.zeros((32, 32, 3), dtype=np.uint8)
ok, buf = cv2.imencode(".png", img)
assert ok and cv2.imdecode(buf, cv2.IMREAD_COLOR).shape == (32, 32, 3)
print("OpenCV читает и пишет изображения: ок")
```

Для лаб 1–3 строки `torch`, `timm` и прочие могут показывать «не установлен», это нормально. Python должен быть 3.10–3.12, остальные пакеты должны напечатать номер версии.

---

## Если что-то сломалось

| Что видите                                                     | Что сделать                                                                                             |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `cv2.imread` вернул `None`                                     | Проверьте путь: файл существует? В пути нет кириллицы? Выведите `os.path.exists(path)`                  |
| `ModuleNotFoundError: No module named 'cv2'`                   | Окружение не активировано или ядро Jupyter выбрано не то. Проверьте `sys.executable`                    |
| После `pip install ultralytics` перестал работать `import cv2` | `pip uninstall -y opencv-python opencv-python-headless`, затем `pip install "opencv-python-headless<5"` |
| `ImportError: libGL.so.1` на Linux                             | Стоит `opencv-python` вместо headless-версии, лечится так же, как строкой выше                          |
| В Kaggle `git clone` пишет `Could not resolve host`            | Не включён Internet в Session options                                                                   |
| В Kaggle нет пункта GPU                                        | Не подтверждён телефон или закончилась недельная квота                                                  |
| `pip` ругается на Python 3.13                                  | Поставьте Python 3.11 и пересоздайте окружение                                                          |

