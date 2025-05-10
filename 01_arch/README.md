# Домашнее задание к занятию «Архитектура современных веб-сервисов»

Пришлите ответы на вопросы в личном кабинете на сайте [netology.ru](https://netology.ru).

## Задание «Карта взаимодействия»

### Описание
<details> <summary>Нажмите, чтобы развернуть/свернуть</summary>

Вам попало в руки приложение, состоящее из нескольких сервисов, и клиент к нему. Ваша задача — используя Wireshark, построить карту взаимодействия между сервисами в рамках запросов, которые отправляет клиент. Нужно проанализировать ответы.

В каталоге [assets](assets) даны 4 сервера (`server-1/4`) под платформы:

1. *.bin — Linux.
2. *.exe — Windows.
3. i*.bin — macOS.

А также клиент к ним (`client`):

1. *.bin — Linux.
2. *.exe — Windows.
3. i*.bin — macOS.

### Этапы выполнения

1. Скачайте серверы для вашей платформы. Не забудьте проверить любые скачиваемые файлы через VirusTotal.
2. Скачайте каталоги с ключами **[`keys1`](assets/keys1)** и **[`keys2`](assets/keys2)** и разместите их в том же каталоге, что и скачанные в п.1 серверы.
3. Запустите по порядку серверы от 1 до 4. Они стартуют на портах 9001–9004 соответственно.
4. Запустите Wireshark в режиме отслеживания loopback (`Loopback: lo`).
5. Запустите клиента, проверяя, что клиент выводит ответ в том виде, как показано ниже. Часть данных может отличаться.


```json
{
  "transactions": [
    {
      "id": 1,
      "userId": 999,
      "category": "auto",
      "amount": 1000000,
      "created": 1613389415
    }
  ],
  "categoryStats": {
    "auto": 1000000
  }
}
```

**Примечание**. Вы не сможете скачать сами каталоги, если не умеете пользоваться Git, поэтому аккуратно скачайте файлы ключей и положите их в соответствующие каталоги, которые создаёте на своём компьютере. У вас должна получиться структура:

- keys1/
  - public.key
  - private.key
- keys2/
  - public.key
- client-x64.bin (либо другой для вашей платформы)
- server1-x64.bin (либо другой для вашей платформы)
- server2-x64.bin (либо другой для вашей платформы)
- server3-x64.bin (либо другой для вашей платформы)
- server4-x64.bin (либо другой для вашей платформы)

Серверы и клиенты запускайте из командной строки.

</details>

### Решение задания
<details> <summary>Ранее сделанное решение</summary>
В качестве решения пришлите в формате ниже ответы на вопросы:
1. Каким образом проходит путь запросов от клиента: на какой сервис и через какие сервисы?
```
Client → Server1 (9001) → Server2 (9002) → Server3 (9003) → Server4 (9004)
```


2. Какие запросы делаются на каждом этапе, и какие ответы на них приходят?

1. Client --> Server 1 (запрос аутентификации):
```
PUT http://localhost:9001/users  
Content-Type: application/x-www-form-urlencoded  

login=user&password=111111  
```

2. Server 1 --> Client (ответ с токеном):
```
200 OK  
Content-Type: application/json  

{  
  "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsImxvZ2luIjoidXNlciIsInJvbGVzIjpbIlVTRVIiXSwiaWF0IjoxNzQ2ODc0MzAxLCJleHAiOjE3NDY4Nzc5MDF9.fcbNOlPVC4GwysrqOcCHboGsT4xCd7L5a9KHCkPwXRsKd_3ysH6L6O2X1n4tUsyqgD..."  
}  

```

3. Client --> Server 2 (запрос транзакций с JWT):
```
GET http://localhost:9002/api/transactions  
Authorization: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsImxvZ2luIjoidXNlciIsInJvbGVzIjpbIlVTRVIiXSwiaWF0IjoxNzQ2ODc0MzAxLCJleHAiOjE3NDY4Nzc5MDF9.fcbNOlPVC4GwysrqOcCHboGsT4xCd7L5a9KHCkPwXRsKd_3ysH6L6O2X1n4tUsyqgD...  

```

4. Server 2 --> Client (ответ с транзакциями и статистикой):
```
200 OK  
Content-Type: application/json  

{  
  "transactions": [  
    {  
      "id": 1,  
      "userId": 2,  
      "category": "auto",  
      "amount": 1000000,  
      "created": 1746874144  
    },  
    {  
      "id": 2,  
      "userId": 2,  
      "category": "auto",  
      "amount": 100000,  
      "created": 1746874144  
    },  
    {  
      "id": 3,  
      "userId": 2,  
      "category": "food",  
      "amount": 100000,  
      "created": 1746874144  
    }  
  ],  
  "categoryStats": {  
    "auto": 1100000,  
    "food": 100000  
  }  
}  
```

5. Client --> Server 3 (запрос транзакций с X-Userid):

```
GET http://localhost:9003/api/transactions  
X-Userid: 2  
```

6. Server 3 --> Client (ответ с транзакциями и статистикой):
```
200 OK  
Content-Type: application/json  

{  
  "transactions": [  
    {  
      "id": 1,  
      "userId": 2,  
      "category": "auto",  
      "amount": 1000000,  
      "created": 1746874144  
    },  
    {  
      "id": 2,  
      "userId": 2,  
      "category": "auto",  
      "amount": 100000,  
      "created": 1746874144  
    },  
    {  
      "id": 3,  
      "userId": 2,  
      "category": "food",  
      "amount": 100000,  
      "created": 1746874144  
    }  
  ],  
  "categoryStats": {  
    "auto": 1100000,  
    "food": 100000  
  }  
}  
```
7. Client --> Server 4 (запрос транзакций с X-Userid):

```
GET http://localhost:9004/api/transactions  
X-Userid: 2  
```

8. Server 4 --> Client (ответ только с транзакциями, без статистики):

```
200 OK  
Content-Type: application/json  

[  
  {  
    "id": 1,  
    "userId": 2,  
    "category": "auto",  
    "amount": 1000000,  
    "created": 1746874144  
  },  
  {  
    "id": 2,  
    "userId": 2,  
    "category": "auto",  
    "amount": 100000,  
    "created": 1746874144  
  },  
  {  
    "id": 3,  
    "userId": 2,  
    "category": "food",  
    "amount": 100000,  
    "created": 1746874144  
  }  
]  
```


Все сервисы (9002, 9003, 9004) возвращают одни и те же транзакции, но в разном формате.

Сервисы 9002 и 9003 добавляют статистику по категориям (categoryStats), а 9004 — нет.

Для аутентификации используется JWT (порт 9001), а для запросов — либо этот токен, либо заголовок X-Userid.
</details>

Исправленное решение

Путь запросов от клиента проходит через следующие сервисы и порты:

1. Клиент ↔ Сервер 1 (порт 9001)
  Клиент отправляет запрос на порт 9001 (исходящий порт клиента: 40564).
  Сервер 1 отвечает на порт 40564.

2. Клиент ↔ Сервер 2 (порт 9002)
  Клиент отправляет запрос на порт 9002 (исходящий порт клиента: 42734).
  Сервер 2 отвечает на порт 42734.

3. Межсерверные взаимодействия:

Сервер 2 ↔ Сервер 3 (порт 9003)
  Сервер 2 отправляет запрос на порт 9003 (исходящий порт: 37198).
  Сервер 3 отвечает на порт 37198.

Сервер 2 ↔ Сервер 4 (порт 9004)
  Сервер 2 отправляет запрос на порт 9004 (исходящий порт: 53304).
  Сервер 4 отвечает на порт 53304.


2. Какие запросы делаются на каждом этапе, и какие ответы на них приходят?

 Клиент → Сервер 1 (порт 9001):
Исходящий порт клиента: 40564

Запрос:
```
PUT /users HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Тело: login=user&password=111111  
```
Ответ:

```
HTTP/1.1 200 OK  
Content-Type: application/json  
{"token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsImxvZ2luIjoidXNlciIsInJvbGVzIjpbIlVTRVIiXSwiaWF0IjoxNzQ2OTA3MDcwLCJleHAiOjE3NDY5MTA2NzB9.gXRYLuiWqvBB2eQi2xbndd8VKsqGIVRSjFSRdxw3guBEgw0cr-TR1iQ_SYfJG0twaDf..."}
```

Клиент → Сервер 2 (порт 9002):
Исходящий порт клиента: 42734

Запрос:
```
GET /api/transactions HTTP/1.1  
Authorization: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsImxvZ2luIjoidXNlciIsInJvbGVzIjpbIlVTRVIiXSwiaWF0IjoxNzQ2OTA3MDcwLCJleHAiOjE3NDY5MTA2NzB9.gXRYLuiWqvBB2eQi2xbndd8VKsqGIVRSjFSRdxw3guBEgw0cr-TR1iQ_SYfJG0twaDf...
```

Ответ:
```
HTTP/1.1 200 OK  
Content-Type: application/json  
{
  "transactions": [
    {"id": 1, "userId": 2, "category": "auto", "amount": 1000000, "created": 1746907056},
    {"id": 2, "userId": 2, "category": "auto", "amount": 100000, "created": 1746907056},
    {"id": 3, "userId": 2, "category": "food", "amount": 100000, "created": 1746907056}
  ],
  "categoryStats": {"auto": 1100000, "food": 100000}
}
```

Сервер 2 → Сервер 3 (порт 9003):
Исходящий порт Сервера 2: 37198

Запрос:
```
GET /api/transactions HTTP/1.1  
X-Userid: 2
```
Ответ:

```
HTTP/1.1 200 OK  
Content-Type: application/json  
{
  "transactions": [
    {"id": 1, "userId": 2, "category": "auto", "amount": 1000000, "created": 1746907056},
    {"id": 2, "userId": 2, "category": "auto", "amount": 100000, "created": 1746907056},
    {"id": 3, "userId": 2, "category": "food", "amount": 100000, "created": 1746907056}
  ],
  "categoryStats": {"auto": 1100000, "food": 100000}
}
```

Сервер 2 → Сервер 4 (порт 9004):
Исходящий порт Сервера 2: 53304

Запрос:

```
GET /api/transactions HTTP/1.1  
X-Userid: 2
```

Ответ:

```
HTTP/1.1 200 OK  
Content-Type: application/json  
[
  {"id": 1, "userId": 2, "category": "auto", "amount": 1000000, "created": 1746907056},
  {"id": 2, "userId": 2, "category": "auto", "amount": 100000, "created": 1746907056},
  {"id": 3, "userId": 2, "category": "food", "amount": 100000, "created": 1746907056}
]
```






<details> <summary>Нажмите, чтобы развернуть/свернуть</summary>


### Формат ответа

Обратите внимание: это формат, а не пример реального взаимодействия из вашего задания.

```text
1. Client --> Server 4 (запрос):

GET http://localhost:9004/authenticate
Content-Type: application/json

{
  "login": "root",
  "password": "secret"
}

2. Server 4 --> Server 3 (запрос):

GET http://localhost:9003/authenticate
Content-Type: application/json

{
  "login": "root",
  "password": "secret"
}

3. Server 3 --> Server 4 (ответ):

200 OK
Content-Type: application/json

{
  "transactions": [
    {
      "id": 1,
      "userId": 999,
      "category": "auto",
      "amount": 1000000,
      "created": 1613389415
    }
  ],
  "categoryStats": {
    "auto": 1000000
  }
}

3. Server 4 --> Client (ответ):

200 OK
Content-Type: application/json

{
  "transactions": [
    {
      "id": 1,
      "userId": 999,
      "category": "auto",
      "amount": 1000000,
      "created": 1613389415
    }
  ],
  "categoryStats": {
    "auto": 1000000
  }
}
```

## Задание «Токен»

Это необязательное задание. Его невыполнение не влияет на получение зачёта по домашнему заданию.

### Задача

В рамках того же проекта из каталога [assets](assets) вам даны ключи. Они находятся в каталогах [`keys1`](assets/keys1) и [`keys2`](assets/keys2) соответственно. Ключи предназначены для `server1` и `server2` соответственно.

Используя полученную вами в первом задании схему взаимодействия, запросы и ответы на каждом этапе, попробуйте предположить, для чего используются эти ключи.

<details>
<summary>Несколько подсказок</summary>

1. Попробуйте сравнить содержимое каталогов `keys1` и `keys2`.
2. Попытайтесь подменить один или несколько ключей и посмотреть, на что это повлияет. Не забудьте перезапустить тот сервис, для которого вы поменяли ключ.
3. Возможно, часть передаваемых данных закодирована каким-то алгоритмом. Попробуйте декодировать её и посмотреть, есть ли данные, которые указывают на то, как использовались ключи.
</details>

### Решение задания

В качестве решения пришлите информацию:
1. Для чего и на каком этапе используется, если используется, каждый ключ из каталога `keys1`.
2. Для чего и на каком этапе используется, если используется, ключ из каталога `keys2`.

</details>
