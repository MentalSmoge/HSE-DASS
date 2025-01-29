# Лабораторная работа №4
### Тема:
Проектирование REST API
### Цель работы:
Получить опыт проектирования программного интерфейса.
## Документация по API
### 1. Users
#### 1.1 POST /users
**Метод:** POST  
**Описание:** Создать нового пользователя.  
**Запрос:**  
- Тело запроса:
  - name: имя пользователя

**Ответ:**
- Код ответа: 201 Created / 400 Bad Request (если не правильное тело запроса)
- Тело ответа: JSON-объект созданного пользователя / Сообщение об ошибке

**Пример ответа:**  
Код ответа 201
```json
{
    "id": 3,
    "name": "test"
}
```
Код ответа 400  
**"field" is not allowed**
#### 1.2 GET /users/{id}
**Метод:** GET  
**Описание:** Получить информацию о пользователе по его ID.  
**Запрос:**  
- Параметры URL: {id} — ID пользователя.

**Ответ:**
- Код ответа: 200 OK / 404 Not Found (если не найден)
- Тело ответа: JSON-объект пользователя / Сообщение об ошибке

**Пример ответа:**  
Код ответа 200  
```json
{
    "id": 1,
    "name": "sample name"
}
```
Код ответа 404  
**Пользователь не найден**

### 2. Projects
#### 2.1 POST /projects
**Метод:** POST  
**Описание:** Создать новый проект.  
**Запрос:**  
- Тело запроса:
  - owner_id: ID пользователя
  - title: название проекта

**Ответ:**
- Код ответа: 201 Created / 400 Bad Request (если не правильное тело запроса)
- Тело ответа: JSON-объект созданного проекта / Сообщение об ошибке

**Пример ответа:**  
Код ответа 201
```json
{
    "id": 2,
    "owner_id": 1,
    "title": "best title"
}
```
Код ответа 400  
**"owner_id" is required**

#### 2.2 GET /projects/{id}
**Метод:** GET  
**Описание:** Получить информацию о проекте по его ID.  
**Запрос:**  
- Параметры URL: {id} — ID проекта.

**Ответ:**
- Код ответа: 200 OK / 404 Not Found (если не найден)
- Тело ответа: JSON-объект проекта / Сообщение об ошибке

**Пример ответа:**  
Код ответа 200  
```json
{
    "id": 2,
    "owner_id": 1,
    "title": "sample title"
}
```
Код ответа 404  
**Проект не найден**
### 3. Boards
#### 3.1 POST /boards
**Метод:** POST  
**Описание:** Создать новую доску.  
**Запрос:**  
- Тело запроса:
  - project_id: ID доски
  - title: название доски

**Ответ:**
- Код ответа: 201 Created / 400 Bad Request (если не правильное тело запроса)
- Тело ответа: JSON-объект созданной доски / Сообщение об ошибке

**Пример ответа:**  
Код ответа 201
```json
{
    "id": 2,
    "owner_id": 1,
    "title": "best title"
}
```
Код ответа 400  
**"owner_id" is required**
#### 3.2 GET /boards/{id}
**Метод:** GET  
**Описание:** Получить информацию о доске по её ID.  
**Запрос:**  
- Параметры URL: {id} — ID доски.

**Ответ:**
- Код ответа: 200 OK / 404 Not Found (если не найден)
- Тело ответа: JSON-объект проекта / Сообщение об ошибке

**Пример ответа:**  
Код ответа 200  
```json
{
    "id": 123,
    "project_id": 2,
    "title": "sample board"
}
```
Код ответа 404  
**Доска не найдена**
#### 3.3 PUT /boards/{id}
**Метод:** PUT  
**Описание:** Обновить информацию о доске по её ID.  
**Запрос:**  
- Параметры URL: `{id}` — ID доски.
- Тело запроса:
  - `project_id`: ID проекта (обязательное поле)
  - `title`: название доски (минимум 3 символа)

**Ответ:**
- Код ответа: 200 OK / 400 Bad Request (если не правильное тело запроса) / 404 Not Found (если доска не найдена)
- Тело ответа: JSON-объект обновленной доски / Сообщение об ошибке

**Пример ответа:**  
Код ответа 200  
```json
{
    "id": 123,
    "project_id": 2,
    "title": "updated board title"
}
```
Код ответа 404  
**Доска не найдена**

#### 3.4 DELETE /boards/{id}
**Метод:** DELETE  
**Описание:** Удалить доску по ее ID.  
**Запрос:**  
- Параметры URL: `{id}` — ID доски.

**Ответ:**
- Код ответа: 204 No Content / 404 Not Found (если доска не найдена)
- Тело ответа: Пустое / Сообщение об ошибке

**Пример ответа:**  
Код ответа 204  
Код ответа 404  
**Доска не найдена**

### 4. Board Elements
#### 4.1 POST /boards/{id}/elements
**Метод:** POST  
**Описание:** Добавить новый элемент на доску.  
**Запрос:**  
- Параметры URL: `{id}` — ID доски.
- Тело запроса:
  - `board_id`: ID доски (обязательное поле)
  - `type`: тип элемента (обязательное поле)
  - `pos_x`: позиция по оси X (обязательное поле)
  - `pos_y`: позиция по оси Y (обязательное поле)

**Ответ:**
- Код ответа: 201 Created / 400 Bad Request (если не правильное тело запроса) / 404 Not Found (если доска не найдена)
- Тело ответа: JSON-объект созданного элемента / Сообщение об ошибке

**Пример ответа:**  
Код ответа 201
```json
{
    "id": 6123123,
    "board_id": 123,
    "type": 0,
    "pos_x": 13.62,
    "pos_y": 111.15
}
```
Код ответа 404  
**Board not found**

#### 4.2 GET /boards/{id}/elements
**Метод:** GET  
**Описание:** Получить все элементы доски по ее ID.  
**Запрос:**  
- Параметры URL: `{id}` — ID доски.

**Ответ:**
- Код ответа: 200 OK / 404 Not Found (если доска не найдена)
- Тело ответа: JSON-массив элементов доски / Сообщение об ошибке

**Пример ответа:**
Код ответа 200  
```json
[
    {
        "id": 6123123,
        "board_id": 123,
        "type": 0,
        "pos_x": 13.62,
        "pos_y": 111.15
    }
]
```
Код ответа 404  
**Доска не найдена**

#### 4.3 GET /boards/{id}/elements/{elementId}
**Метод:** GET  
**Описание:** Получить элемент доски по ее ID и ID доски.  
**Запрос:**  
- Параметры URL: 
  - `{id}` — ID доски.
  - `{elementId}` — ID элемента.

**Ответ:**
- Код ответа: 200 OK / 404 Not Found (если доска или элемент доски не найден)
- Тело ответа: JSON-объект элемент доски / Сообщение об ошибке

**Пример ответа:**
Код ответа 200  
```json
{
    "id": 2,
    "board_id": 123,
    "type": 0,
    "pos_x": 555.2,
    "pos_y": 123.13
}
```
Код ответа 404  
**Board not found**

#### 4.4 PUT /boards/{id}/elements/{elementId}
**Метод:** PUT  
**Описание:** Обновить позицию элемента на доске.  
**Запрос:**  
- Параметры URL: 
  - `{id}` — ID доски.
  - `{elementId}` — ID элемента.
- Тело запроса:
  - `type`: новый тип элемента (необязательное поле)
  - `pos_x`: новая позиция по оси X (необязательное поле)
  - `pos_y`: новая позиция по оси Y (необязательное поле)

**Ответ:**
- Код ответа: 200 OK / 404 Not Found (если доска или элемент не найдены)
- Тело ответа: Обновленный элемент / Сообщение об ошибке

**Пример ответа:**  
Код ответа 200  
```json
{
    "id": 2,
    "board_id": 123,
    "type": 0,
    "pos_x": 555.2,
    "pos_y": 123.13
}
```
Код ответа 404  
**Board Element not found**

#### 4.5 DELETE /boards/{id}/elements/{elementId}
**Метод:** DELETE  
**Описание:** Удалить элемент с доски по его ID.  
**Запрос:**  
- Параметры URL: 
  - `{id}` — ID доски.
  - `{elementId}` — ID элемента.

**Ответ:**
- Код ответа: 204 No Content / 404 Not Found (если доска или элемент не найдены)
- Тело ответа: Пустое / Сообщение об ошибке

**Пример ответа:**  
Код ответа 204  
Код ответа 404  
**Board Element not found**

## Тестирование API с помощью Postman
### 1. Users
#### 1.1 POST /users
![image](https://github.com/user-attachments/assets/13755f87-b931-42cb-8781-4b95f18e8a78)
![image](https://github.com/user-attachments/assets/4ca72d16-cc81-4271-9d89-389b196b56ff)

#### 1.2 GET /users/{id}
![image](https://github.com/user-attachments/assets/519ee4af-8b53-431c-85eb-a61866c033bd)

### 2. Projects
#### 2.1 POST /projects
![image](https://github.com/user-attachments/assets/1ed8577c-ae5c-4596-b3a8-45407121857c)
![image](https://github.com/user-attachments/assets/8f54ce38-a37f-476a-9d41-6d21c03b7580)

#### 2.2 GET /projects/{id}
![image](https://github.com/user-attachments/assets/e2b97694-0acc-4971-ab10-dead7594e0ca)
![image](https://github.com/user-attachments/assets/9f875a2c-81c3-4b26-b912-970b0dd6b624)

### 3. Boards
#### 3.1 POST /boards
![image](https://github.com/user-attachments/assets/e5ad1e8f-8e87-4938-99c0-ccd5f0ec94c6)
![image](https://github.com/user-attachments/assets/96f4306a-2d42-44fc-809a-46b7d3be0009)

#### 3.2 GET /boards/{id}
![image](https://github.com/user-attachments/assets/9f2a9232-87b6-4f7d-8bd5-b2efe5644ba1)

#### 3.3 PUT /boards/{id}
![image](https://github.com/user-attachments/assets/668808ed-51a2-4715-9231-b69fc23c5f47)
![image](https://github.com/user-attachments/assets/494b57ef-db55-4b03-b834-8f9cb7262108)

#### 3.4 DELETE /boards/{id}
![image](https://github.com/user-attachments/assets/6b44a8a9-dfe5-47fd-8fbf-5e0ea34b22cc)

### 4. Board Elements
#### 4.1 POST /boards/{id}/elements
![image](https://github.com/user-attachments/assets/b3959c12-04a5-4007-af8e-7a0eb8e2f297)
![image](https://github.com/user-attachments/assets/5a537b99-fa7a-4bd3-915d-8e1d23083ef5)


#### 4.2 GET /boards/{id}/elements
![image](https://github.com/user-attachments/assets/5ef7cfd5-53d6-46ce-90a9-a0110ebe39b5)

#### 4.3 GET /boards/{id}/elements/{elementId}
![image](https://github.com/user-attachments/assets/1a3d534c-7fa2-4034-bb3d-d532a5ce6f2d)

#### 4.4 PUT /boards/{id}/elements/{elementId}
![image](https://github.com/user-attachments/assets/6cae5422-83b8-45f2-8f27-e9722856bdf5)
![image](https://github.com/user-attachments/assets/70e99b98-f08a-465a-8f67-035496ba803f)

#### 4.5 DELETE /boards/{id}/elements/{elementId}
![image](https://github.com/user-attachments/assets/0bd2bd64-dc49-4497-960d-8f8991511127)

