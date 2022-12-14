# HSEDB_PROJECT

# Описание предметной области
## 1 корпоративный мессенджер
Основные сущности
1) Пользователь
2) Чат
3) Сообщение
4) Группа
5) Файл
# Проектирование моделей
## 2
### a) Концептуальная модель

![image](https://user-images.githubusercontent.com/54531224/204368472-0bbec43d-e9ec-45f5-b774-fa1411aaa88b.png)

### b) Логическая модель
База данных находится во 2й нормальной форме, в основном из-за проблем с сообщениями, которые, в обсуждении, могут иметь предыдущее. Делать отдельные таблицы для этого неэффективно, а вторая нормальная форма и так присутствует.
![image](https://user-images.githubusercontent.com/54531224/204379533-eed8035e-e0ca-4ffe-ad80-e0aa2321530d.png)



При пересылке сообщения мы бужем создавать новое а от старого оставлять только ссылку на пользователя от которого оно переслано.
### c) Физическая модель
User
| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK    | user_id               | уникальный идентефикатор пользователя                      | INTEGER     | NOT NULL |
|       | login                 | логин пользователя                                         | VARCHAR(30) | NOT NULL |
|       | name                  | имя пользователя, отображающееся остальным пользователям   | VARCHAR(50) | NOT NULL |

Chat

| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK    | chat_id               | уникальный идентефикатор чата                              | INTEGER| NOT NULL |
|       | name                 | название чата                                               | VARCHAR(30) | NOT NULL |
|       | type                  | Тип чата: канал, приватный чат или групповой чат           | TEXT | ANY(Array["CHANNEL", "PRIVATE", "PUBLIC"]) |
|       | member_count          | количество участников чата                                         | INTEGER | member_count > 0 |
|       | description                  | описание чата  | TEXT | - |

Group
| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK    | group_id               | уникальный идентефикатор пользователя                     | INTEGER     | NOT NULL |
|       | group_name                 | название группы                                       | VARCHAR(50) | NOT NULL |
|       | member_count                  | количество состоящих в группе   | INTEGER | member_count >= 0 |

message

| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK FK | message_id            | уникальный идентефикатор сообщения                         | INTEGER     | NOT NULL |
| FK    | sender_id             | автор сообщения                                            | INTEGEК     | NOT NULL |
| FK    | chat_id               | идентефикатор чата в котором сообщение было отправлено     | INTEGER     | NOT NULL |
|       | text                  | текст сообщения     | TEXT | - |
|       | prev_message_id       | идентефикатор сообщения, ответом на которое было послано данное, либо идентефикатор сообщения, которое было перелано, если оно было переслано | INTEGER | - |
|       | is_forwarded  | было ли переслано сообщение | BOOLEAN | NOT NULL|
|       | attachment_count      | количество прикрепленных вложений  | INTEGER | - |
|       | time_send             | время, когда сообщение было отправлено | TIMESTAMP | - |

Если сообщение переслано, то оно не может быть ответом на другое сообщение, поэтому такая структура будет работать.

group_member

| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK    | file_id               | уникальный идентефикатор файла        | INTEGER     | NOT NULL |
|       | file_link             | ссылка на файл              | TEXT | NOT NULL |
|       | file_size             | размер файла в байтах       | INTEGER | file_size > 0|

chat_member

| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK FK | user_id               | уникальный идентефикатор пользователя        | INTEGER     | NOT NULL |
| PK FK | chat_id                 | уникальный идентефикатор чата              | INTEGER | NOT NULL |
|       | chat_role | роль учатсника в чате | TEXT | Any(Array["admin", "subscriber", "member"]) |

group_member
версионируется как раз эта табличка, версионирование делаем SCD2
| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK FK | user_id               | уникальный идентефикатор пользователя        | INTEGER     | NOT NULL |
| PK FK | group_id                 | уникальный идентефикатор группы              | INTEGER | NOT NULL |
|       | added_timestamp | момент когда пользователя добавили в группу | TIMESTAMP | - |
|       | removed_timestamp | момент когда пользователя удалили из группы| TIMESTAMP | - |


group_chat

| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK FK | group_id               | уникальный идентефикатор группы        | INTEGER     | NOT NULL |
| PK FK | chat_id                 | уникальный идентефикатор чата              | INTEGER | NOT NULL |


attachment

| PK/FK | Название                | Описание | Тип данных| ограничение|
| ----- |:---------------------:| :---------------------------------------------------------:| :----------:|:--------:|
| PK FK | message_id               | уникальный идентефикатор сообщения        | INTEGER     | NOT NULL |
| PK FK | file_id                 | уникальный идентефикатор файла              | INTEGER | NOT NULL |


