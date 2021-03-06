# ai-lab-network
Сеть лаборатории по проблемам ИИ.

## Постановка задачи

Есть рабочие места ученых и вычислительные станции для моделирования. Необходимо реализовать удаленный доступ, веб-интерфейс для управления сетью. Необходимо обеспечить резервное копирование данных, обмен контактами, ведение проектов (контроль версий), автоматическое ведение инста-блога с новостями лаборатории.

## Выбор средств реализации

1) Клиент-серверная часть. Для ученых аккаунты. Всё вычислительное моделирование на бэкенде.
2) VPN для удаленного доступа. IPsec - часто используется для корпоративных сетей.
3) API инстаграма.
4) БД. PostgreSQL.
5) Инструмент Nessus для обнаружения уязвимостей.
6) GitHub для контроля версий.


## Предоставление доступа

Для каждого сотрудника предоставлен корпоративный аккаунт в системе Яндекс.Коннект для синхронизации контактов, общения, ведения проектов.
Часть сервисов (например, станция для моделирования) доступны лишь внутри VPN, поэтому для удаленного доступа также предоставленные параметры подключения к VPN и заведены ключи для удаленного доступа по SSH. Станции также предоставляют возможность подключиться в режиме удаленного рабочего стола по технологии VNC.

![изображение](https://user-images.githubusercontent.com/9255915/116287583-03810c80-a7aa-11eb-95b0-e700e6e272f5.png)

### VPN

Был выбрана технологии IKEv2/IPsec, т.к. она широко используется в корпоративных сетях и не имеет серьёзных уязвимостей, а также имеет реализации распространяемые свободно.

![Снимок экрана 2021-04-27 224859](https://user-images.githubusercontent.com/9255915/116288343-daad4700-a7aa-11eb-9898-efe1d45c6eee.png)


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

## PostgreSQL
Необходимо хранить хорошо структурированные данные, поэтому выбор падает на самую продвинутую и гибкую РСУБД. PostgreSQL полностью соответствовует SQL-стандартам ANSI/ISO.
PostgreSQL отличается от других РСУБД тем, что обладает объектно-ориентированным функционалом, в том числе полной поддержкой концепта ACID (Atomicity, Consistency, Isolation, Durability).
Будучи основанным на мощной технологии Postgres отлично справляется с одновременной обработкой нескольких заданий. Поддержка конкурентности реализована с использованием MVCC (Multiversion Concurrency Control), что также обеспечивает совместимость с ACID.

### Преимущества выбранной СУБД:
* Надёжность и целостность данных
* Полная SQL-совместимость
* PostgreSQL можно программно расширить за счёт хранимых процедур
* Большое количество поддерживаемых типов данных

### Защита от sql-инъекций
В системе должна быть предусмотрена защита от sql-инъекций. 
Презентация с полезным материалом [SQL Injection от А до Я](https://www.ptsecurity.com/upload/corporate/ru-ru/analytics/PT-devteev-Advanced-SQL-Injection.pdf).

## API instagram
В системе предусмотрено автоматическое ведение инста-блога с новостями лаборатории. Для реализации данной функции используется взаимодействие с API инстаграма. Система автоматически делает изображение и посылает post-запрос для публикации. Всё остальное управление аккаунтом происходит в ручном режиме, поскольку автоматическая генерация постов не предполагает отслеживание и контроль от сотрудников лабаратории. Перед отправкой изображение сохраняется в ImageJPEG качество 100, иначе инстаграм вернет ошибку. При разработке ипользуется [документация instagram](https://developers.facebook.com/docs/instagram-api/guides/content-publishing).

![image](https://user-images.githubusercontent.com/68510263/116348752-162a2e80-a808-11eb-9c78-5c35f455e182.png)


## Контроль версий

Для более удобного управления процессом коллективной разработки была выбрана распределённая система контроля версий Git. Был выбран веб-сервис, основанный на этой системе, GitHub.
Этот выбор обусловлен наличием опыта работы с ним, большая популярность (сервисом пользуются более 1,5 млн компаний, включая Apple, Amazon, Google и Microsoft),
а также наличие средств для организации процесса разработки разными разработчиками в разных должностях, например, управление поставленными задачами (создание, распределение и делегирование, изменение статуса и т.п.)

Конкретно была выбрана коммерческая версия GitHub - GitHub Enterprice. Данная версия предоставляет доступ к функционалу по автоматическому поиску уязвимостей в коде и жизненном цикле, больше разнообразных средств автоматизации (например, автоматизация пайплайна, гибкий CI/CD), и многое другое.
По статистике самого сайта GitHub Enterprice за последние три года, его использование уменьшило время написания программного кода на 45 минут в каждые сутки, уменьшило время на адаптацию к разработке на 40%, а денежные инвестиции вернулись в размере более 600%.

Репозитории на веб-сервисе будут закрытым и доступны только владельцам корпоративных аккаунтов, дабы скрыть исходный код от других организаций.
Он будет храниться на облаке на серверах GitHub.

## Исполнители

* Кудрявцев Максим
* Ханов Дамир
* Ошев Кирилл
* Акименкова Ирина
