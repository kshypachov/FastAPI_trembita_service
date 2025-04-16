# Синхронний Python REST-сервіс з підтримкою системи «Трембіта»

REST-сервіс, описаний в даній інструкції, розроблений мовою програмування Python з використанням фреймворку FastAPI і є сумісним з системою «Трембіта».

FastAPI – це сучасний, швидкий (високопродуктивний) web-фреймворк для побудови API з Python 3.10+ на основі стандартних асинхронних викликів.

Даний сервіс передбачає отримання з бази даних (реєстру) відомостей про інформаційні об'єкти (користувачів) та управління їх статусом, у тому числі обробку запитів на пошук, отримання, створення, редагування та видалення об'єктів.

Для демонстрації інтеграції з системою «Трембіта» було розроблено [вебклієнт](https://github.com/MadCat-88/Trembita_Py_R_SyncCli) для роботи з даним вебсервісом.


## Вимоги до програмного забезпечення 
| ПЗ            |   Версія   | Примітка                                                                                                                                                                                               |
|:--------------|:----------:|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ubuntu Server |   24.04    | Рекомендовані характеристики віртуальної машини:<br/> CPU: 1 <br/> RAM: 512 Мб                                                                                                                         |
| Python        | **3.10!**  | За допомогою скрипта встановлюється автоматично.<br/> Також можна встановити вручну при виборі відповідного типу інсталяції.<br/>**Важливо!** Якщо версія Python нижче 3.10, сервіс працювати не буде. |
| MariaDB       |   10.5+    | За допомогою скрипта встановлюється автоматично.<br/> Також можна встановити вручну при виборі відповідного типу інсталяції                                                                            |
| Git           |            | Для клонування репозиторію                                                                                                                                                                             |


## Залежності

Залежності програмного забезпечення вебсервісу зазначені в файлі `requirements.txt`.

## Структура проєкту

Структура проєкту виглядає наступним чином:

```
FastAPI_trembita_service/
├── main.py                # Точка входу застосунку
├── config.ini             # Конфігурація проєкту
├── alembic.ini            # Конфігурація міграцій БД
├── utils/
│   ├── validations.py     # Валідація полів повідомлень
│   ├── update_person.py   # Оновлення запису у БД
│   ├── get_person.py      # Пошук запису у БД за критерієм
│   ├── get_all_persons.py # Отримання всіх записів БД
│   ├── delete_person.py   # Видалення запису з БД
│   ├── create_person.py   # Створення запису у БД
│   └── config_utils.py    # Зчитування конфігураційного файлу
├── models/
│   └── person.py          # Моделі даних
├── migrations/
│   └── env.py             # Підключення моделей для Alembic
├── docs/                  # Документація з розгортання
├── requirements.txt       # Залежності ПЗ вебсервісу
├── README.md              # Документація
├── deploy.sh              # Скрипт для автоматизації встановлення
├── remove.sh              # Скрипт для видалення сервісу та очищення системи
```

## Інсталяція сервісу

Сервіс можна інсталювати за допомогою скрипта автоматичного встановлення або вручну. Також сервіс може працювати в Docker. 
- [Інсталяція сервісу за допомогою скрипта автоматичного встановлення](./docs/script_installation.md).
- [Інсталяція сервісу вручну](./docs/manual_installation.md).
- [Конфігурація сервісу](./docs/configuration.md).
- [Розгортання вебсервісу в Docker ](./docs/docker_installation.md).


## Наповнення бази даних тестовими записами

Для забезпечення зручності тестування розробленого вебсервісу потрібно наповнити його БД тестовими записами.
З цією метою було розроблено окремий скрипт, інсталяція та робота з яким описані [тут](https://github.com/MadCat-88/Trembita_PutFakeData_Rest)

## Адміністрування сервісу

### Запуск вебсервісу

Для запуска вебсервісу необхідно виконати наступну команду:

```bash
sudo systemctl start fastapi_trembita_service
```

### Ознайомлення з документацією АРІ

Після запуску вебсервісу можна отримати доступ до автоматичної **документації API** за наступними адресами:

- Swagger UI: http://[адреса серверу]:8000/docs
- ReDoc: http://[адреса серверу]:8000/redoc

### Перевірка статусу вебсервісу

```bash
sudo systemctl status fastapi_trembita_service
```

### Зупинка вебсервісу

```bash
sudo systemctl stop fastapi_trembita_service
```

### Видалення вебсервісу

Для видалення вебсервісу та всіх пов'язаних компонентів було створено скрипт `remove.sh`.
Даний скрипт після запуску, зупинить та видалить вебсервіс, видалить віртуальне середовище, клонований репозиторій та системні залежності.

Для того, щоб запустити скрипт необхідно:

1. Зробити файл виконуваним:

```bash
chmod +x remove.sh
```

2. Запустити скрипт:

```bash
./remove.sh
```

Також існує можливість видалення вебсервісу вручну, згідно відповідної [інструкції](/docs/delete.md)

### Перегляд журналу подій

За замовчуванням журнал подій зберігається у файлі `fastapi_trembita_service.log`.

Конфігурація параметрів журналювання подій виконується в файлі «config.ini». Детальніше з параметрами журналювання в даному файлі можна ознайомитися в [настановах з конфігурації](/docs/configuration.md).

Для того, щоб переглянути журнал подій вебсервісу необхідно виконати команду:

```bash
journalctl -u fastapi_trembita_service -f
```

### Налаштування HTTPS

Налаштування підключення до сервісу за протоколом HTTPS наведено [в інструкції](./docs/https_nginx_reverse_proxy.md).

## Інтеграція вебсервісу з системою «Трембіта»

Системи «Трембіта» не вимагає особливої спеціалізації вебсервісів для роботи з нею. Для повноцінної інтеграції з системою «Трембіта» вебсервіс повинен підтримувати можливість зберігання заголовків системи «Трембіта», які було передані в запиті від вебкліента через ШБО.
В даному вебсервісі за це відповідає наступний фрагмент коду в файлі `main.py`:

```
# Логування всіх заголовків
headers = dict(request.headers)
logger.info("Заголовки запиту:")
for header_key, header_value in headers.items():
logger.info(f"    {header_key}: {header_value}")

# Логування додаткових параметрів запиту Трембити
if queryId:
    logger.info(f"Значення параметру запиту queryId: {queryId}")
if userId:
    logger.info(f"Значення параметру запиту userId: {userId}")  
```

де:
- header_key – заголовок запиту системи «Трембіта»;
- header_value – значення заголовку.

Більш детальну інформацію про заголовки наведено в описі [роботи із REST-сервісами в системі «Трембіта»](https://github.com/MadCat-88/Services-development-for-Trembita-system/blob/main/REST%20services%20development%20for%20Trembita%20system.md#%D0%B7%D0%B0%D0%B3%D0%BE%D0%BB%D0%BE%D0%B2%D0%BA%D0%B8-%D0%B7%D0%B0%D0%BF%D0%B8%D1%82%D1%96%D0%B2-%D0%B4%D0%BB%D1%8F-rest-%D1%81%D0%B5%D1%80%D0%B2%D1%96%D1%81%D1%96%D0%B2-%D0%BD%D0%B5%D0%BE%D0%B1%D1%85%D1%96%D0%B4%D0%BD%D1%96-%D0%B7%D0%B0%D0%B4%D0%BB%D1%8F-%D0%B7%D0%B0%D0%B1%D0%B5%D0%B7%D0%BF%D0%B5%D1%87%D0%B5%D0%BD%D0%BD%D1%8F-%D1%81%D1%83%D0%BC%D1%96%D1%81%D0%BD%D0%BE%D1%81%D1%82%D1%96-%D0%B7-%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%BE%D1%8E-%D1%82%D1%80%D0%B5%D0%BC%D0%B1%D1%96%D1%82%D0%B0).
- queryId та userId – додаткові параметри запиту, які передаються в URL.

- Більш детальну інформацію про додаткові параметри наведено в розділі [Використання сервісу](./docs/using.md).

## Використання сервісу

Вебсервіс представляє собою набір з 5 методів, які дозволяють управляти записами про умовних користувачів (Person) в БД:

- [створення нового запису](./docs/using.md#person-post);
- [отримання всіх записів з БД](./docs/using.md#person-get-all);
- [оновлення існуючого запису за кодом УНЗР](./docs/using.md#person-update);
- [отримання запису по заданому критерію пошуку](./docs/using.md#person-get-by-parameter);
- [видалення існуючого запису за кодом УНЗР](././docs/using.md#person-delete).

Після встановлення вебсервісу його база даних порожня. 
Для демонстрації можливостей вебсервісу першим кроком необхідно створити нові записи в БД. Це можна зробити використовуючи відповідний [вебклієнт](https://github.com/MadCat-88/Trembita_Py_R_SyncCli), з використанням методу [Person Post](./docs/using.md#person-post) або за допомогою [скрипта наповнення бази даних](./README.md#наповнення-бази-даних-тестовими-записами).


## Внесок

Якщо ви хочете зробити свій внесок у проєкт, будь ласка, створіть форк репозиторію і відправте Pull Request.

## Ліцензія

Цей проєкт ліцензується відповідно до умов MIT License.

 ##
Матеріали створено за підтримки проєкту міжнародної технічної допомоги «Підтримка ЄС цифрової трансформації України (DT4UA)».
