# Тестовое задание PHP разработчика
## О проекте
Постарался выполнить в соотвествии с заданием.

Возможно не учёл требования по производительности, понятие 1000000 пользователей размытое.
 Для проекта использовал Yii2, т.к. осваиваю его в рамках саморазвития. БД Postgres 9.4. Не использовал какого либо вида кеширование, в рамках тестого заданяи считаю избыточным. Тестировал на глаз.

Сейчас приложение крутится на домене badcoder.ru, тачка домашняя Core 2 Duo 2.66, 6Gb ОЗУ, Debian 7

Админка badcoder.ru/admin

- login: admin
- password: KZTgCD!--!_-Epx_

## Требования к системе
- Apache2
- php 5.5
- Postgres 9.4
- Postgres Extension PostGIS
- Postgres Extension Gist
- composer
- git

## Установка
```sh
git clone https://github.com/novikovsergey/doublegis.git
composer install
```
После настроить подключение к БД, затем:
```sh
 ./yii migrate
 ./yii generator/generate
```
Генератор тестовых данных на больших обьёмах работае очень медленно, т.к. я ограничился ActiveRecord, по хорошему надо передлать, на массовые вставки данных в БД.
## Методы
# 1. buildings
Метод выводит список всех зданий
#### Параметры 
не имеет
#### Описание ответа
список обьектов здания

#### Поля:
- id - идентификатор БД
- address - строка с адресом
- location - координаты на плоскости в формате "x,y"
#### Пример запроса
- badcoder.ru/buildings
#### Пример ответа
```json
{
	id: 1,
	address: "821295, Калужская область, город Клин, ул. Славы, 20",
	location: "-95.175643,82.44479"
}
```
# 2. rubrics
Метод возвращает всех родителей для заданных потомков, если параметры не указаны дерево возвращается целиком
#### Параметры

- ids - Идентификаторы вершин перечисленные через запятую  для которых вернуть родителей, по умолчанию не задан
#### Описание ответа
 список обьектов, произвольной вложенности

- id - идентификатор 
- title - строка название
- parent_id - идентификатор родителя
- subrubrics - массив вложенных обьектов

#### Пример запроса
- badcoder.ru/rubrics?ids=10,25

#### Пример ответа

```json
{
	id: 10,
	title: "tenetur",
	parent_id: null,
	subrubrics: [ ]
},
{
	id: 11,
	title: "deleniti",
	parent_id: null,
	subrubrics: [
		{
		id: 20,
		title: "aut",
		parent_id: 11,
		subrubrics: [
			{
			id: 25,
			title: "tempora",
			parent_id: 20,
			subrubrics: [ ]
			}
		]
		}
	]
}
```
# 3. companies

Метод возвращает список компаний подходящих запросу, ограничение вывода 1000 компаний

#### Параметры

- ids - Идентификаторы необходимых компаний перечисленные через запятую 
- building_id - Идентификатор здания в котором находятся компании
- rubric_ids -  Идентификаторы  перечисленные через запятую рубрик к которым относятся компании, компании  выводятся для всех наследников указанных рубрик
- radius - Параметр описывает окружность в формате "x,y,R" в которую попадают компании
- envelope - Параметр описывает прямоугольную область в формать "x_min,y_min,x_max,y_max" в которую попадают компании.
- q - Строка поиска по названию компании

> Внимание параметры radius и envelope взаимоисключающие!
#### Описание ответа
список обьектов компаний

- id - идентификатор БД
- title - строка название компании
- address - адресс здания в котором находится компания
- phone - массив с телефонами компании
- rubrics - массив обьектов рубрик

#### Пример запроса

- badcoder.ru/companies?ids=10,25
- badcoder.ru/companies?rubric_ids=18
- badcoder.ru/companies?radius=10,-5,34.5&q=Текстиль
- badcoder.ru/companies?envelope=10,-5,14.5,13.768346&rubric_ids=78,3
- badcoder.ru/companies?building_id=10&q=гараж

#### Пример ответа
```json
{
	id: 6199,
	title: "ЗАО ЦементТяжХоз",
	address: "582927, Амурская область, город Одинцово, бульвар Будапештсткая, 08",
	phones: [
		"(812) 810-49-02",
		"+7 (922) 574-9010",
		"(812) 535-86-79",
		"(35222) 69-2934",
		"8-800-840-8476"
	],
	rubrics: [
		{
			id: 714,
			title: "Quae"
		},
		{
			id: 383,
			title: "Tempore"
		}
	]
},
```

## Коды ошибок методов
- 200 - Успешный запроса
- 400 - Забпрос составлен неверно