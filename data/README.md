# Данные курса

Все лабораторные курса вы делаете на одном датасете, [Oxford-IIIT Pet](https://www.robots.ox.ac.uk/~vgg/data/pets/). Его собрали в Оксфорде и IIIT Хайдарабад в 2012 году: 7390 фотографий кошек и собак 37 пород, около 200 снимков на породу. Среди кошек есть бенгальская, мейн-кун, сфинкс и русская голубая, среди собак бигль, мопс, сенбернар и сиба-ину.

У каждой фотографии есть три вида разметки, и за семестр вы используете все три:

- **порода и вид** (кошка или собака): классификация, лаба 4;
- **бокс головы** животного в формате Pascal VOC XML: детекция, лаба 5. Бокс есть примерно у половины снимков;
- **trimap-маска** животного: сегментация, лабы 2 и 6.

## Ваш вариант

Вариант равен вашему порядковому номеру в списке группы: пятый по списку берёт вариант 5. В варианте 4 породы, 2 кошачьих и 2 собачьих, это около 800 изображений и 85 МБ в архиве. Список вариантов лежит [внизу страницы](#таблица-вариантов).

## Как скачать

Скрипт `get_data.py` работает на Python 3.8 и новее и обходится стандартной библиотекой, ставить ничего не нужно. Он скачивает архив варианта из [релиза data-v1](https://github.com/ykvnkm/computer-vision-course/releases/tag/data-v1), проверяет контрольную сумму SHA-256 и распаковывает его. Если вариант уже скачан, повторный запуск ничего не качает.

### Локально

Из корня клона репозитория курса:

```bash
python data/get_data.py --variant 7
```

Появится папка `data/variant_07/`. Если нужна другая папка, добавьте `--dest путь/к/папке`.

### Google Colab

Вставьте в первую ячейку ноутбука и поменяйте номер варианта:

```python
import os, sys, urllib.request
os.makedirs("data", exist_ok=True)
urllib.request.urlretrieve("https://raw.githubusercontent.com/ykvnkm/computer-vision-course/main/data/get_data.py", "data/get_data.py")
sys.path.insert(0, "data"); from get_data import ensure_variant
DATA_DIR = ensure_variant(7)   # /content/data/variant_07
```

`ensure_variant` возвращает путь к папке варианта (`pathlib.Path`). Тот же код работает в Kaggle и в локальном Jupyter.

### Kaggle

В Kaggle интернет в ноутбуке по умолчанию выключен. Включите его так: справа панель **Settings → Internet → On**. Kaggle попросит один раз подтвердить номер телефона. После этого работает тот же код, что и для Colab, данные окажутся в `/kaggle/working/data/variant_07/`.

Если подтвердить телефон не получается, запускайте ноутбук в Colab или локально.

### Если GitHub недоступен

Запасной путь собирает вариант из официальных архивов Oxford (около 810 МБ, качаются один раз):

```bash
python data/get_data.py --variant 7 --from-official
```

Если архивы `images.tar.gz` и `annotations.tar.gz` у вас уже есть, укажите папку с ними: `--official-source путь/к/папке`. Когда GitHub снова заработает, перекачайте вариант командой `python data/get_data.py --variant 7 --force`, чтобы данные совпадали с данными остальной группы.

Остальные ключи: `--list` печатает список вариантов, `--check` сверяет распакованную папку с эталоном, `--source` задаёт другой источник архивов (папку или URL).

## Что внутри

```
data/variant_07/
├── images/                 изображения с исходными именами Oxford: Bengal_12.jpg, pug_105.jpg
├── annotations/
│   ├── xmls/               Pascal VOC XML с боксом головы (есть не у всех изображений)
│   └── trimaps/            PNG-маски, имя как у изображения: Bengal_12.png
├── labels.csv              filename,breed,species,class_id
├── variant.json            породы, class_id, число файлов по породам
└── README.md               описание варианта
```

- **labels.csv**: одна строка на изображение. `breed` записан так же, как в имени файла (`British_Shorthair`, `american_pit_bull_terrier`), `species` равен `cat` или `dog`, `class_id` от 0 до 3. Номера классов идут в порядке списка `breeds` из `variant.json`: сначала кошки, потом собаки, по алфавиту.
- **Имена файлов**: кошачьи породы в Oxford начинаются с заглавной буквы, собачьи со строчной. Номер после подчёркивания ничего не значит, в нумерации есть пропуски.
- **XML**: в теге `<object><name>` записан вид (`cat` или `dog`), а не порода. Породу берите из `labels.csv`. Координаты бокса `xmin, ymin, xmax, ymax` в пикселях, отсчёт с 1, как принято в Pascal VOC.
- **Изображения** лежат такими, какими их выложили авторы: размеры от 103 до 3264 пикселей по стороне, встречаются файлы в оттенках серого и с альфа-каналом. Если вашему коду нужны ровно три канала, приводите явно: `Image.open(p).convert("RGB")`.

### Формат trimap

Маска лежит в PNG с одним каналом, в каждом пикселе одно из трёх чисел:

| Значение | Что означает |
|---:|---|
| 1 | животное |
| 2 | фон |
| 3 | граница животного, пиксели, которые разметчик не отнёс ни к животному, ни к фону |

В обычном просмотрщике маска выглядит чёрной: значения 1–3 из 255 почти не отличаются от нуля. Чтобы её увидеть, покажите её через `plt.imshow(mask)` или умножьте на 80. Для бинарной маски «животное или нет» обычно берут `mask == 1` или `mask != 2`. Что делать с границей, решаете вы, и это решение стоит записать в отчёт.

### Пример чтения

```python
import numpy as np, pandas as pd
from PIL import Image
labels = pd.read_csv(DATA_DIR / "labels.csv")
img = np.asarray(Image.open(DATA_DIR / "images" / labels.filename[0]).convert("RGB"))
mask = np.asarray(Image.open(DATA_DIR / "annotations" / "trimaps" / labels.filename[0].replace(".jpg", ".png")))
```

## Таблица вариантов

- Вариант 00 демонстрационный, на нем будут объяснятся решения на парах. 
- Названия пород по-русски есть в `README.md` внутри каждого варианта.

| Вариант | Кошки                           | Собаки                                                |
| ------: | ------------------------------- | ----------------------------------------------------- |
|      00 | Bengal, Egyptian_Mau            | saint_bernard, staffordshire_bull_terrier             |
|      01 | Bengal, Egyptian_Mau            | great_pyrenees, havanese                              |
|      02 | Birman, Siamese                 | basset_hound, keeshond                                |
|      03 | British_Shorthair, Russian_Blue | beagle, leonberger                                    |
|      04 | Birman, Ragdoll                 | german_shorthaired, pug                               |
|      05 | British_Shorthair, Russian_Blue | saint_bernard, shiba_inu                              |
|      06 | British_Shorthair, Russian_Blue | german_shorthaired, samoyed                           |
|      07 | Birman, Siamese                 | english_setter, pomeranian                            |
|      08 | Maine_Coon, Ragdoll             | american_pit_bull_terrier, staffordshire_bull_terrier |
|      09 | Persian, Sphynx                 | american_bulldog, staffordshire_bull_terrier          |
|      10 | Abyssinian, Bengal              | scottish_terrier, wheaten_terrier                     |
|      11 | Bengal, Egyptian_Mau            | english_cocker_spaniel, havanese                      |
|      12 | Bombay, Egyptian_Mau            | american_pit_bull_terrier, staffordshire_bull_terrier |
|      13 | Maine_Coon, Persian             | chihuahua, miniature_pinscher                         |
|      14 | British_Shorthair, Russian_Blue | basset_hound, pomeranian                              |
|      15 | Bombay, Sphynx                  | american_bulldog, boxer                               |
|      16 | Bombay, Maine_Coon              | american_bulldog, american_pit_bull_terrier           |
|      17 | Bombay, Persian                 | chihuahua, miniature_pinscher                         |
|      18 | Abyssinian, Bengal              | japanese_chin, pug                                    |
|      19 | Birman, Ragdoll                 | great_pyrenees, wheaten_terrier                       |
|      20 | Ragdoll, Sphynx                 | american_bulldog, boxer                               |
|      21 | Abyssinian, Bengal              | newfoundland, samoyed                                 |
|      22 | Birman, Siamese                 | newfoundland, yorkshire_terrier                       |

В каждом варианте есть одна пара похожих пород, которые модели путают чаще остальных (например, бирманская кошка и рэгдолл или питбультерьер и стаффордширский бультерьер). Какая пара у вас, видно в `data/variants.json`, поле `hard_pair`. В лабе 4 проверьте, на ней ли ошибается ваша модель.

Машиночитаемая версия таблицы: [variants.json](variants.json).

## Лицензия

Oxford-IIIT Pet Dataset распространяется по лицензии [Creative Commons Attribution-ShareAlike 4.0](https://creativecommons.org/licenses/by-sa/4.0/). Авторские права на сами фотографии остаются у их владельцев. Варианты курса являются производной работой и распространяются на тех же условиях. 