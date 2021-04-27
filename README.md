# ai-lab-network
Сеть лаборатории по проблемам ИИ.

## Постановка задачи

Есть рабочие места ученых и вычислительные станции для моделирования. Необходимо реализовать удаленный доступ, веб-интерфейс для управления сетью. Необходимо обеспечить резервное копирование данных, обмен контактами, ведение проектов (контроль версий), автоматическое ведение инста-блога с новостями лаборатории.

## Выбор средств реализации
1) API инстаграма.
2) БД. PostgreSQL (защита от sql-запросов).
3) Клиент-серверная часть. Для ученых аккаунты. Всё вычислительное моделирование на бэкенде.
4) VPN для удаленного доступа. IPsec - часто используется для корпоративных сетей.
5) GitHub для контроля версий.
6) Инструмент Nessus для обнаружения уязвимостей.

## Топология сети
![image](https://user-images.githubusercontent.com/46625865/116261950-92355f80-a791-11eb-9ad0-3390c43328b9.png)

Основными критериями при выборе архитектуры служили: 
1. Отказоустойчивость
2. Возможность разделения различных групп учёных
3. Ценовая обоснованность

Была выбрана 2х уровневая архитектура.

Рассмотрим для начала самый простой вариант организации сети.

![image](https://user-images.githubusercontent.com/46625865/116267984-50f37e80-a796-11eb-9935-ad4c3d326612.png)

В этом случае при разрыве соединения между роутером и маршрутизатором теряется доступ в интернет для всей сети. Для обеспечения защиты от такого вида отказа у нас появляется ещё один "уровень" с маршрутизаторами. Мы соединяем наши маршрутизаторы 1го уровня со всеми маршрутизаторами второго уровня. Помимо этого у нас есть 2 соединения между маршрутизаторами второго уровня. За счёт этих соединений у нас значительно понижается вероятность того, что какая-то часть сети целиком выйдет из строя.

Исходя из всего, написанного выше, мы решили остановиться на данном виде топологии.

## Поиск уязвимостей

Для автоматизации процесса поиска уязвимостей в информационной системе была выбрана программа Nessus.
Она позволяет обнаружить множество часто встречающихся видов изъянов в системе.
Nessus предоставляет удобный интерфейс просмотра обнаруженных проблем.
Этот сканер может быть неудобен для новичков, но необходимость более детальных настроек говорит о его универсальности и более гибких возможностях, эффективное использование которых требует профессиональных знаний.
Сканер поддерживается разработчиками, и его база уязвимостей получает обновления каждую неделю.

![изображение](https://user-images.githubusercontent.com/39821724/116273611-5606fc80-a79b-11eb-8db9-ea536c9b70a6.png)


## Контроль версий

Для более удобного управления процессом коллективной разработки была выбрана распределённая система контроля версий Git. Был выбран веб-сервис, основанный на этой системе, GitHub. Этот выбор обусловлен
наличием опыта работы с ним,
большая популярность (сервисом пользуются более 1,5 млн компаний, включая Apple, Amazon, Google и Microsoft),
а также наличие средств для организации процесса разработки разными разработчиками в разных должностях, например, управление поставленными задачами (создание, распределение и делегирование, изменение статуса и т.п.)

Конкретно была выбрана коммерческая версия GitHub - GitHub Enterprice. Данная версия предоставляет доступ к функционалу по автоматическому поиску уязвимостей в коде и жизненном цикле, больше разнообразных средств автоматизации (например, автоматизация пайплайна, гибкий CI/CD), и многое другое.
По статистике самого сайта GitHub Enterprice за последние три года, его использование уменьшило время написания программного кода на 45 минут в каждые сутки, уменьшило время на адаптацию к разработке на 40%, а денежные инвестиции вернулись в размере более 600%.

Репозиторий на веб-сервисе будут закрытым, дабы скрыть исходный кот от других организаций.
Он будет храниться на облаке на сервере нашего предприятия.

## Исполнители

* Кудрявцев Максим
* Ханов Дамир
* Ошев Кирилл
* Акименкова Ирина
