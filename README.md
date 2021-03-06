Тестовое задание для frontend-разработчика
---

Вашему вниманию представляется тествое задание для кандидата на должность frontend-разработчика.
От вас требуется решить описанную ниже задачу с помощью web-приложения. При создании приложения вы можете использовать
любой фреймворк (предпочтительнее ReactJS в связке с Typescript). Мы не накладываем никаких ограничений на поддержку браузеров.

Вы можете отправить нам ссылку на ваш репозиторий или ссылку на развернутое приложение.

Наши контакты: [personal@itmh.ru](mailto:personal@itmh.ru), 8-800-755-7000
Наши вакансии: [job.itmh.ru](https://job.itmh.ru/#work)

# Задача

Есть некоторое количество телеканалов.
Телеканалы разбиты на группы так, что каждый телеканал состоит минимум в одной группе.
У телеканалов есть программа телепередач, причем, для каждого канала можно узнать две передачи - текущую и следующую.
Необходимо сделать приложение, которое бы показывало список телеканалов, входящих в выбранную группу.
Для выбора группы телеканалов можно использовать элемент управления на подобии select или списка тэгов.
Каждый телеканал должен быть представлен логотипом, названием и текущей телепередачей.
Текущая телепередача канала представляется названием, временем начала и прогрессбаром,
на котором было бы видно какая часть передачи прошла от её начала.

![Макет приложения](example.png "Макет приложения")

# RESTFul API

Данные для работы приложения (списки групп каналов, каналы и программу телепередач) можно получить через RESTFul API.
RESTFul API предоставляется NodeJS приложением с втроенным веб-сервером, веб сервер запускается на порту 3000, слушает
все доступные интерфейсы.
 
Пример запуска приложения :

```
$ npm start
listen connections on port 3000
```

Можно указать нормер порта и адрес, на которых веб-сервре будет принимать соединения:

```
$ npm start -- --address 127.0.0.1 --port 80
listen connections on 127.0.0.1:80
```

Пример доступа к API с помощью curl:

```
$ curl -s -i http://localhost:3000/group
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Content-Type: application/json; charset=utf-8

[
  {
    "id": "Детские",
    "name": "Детские"
  },
  {
    "id": "Живая природа",
    "name": "Живая природа"
  },
  ...
]
```

ВАЖНО! Перед запуском приложения убедитесь, что были установлены все компоненты, необходимые для работы приложения

```
$ npm install
```

## GET /group

Запрос списка групп телеканалов. Каждая группа представлена объектом, включающим поля:

  1. `id` - идентификатор группы;
  1. `name` - нвзвание группы.

```
$ curl -s -i http://localhost:3000/group
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Content-Type: application/json; charset=utf-8

[
  {
    "id": "Детские",
    "name": "Детские"
  },
  {
    "id": "Живая природа",
    "name": "Живая природа"
  },
  ...
]
```

## GET /group/:id/channel

Запрос списка телеканалов в группе. Каждый телеканал представлен объектом, включающим поля:

  1. `id` - идентификатор канала;
  1. `name` - нвзвание канала;
  1. `icon` - относительный URL логотипа канала;
  1. `program` - список программы телепередач, если при запросе был казан query параметр `?withProgram`.

```
$ curl -s -i http://localhost:3000/group/%D0%94%D0%B5%D1%82%D1%81%D0%BA%D0%B8%D0%B5/channel
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Content-Type: application/json; charset=utf-8

[
  {
    "id": 80,
    "name": "Рыжий",
    "icon": "/public/channel-80.png",
    "groups": [
      "Детские"
    ]
  },
  {
    "id": 112,
    "name": "Nickelodeon",
    "icon": "/public/channel-112.png",
    "groups": [
      "Детские"
    ]
  },
  ...
]
```

При запросе с query параметром `?withProgram`

```
$ curl -s -i http://localhost:3000/group/%D0%94%D0%B5%D1%82%D1%81%D0%BA%D0%B8%D0%B5/channel?withProgram
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Content-Type: application/json; charset=utf-8

[
  {
    "id": 80,
    "name": "Рыжий",
    "icon": "/public/channel-80.png",
    "groups": [
      "Детские"
    ],
    "program": {
      "current": {
        "startTime": "2019-03-18T16:55:00.000Z",
        "name": "Динофроз",
        "endTime": "2019-03-18T17:20:00.000Z"
      },
      "next": {
        "startTime": "2019-03-18T17:20:00.000Z",
        "name": "Динофроз",
        "endTime": "2019-03-18T17:50:00.000Z"
      }
    }
  },
  {
    "id": 112,
    "name": "Nickelodeon",
    "icon": "/public/channel-112.png",
    "groups": [
      "Детские"
    ],
    "program": {
      "current": {
        "startTime": "2019-03-18T16:45:00.000Z",
        "name": "Черепашки-ниндзя",
        "endTime": "2019-03-18T17:10:00.000Z"
      },
      "next": {
        "startTime": "2019-03-18T17:10:00.000Z",
        "name": "Санджей и Крэйг",
        "endTime": "2019-03-18T17:30:00.000Z"
      }
    }
  },
  ...
]
```

## GET /channel/:id/program

Запрос программы телепередач. Ответ - объект из двух полей:

  1. `current` - текущая телепередача на момент получения запроса сервером;
  2. `next` - телепередача следующая за текущей.

```
$ curl -s -i http://localhost:3000/channel/80/program
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Content-Type: application/json; charset=utf-8

{
  "current": {
    "startTime": "2019-03-18T16:55:00.000Z",
    "name": "Динофроз",
    "endTime": "2019-03-18T17:20:00.000Z"
  },
  "next": {
    "startTime": "2019-03-18T17:20:00.000Z",
    "name": "Динофроз",
    "endTime": "2019-03-18T17:50:00.000Z"
  }
}
```
