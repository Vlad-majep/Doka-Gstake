# Gstake

## API v1

Здесь будут описаны все роутер проекта, и запросы на них  
 Все объекты являются `json` сервер принимает и возвращает данные только в `json`
Вне зависимости от роутера, сервер может вернуть следующее

Status: **500** Internal Server Error

```json
{
  "message": "SERVER ERROR"
}
```

---

## auth

_POST_ `api/v1/auth/signup` - регистрация пользователя

- body:

  ```json
  {
    "name": "example_name",
    "password": "example_password"
  }
  ```

- answer:  
  Status: **201** Created

  ```json
  {
    "message": "User has been created",
    "data": {
      "name": "example_name",
      "balance": {
        "simple": 0,
        "eth": 0,
        "btc": 0,
        "usdt": 0,
        "bnb": 0
      },
      "wallets": {
        "eth": "0x1ad77aE25f5Ac93D4898A08FAE29F47de21E3a51",
        "btc": "14jg7TLpUoggZFNUoY3pNK5qvwc5gWVxHD"
      },
      "seed": [
        "red",
        "river",
        "suitcase",
        "city",
        "vacation",
        "journey",
        "office",
        "chair",
        "party",
        "star",
        "rain",
        "window",
        "sunset",
        "code",
        "forest",
        "beach",
        "food",
        "internet",
        "keyboard",
        "adventure",
        "job",
        "mountain",
        "happy",
        "banana"
      ],
      "keys": {
        "eth": "0x177614c859c392cc8fa97d015794de86aafdb20cff6deefb9aa57a1fef812b00",
        "btc": "L5gGwU26PmixnSChVh3nzYkVHAWqw4FXSNXSY2tpyTruNnDCJMiM"
      },
      "_id": "6505887ce28fe8b13ae86e6d"
    }
  }
  ```

  Status: **400** Bad Request

  ```json
  {
    "statusCode": 400,
    "error": "Bad Request",
    "message": "Validation failed",
    "validation": {
      "body": {
        "source": "body",
        "keys": ["name"], // или password
        "message": "\"name\" is required" // или must be a string // или length must be at least 2 characters long // или length must be less than or equal to 32 characters long
      }
    }
  }
  ```

  Status: **409** Conflict

  ```json
  {
    "message": "You can not use these parameters, try other ones" // при передачи одного и того же имени
  }
  ```

---

_POST_ `api/v1/auth/signin` - авторизация пользователя

- body:

  ```json
  // если двух-факторная аутентификация не включена
  {
    "name": "example_name",
    "password": "example_password"
  }
  // если двух-факторная аутентификация включена
  {
    "name": "example_name",
    "password": "example_password",
    "otp": 123456 // всегда 6 цифр
  }
  ```

- answer:
  Status: **200** OK

  ```json
  {
    "message": "You have successfully logged in"
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "User not found"
  }
  ```

  Status: **400** Bad Request

  ```json
  {
    "message": "Wrong password"
  }
  ```

---

_POST_ `api/v1/auth/signin/byWords` - авторизация пользователя используя слова вместо пароля

- bode:

  ```json
  {
    "name": "example_name",
    "words": ["example_firs_word", "example_second_word" ... ] // 24 слова
  }
  ```

- answer:
  Status: **200** OK
  ```json
  {
    "message": "You have successfully logged in"
  }
  ```

_DELETE_ `api/v1/auth/signout` - выход из системы

```
  нужно авторизоваться
```

- answer:
  Status: **200** OK
  ```json
  {
    "message": "You have successfully logged out"
  }
  ```

## admin

_POST_ `api/v1/admin/auth/signin` - авторизация администратора

- body:

  ```json
  {
    "name": "example_name",
    "password": "example_password"
  }
  ```

- answer:
  Status: **200** OK
  ```json
  {
    "message": "You have successfully logged in"
  }
  ```
  Status: **401** Unauthorized
  ```json
  {
    "message": "User is not authorized"
  }
  ```

---

_POST_ `api/v1/admin/create` - создание администратора

```
  нужно авторизоваться, как владелец
```

- body:

  ```json
  {
    "name": "example_name",
    "password": "example_password"
  }
  ```

- answer:  
   Status: **201** Created

  ```json
  {
    "message": "Admin has been created",
    "data": {
      "name": "example_name",
      "_id": "6505a25a4eae0ee06626c9a5"
    }
  }
  ```

  Status: **400** Bad Request

  ```json
  {
    "statusCode": 400,
    "error": "Bad Request",
    "message": "Validation failed",
    "validation": {
      "body": {
        "source": "body",
        "keys": ["name"], // или password
        "message": "\"name\" is required" // или must be a string // или length must be at least 2 characters long // или length must be less than or equal to 32 characters long
      }
    }
  }
  ```

  Status: **403** Forbidden

  ```json
  {
    "message": "You are not allowed to do this operation"
  }
  ```

  Status: **409** Conflict

  ```json
  {
    "message": "You can not use these parameters, try other ones" // при передачи одного и того же имени
  }
  ```

## request

_POST_ `api/v1/request` - создание запроса

```
  нужно авторизоваться, как пользователь
```

- body:

  ```json
  {
    "description": "example_description", // необязательный параметр
    "chain": "example_chain", // одна из этих 'btc', 'bsc', 'eth'
    "currency": "example_currency", // одна из этих 'bnb', 'btc', 'eth', 'usdt', 'busd'
    "value": 0.055, // положительное число больше 0
    "address": "0x0000000000000000000000000000000000000000" // 42 символа, начинается с 0x !регистр важен! 0X не подойдет
  }
  ```

- answer:

  Status: **201** Created

  ```json
  {
    "message": "Request has been created",
    "data": {
      "balance": {
        "simple": 0,
        "eth": 0,
        "btc": 9.945,
        "usdt": 0,
        "bnb": 0
      },
      "requests": [
        {
          "timeOfCreate": "2001-01-01T01:01:01.001Z",
          "chain": "btc",
          "currency": "btc",
          "value": 0.55,
          "address": "0x0000000000000000000000000000000000000000",
          "completed": false,
          "_id": "6505bb61f762991c00beb2e2"
        }
      ]
    }
  }
  ```

  Status: **403** Forbidden

  ```json
  {
    "message": "You are not allowed to do this operation"
  }
  ```

---

_PATCH_ `api/v1/request/:userId/:requestId` - принятие запроса

```
  нужно авторизоваться, как администратор или владелец
```

- params:

  `userId` - id пользователя  
  `requestId` - id запроса у пользователя

- answer:

  Status: **200** OK

  ```json
  {
    "message": "Request has been accepted"
  }
  ```

  Status: **400** Bad Request

  ```json
  {
    "message": "The request has already been accepted"
  }
  ```

  Status: **403** Forbidden

  ```json
  {
    "message": "You are not allowed to do this operation"
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "Request not found"
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "User not found"
  }
  ```

---

_GET_ `api/v1/request/:userId` - вывод запросов пользователя

```
  нужно авторизоваться, как администратор или владелец
```

- params:

  `userId` - id пользователя

- answer:

  Status: **200** OK

  ```json
  {
    "data": [ // так же может быть пустой массив
      {
        "timeOfCreate": "2001-01-01T01:01:01.001Z",
        "chain": "btc",
        "currency": "btc",
        "value": 0.55,
        "address": "0x0000000000000000000000000000000000000000",
        "completed": true,
        "_id": "6505bb61f762991c00beb2e2"
      } ...
    ]
  }
  ```

  Status: **403** Forbidden

  ```json
  {
    "message": "You are not allowed to do this operation"
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "User not found"
  }
  ```

## users

_GET_ `api/v1/users/me` - вывод текущего пользователя

```
  нужно авторизоваться, как пользователь
```

- answer:

  Status: **200** OK

  ```json
  {
    "data": [
      {
        "balance": {
          "simple": 0,
          "eth": 0,
          "btc": 0,
          "usdt": 0,
          "bnb": 0
        },
        "wallets": {
          "eth": "0xe7219D79C3C74b124D2C55d15048e444ff8Df85b",
          "btc": "1A9LKWxX4RHtPrygMPY8qtigJQQCntFu7w"
        },
        "keys": {
          "eth": "0xc8233b1948426b2a17ea9e8aad0336eda08bd5ac7c50b60d8594f47fa7b0b481",
          "btc": "Kz173FkZSNqmnyFK8i4T3n1hWNNQ8VDWQQMz6QV6o3iqLBn8i7z8"
        },
        "_id": "650487a14ed3c65bf6866b5c",
        "name": "name_1",
        "requests": [
          {
            "timeOfCreate": "2001-01-01T01:01:01.001Z",
            "chain": "btc",
            "currency": "btc",
            "value": 0.1,
            "address": "0x0000000000000000000000000000000000000000",
            "completed": true,
            "_id": "6505bb61f762991c00beb2e2"
          }
        ],
        "deposits": [],
        "transactions": []
      }
      ...
    ]
  }
  ```

  Status: **403** Forbidden

  ```json
  {
    "message": "You are not allowed to do this operation"
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "User not found"
  }
  ```

---

_GET_ `api/v1/users/` - вывод пользователей

```
  нужно авторизоваться, как администратор или владелец
```

---

_GET_ `api/v1/users/:userId` - вывод пользователей

```
  нужно авторизоваться, как администратор или владелец
```

- params:

  `userId` - id пользователя

- answer:

  Status: **200** OK

  ```json
  {
    "data": [ // так же может быть пустой массив
      {
        "timeOfCreate": "2001-01-01T01:01:01.001Z",
        "chain": "btc",
        "currency": "btc",
        "value": 0.55,
        "address": "0x0000000000000000000000000000000000000000",
        "completed": true,
        "_id": "6505bb61f762991c00beb2e2"
      } ...
    ]
  }
  ```

  Status: **403** Forbidden

  ```json
  {
    "message": "You are not allowed to do this operation"
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "User not found"
  }
  ```

## deposit

_GET_ `api/v1/deposit/:currency` - депозит сети (eth + bsc) / btc

```
  нужно авторизоваться, как пользователь
```

- params:

  `currency` - eth или btc

- answer:

  Status: **201** Created

  ```json
  {
    "data": "0x68CB4aAe1F8084dc39DF823EA518Fbd489Ebe4E9",
    "qrcode": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJQAAACUCAYAAAB1PADUAAAAAklEQVR4AewaftIAAAUISURBVO3BQY4cy5LAQDJQ978yR0tfJZCo6B49fTezP1jrksNaFx3Wuuiw1kWHtS46rHXRYa2LDmtddFjrosNaFx3Wuuiw1kWHtS46rHXRYa2LDmtd9OFLKr+p4g2VqWJSmSomlaniDZWp4g2V31TxjcNaFx3Wuuiw1kUfLqu4SeUNlZsqvlHxRGWqeFJxk8pNh7UuOqx10WGtiz78MJU3Kt5QmSomlUllqniiMlU8UZkqJpWp4hsqb1T8pMNaFx3Wuuiw1kUf/jEqU8Wk8kRlqphUpoonKv9LDmtddFjrosNaF334x6lMFZPKVDGpTBXfqJhUpor/ssNaFx3Wuuiw1kUffljFb6p4ovJEZap4o2JSeVLxjYq/yWGtiw5rXXRY66IPl6n8TVSmikllqphUpopJZap4Q2WqeKLyNzusddFhrYsOa1304UsVf7OKSWWqmFSmiknlpoonFf8lh7UuOqx10WGti+wPvqAyVUwqN1U8UZkqfpLKVPGGylQxqdxU8ZMOa110WOuiw1oXffhSxZOKSWWqmFSmiicqU8Wk8qRiUnmjYlJ5UjFVvFExqUwVk8oTlaniG4e1LjqsddFhrYs+fEllqnhSMalMFZPKVDFVvFExqTypmFS+oTJVTCpvVDypmFSmipsOa110WOuiw1oXffhSxaTyRsWTim9UvFHxhsobFW9UTCpvVDxRmSq+cVjrosNaFx3WuujDD6uYVN5Q+UbFpDJVTCpTxVQxqUwVk8qkMlW8UTGpTBVPKn7SYa2LDmtddFjrog+XVUwqU8Wk8qTiDZVvVDxRmSomlaliUplUpopJ5SepTBXfOKx10WGtiw5rXWR/8AWVqWJSeVLxROVJxROVqWJSeVIxqUwVN6lMFZPKTRU3Hda66LDWRYe1LvrwpYqbVKaKN1SeqEwVk8qTijdUpopvVEwqTyomlZ90WOuiw1oXHda66MOXVG6qmFSeVEwVk8pU8aTiDZUnFTepTBXfUJkqvnFY66LDWhcd1rrow1+uYlKZVKaKJypTxaTyRsUbKlPFVDGpTBWTylTxpGJSuemw1kWHtS46rHXRh8sqnqi8oTJVPFGZKp6oTBWTyjdUpopJ5Q2VN1SmiqnipsNaFx3Wuuiw1kUffpjKVDGpPKl4o+KJylQxqUwVN6lMFZPKVDGpvFExqUwVNx3Wuuiw1kWHtS768MtUpoonKm9UPKmYVKaKJypvVDxReaLyhsr/p8NaFx3Wuuiw1kX2B/9hKlPFpPJGxTdUnlRMKlPFGypPKp6oTBXfOKx10WGtiw5rXfThSyq/qWKqmFSmikllqnhD5Rsqb6hMFU8qnqhMFTcd1rrosNZFh7Uu+nBZxU0qT1SmiicVN1U8UflGxRsqU8UTlaniG4e1LjqsddFhrYs+/DCVNyq+oTJVTCpPKt5QeVLxRGVS+UbFpPKk4qbDWhcd1rrosNZFH/4xFT+p4onKGxWTylTxjYrfdFjrosNaFx3WuujDP0bljYpJZap4ovJE5UnFVDGpfKNiUpkqbjqsddFhrYsOa1304YdV/KSKSeVJxZOKJypPKiaVqWJSmSqmiknlb3ZY66LDWhcd1rrow2Uqv0nlGyrfqHhDZaqYVKaKqWJSmSqeVPykw1oXHda66LDWRfYHa11yWOuiw1oXHda66LDWRYe1LjqsddFhrYsOa110WOuiw1oXHda66LDWRYe1LjqsddFhrYv+D32YdiAxgq0QAAAAAElFTkSuQmCC"
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "User not found"
  }
  ```

---

_GET_ `api/v1/deposit/:amount/:currency/:period` - депозит сети (eth + bsc) / btc

```
  нужно авторизоваться, как пользователь
```

- params:
  `amount` - количество валюты
  `currency` - bnb, btc, eth, usdt, busd
  `period` - количество дней, на которое будет открывается депозит

- answer:

  Status: **200** OK

  ```json
  {
    "data": 1.1643835616438356
  }
  ```

---

_POST_ `api/v1/deposit/` - создать депозит

```
  нужно авторизоваться, как пользователь
```

- body:

  ```json
  {
    "currency": "example_currency", // одна из этих 'bnb', 'btc', 'eth', 'usdt', 'busd'
    "value": 1, // положительное число больше 0
    "period": 1 // количество дней, только целое число
  }
  ```

  - answer:

  Status: **201** Created

  ```json
  {
    "message": "Deposit has been created",
    "data": {
      "timeOfCreate": "2023-09-17T16:30:47.809Z",
      "timeOfEnd": "2023-09-18T16:30:47.809Z",
      "value": 1,
      "receivedValue": 1.1643835616438356,
      "currency": "eth",
      "completed": false,
      "_id": "650729b7c1a4a261912e857d"
    }
  }
  ```

  Status: **403** Forbidden

  ```json
  {
    "message": "User don't have enough money"
  }
  ```

## two factor authentication

_POST_ `two_factor_authentication` - включить двух факторную аутентификацию

```
  нужно авторизоваться, как пользователь
```

- body:

  ```json
  {
    "otp": 000000 // всегда 6 символов
  }
  ```

- answer:

  Status: **200** Created

  ```json
  {
    "message": "Two factor now is turn on"
  }
  ```

  Status: **400** Not Found

  ```json
  {
    "message": "Incorrect data entered"
  }
  ```

---

_DELETE_ `two_factor_authentication` - выключить двух факторную аутентификацию

```
  нужно авторизоваться, как пользователь
```

- body:

  ```json
  {
    "otp": 000000 // всегда 6 символов
  }
  ```

<!-- если у пользователя не была она включена, но он передал otp ключ, то сервер вернет сообщение об успехе выключения -->

- answer:

  Status: **200** Deleted

  ```json
  {
    "message": "Two factor now is turn off"
  }
  ```

  Status: **400** Baq Request

  ```json
  {
    "message": "Incorrect data entered"
  }
  ```

---

_GET_ `two_factor_authentication` - получить qr code

```
  нужно авторизоваться, как пользователь
```

- answer:

  Status: **200** OK

  ```json
  {
    "data": {
      "qrcode2fa": "data:image/png;base64,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",
      "secret32": "JFJHSPCSOMZGKVBUKAYSIJJ2I5TUMURT"
    },
    "message": "QR code"
  }
  ```

  Status: **400** Not Found

  ```json
  {
    "message": "Incorrect data entered"
  }
  ```

---

## transactions

_POST_ `transactions` - добавить транзакции в базу данных

```
  нужно авторизоваться, как пользователь
```

- body:

  ```json
  {
    "network": "ethereum" // bnb или ethereum
  }
  ```

- answer:

  Status: **200** OK

  ```json
  {
    "message": "Was added 26 transactions" // или любое другое количество
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "No transactions found"
  }
  ```

---

_GET_ `transactions` - вывести транзакции

```
  нужно авторизоваться, как пользователь
```

- answer:

  Status: **200** OK

  ```json
  {
    "data": [ // может быть и []
      {
        "chain": "ethereum",
        "value": 0.48183659292477393,
        "currency": "ETH",
        "hash": "0x8b7ca2b488663da8fd09344bd61cbbc07c28f55008d8ebcaf35b85a086be9f17",
        "completed": true,
        "_id": "64073b6af710ad707f467d15"
      } ...
    ]
  }
  ```

  Status: **404** Not Found

  ```json
  {
    "message": "No transactions found"
  }
  ```

---
