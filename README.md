# magisters-novsu
## Вопросы для подготовки к вступительным экзаменам в магистратуру НовГУ
### Инструкция по обновлению: 
    1. Делаете форк репозитория
    2. Отправляете свою ИЗМЕНЕННУЮ ветку в пул реквест основного репа
    3. Ждете апрува
    4. Аминь
    5. Заголовки под тегом '###'. Пример: '### Жизненный цикл БД'.


### 1. Задачи и этапы проектирования и создания базы данных. Жизненный цикл базы данных.

Проектирование базы данных включает в себя следующие шаги:

    а. Определение назначения
    б. Поиск и упорядочивание данных
    в. Разделение базы на сущности (т.е. отдельные таблицы)
    г. Распределение данных по строкам в таблицах.
    д. Настройка типов данных, связей между базами.

Жизненный цикл БД включает в себя 7 этапов:

    а. Предварительное планирование БД
    б. Проверка возможности существования (есть ли необходимые технологии для определения БД)
    в. Определение требований (цели, потребности, требования к обороудованию, ПО)
    г. Концептуальное проектирование (Создаются модели данных на основе пользовательских представлений,
      затем эти представлений складывают воедино 
       и получают че то вразумительное, ну или иными словами - концепцию БД)
    д. Логическое проектирование (Выбор типа модели данных, идет следом за концептуальным)
    е. Физическое проектирование (расширение модели данных с учетом всех факторов и рекомендаций для
       дальнейшего деплоя в сетку приложений)
    ж. Оценка и поддержка БД

### 4. Управление транзакциями. Модель транзакции. Свойства транзакции. Журнализация. Проблемы многопользовательских систем. Блокировки.

#### **Транзакция**

**Транзакция** - это последовательность операций над базой данных, рассматриваемых СУБД как единое целое.

Под управлением транзакциями понимается способность управлять различными операциями над данными, которые выполняются внутри реляционной СУБД. 

Управление транзакциями необходимо для поддержания логической целостности базы данных.

Пример транзакции (SQLite):

    BEGIN TRANSACTION;

    INSERT OR ROLLBACK INTO Orders (id_product, total)
    VALUES (2, 4800);

    UPDATE OR ROLLBACK Products
        SET quantity = quantity - 6
    WHERE id_product = 2;

    INSERT OR ROLLBACK INTO Invoices (id_order, id_product, num_order, sum_order, return, date)
    VALUES (1, 2, 6, 4800, 0, DATETIME('now'));

    COMMIT TRANSACTION;

#### **Модель транзакции**

**Модель транзакции** включает в себя следующие операторы:
    
1. **Оператор COMMIT**. Если все операции успешно выполнены, то транзакция также считается успешно выполненной и СУБД фиксирует (COMMIT) все изменения данных, произведенные этой транзакцией (то есть заносит изменения во внешнюю память).

2. **Оператор ROLLBACK**. Если же хотя бы одна операция транзакции заканчивается неудачей, то транзакция считается невыполненной и производится откат (ROLLBACK) — отмена всех изменений данных, произведенных в ходе выполнения транзакции, и возврат базы данных к состоянию до начала выполнения транзакции.

3. **Оператор SAVEPOINT**. Операция точки сохранения, которая предназначена для установки в транзакции особых точек, куда в дальнейшем может быть произведен откат (при этом отката всей транзакции не происходит).

Модель транзации можно видеть на следующей схеме:

<p align="center">
    <img src="pictures/4_1.jpg" style="width: 550px">
</p>

В этой схеме можно видеть следующие варианты работы транзакции:

1. Оператор COMMIT означает успешное завершение транзакции; его использование делает постоянными изменения, внесенные в базу данных в рамках текущей транзакции;

2. Оператор ROLLBACK прерывает транзакцию, отменяя изменения, сделанные в базе данных в рамках этой транзакции; новая транзакция начинается непосредственно после использования ROLLBACK;

3. Успешное завершение программы, в которой была инициирована текущая транзакция, означает успешное завершение транзакции (как будто был использован оператор COMMIT);

4. Ошибочное завершение программы прерывает транзакцию (как будто был использован оператор ROLLBACK).


#### **Свойства транзакции**

**Свойства транзакции** описываются аббревиатурой ACID (Atomicity, Consistency, Isolation, Durability):

1. **Свойство «Атомарность» (Atomicity)**. Атомарность гарантирует, что каждая транзакция будет выполнена полностью или не будет выполнена совсем. Не допускаются промежуточные состояния.

2. **Свойство «Согласованность» (Consistency)**. Данное свойство гласит, что транзакция, достигающая своего нормального завершения и, тем самым, фиксирующая свои результаты, сохраняет согласованность базы данных (то есть каждая успешная транзакция по определению фиксирует только допустимые результаты). Данное свойство «вытекает» из свойства атомарности.

3. **Свойство «Изолированность» (Isolation)**. Данное свойство означает, что транзакции параллельные транзакции не должны оказывать влияние на ее результаты.

4. **Свойство «Устойчивость» (Durability)**. Свойство означает, что если транзакция выполнена, и даже если в следующий момент произойдет сбой в системе, результат сохранится.

#### **Журнализация**

В случае аппаратных или программных сбоев может привести к повреждению данных в БД. Для восстановления информации в базе данных необходимо иметь некоторую дополнительную информацию. Таким образом, для поддержания надежности хранения данных требуется избыточность данных. Для таких целей существует **журнализация**.

**Журнал** хранит изменения, выполненные транзакциями, обеспечивает атомарность и устойчивость данных в случае сбоя системы.

**Журнал** содержит значения, которые данные имели до и после их изменения транзакцией. *Write-ahead log strategy* обязывает добавлять в журнал запись о предыдущих значениях до начала, а о конечных после завершения транзакции. В случае внезапной остановки системы БД читает лог в обратном порядке и отменяет изменения, сделанные транзакциями. Встретив прерванную транзакцию БД выполняет ее и вносит изменения о ней в журнал. Находясь в состоянии на момент сбоя, БД читает лог в прямом порядке и возвращает изменения, сделанные транзакциями.

#### **Проблемы многопользовательских СУБД**

Естественным следствием развития СУБД является проблема организации совместной работы нескольких пользователей с одной и той же совокупностью данных, или, проблемы многопользовательского доступа к данным.

Если в многопользовательской системе не реализованы механизмы совместной работы нескольких пользователей, то в СУБД это может привести к следующим проблемам:

1. **Проблема «последнего изменения»**. Возникает, когда несколько пользователей изменяют одну и ту же строку, основываясь на ее начальном значении; тогда часть данных будет потеряна, так как каждая последующая транзакция перезапишет изменения, сделанные предыдущей. Выход из этой ситуации заключается в последовательном внесении изменений;

2. **Проблема «грязного чтения»**. Возможна в том случае, если пользователь выполняет сложные операции обработки данных, требующие множественного изменения данных перед тем, как они обретут логически верное состояние. Если во время изменения данных другой пользователь будет считывать их, то может оказаться, что он получит логически неверную информацию. Для исключения подобных проблем необходимо производить считывание данных после окончания всех изменений;

3. **Проблема «неповторяемого чтения»**. Является следствием неоднократного считывания транзакцией одних и тех же данных. Во время выполнения первой транзакции другая может внести в данные изменения, поэтому при повторном чтении первая транзакция получит уже иной набор данных, что приводит к нарушению их целостности или логической несогласованности;

4. **Проблема «чтения фантомов»**. Появляется после того, как одна транзакция выбирает данные из таблицы, а другая вставляет или удаляет строки до завершения первой. Выбранные из таблицы значения будут некорректны.

Решением проблемы параллельной обработки БД заключается в том, что строки таблиц блокируются, а последующие транзакции, модифицирующие эти строки, отвергаются и переводятся в режим ожидания. Решается это с помощью блокировок.

#### **Блокировки**

**Блокировкой** называется временное ограничение на выполнение некоторых операций обработки данных. Блокировка может быть наложена как на отдельную строку таблицы, отдельную таблицу, так и на всю базу данных.

Управлением блокировками на сервере СУБД занимается менеджер блокировок, контролирующий их применение и разрешение конфликтов.

Транзакции и блокировки тесно связаны друг с другом. Транзакции накладывают блокировки на данные, чтобы обеспечить выполнение требований ACID. Без использования блокировок несколько транзакций могли бы изменять одни и те же данные.

Различают следующие типы блокировок:

1. По степени доступности данных: разделяемые и исключающие;
2. По множеству блокируемых данных: строчные, страничные, табличные;
3. По способу установки: автоматические (не явные) и явные;
4. По логике реализации: пессиместическая и оптимистическая.

Пример блокировки (MS SQL):

    LOCK TABLES users WRITE;
    INSERT INTO users (name) VALUES (‘Вася’);
    UNLOCK TABLES;

Работа с транзакциями есть и в моем проекте «Автоматизация извлечения информации о клиентах для базы 1С с применением машинного обучения». В моем случае, транзакция применяется когда пользователь захочет записать данные по всем найденным клиентам в БД. В этом случае, все временно созданные объекты пытаются записаться в транзакции. Если какой-то из объектов не удалось записать, тогда вся транзакция отменяется.

    НачатьТранзакцию();

    Попытка
        Для Каждого ТекущийОбработанныйОбъект Из ОбъектыОбработки Цикл
            ТекущийОбработанныйОбъект.ОбменДанным.Загрузка = Истина;
            ТекущийОбработанныйОбъект.Записать();
        КонецЦикла;

        ЗафиксироватьТранзакцию();
    Исключение
        ОтменитьТранзакцию();
    КонецПопытки;

Также в работе применяются и блокировки. Выполнить блокировку в 1С можно с помощью специального объекта встроенного языка 1С. Сделано это следующим образом:

	БлокировкаДанных = Новый БлокировкаДанных;
	
	ЭлементБлокировки = БлокировкаДанных.Добавить("Документ.ML_ФреймОбработкиДанных");
	ЭлементБлокировки.Режим = РежимБлокировкиДанных.Исключительный;
	ЭлементБлокировки.УстановитьЗначение("МодельМашинногоОбучения", ТекущаяМодельМашинногоОбучения);

	БлокировкаДанных.Заблокировать();

Блокировка в моей работе необходима для того, чтобы никто из пользоватей не мог редактировать дата сет по нужной модели машинного обучения, пока данная модель обучается или производится оценка ее точности.

Источники:

1. **Web SQL Database** - www.w3.org
2. **SQLite Documentation** - www.sqlite.org/docs.html
3. **MS SQL Documentation** - www.learn.microsoft.com/ru-ru/sql/sql-server/
4. **Базы данных : учеб, пособие для вузов** - Кузин А. В.
5. **Основы SQL : учеб, пособие для вузов** - Полякова Л. Н.

### 6. Организация процессов обработки данных в базе данных: формы, запросы, отчеты.

#### **База данных (БД)**

База данных (БД) — это имеющая название совокупность данных, которая отражает состояние объектов и их отношений в рассматриваемой предметной области. Данными называют зарегистрированную информацию, представление фактов, понятий или инструкций в форме, которая подходит для передачи, связи, обработки человеком или с помощью машины. Содержимое базы данных — прайс-листы, контакты пользователей, каталоги товаров, отчеты, статистика продаж и т. д. Изменения одной ячейки автоматически влияют на другие [1].

#### **Организация процессов обработки данных**

Организация процессов обработки данных в базах данных предполагает использование различных инструментов, таких как формы, запросы и отчеты. Каждый из этих инструментов служит определенной цели в организации процессов обработки данных.

Обработка данных включает набор различных функций и операций, которые можно разделить на три группы:
•   поиск, фильтрация и сортировка данных;
•   запросы к базе данных;
•   механизм реализации событий, правил и процедур в базе данных.

#### **Формы**

Формы — это инструмент для ввода и извлечения данных из баз данных. Формы обеспечивают удобный интерфейс для ввода и поиска данных. Формы разработаны так, чтобы отражать структуру данных в базе данных, упрощая и повышая эффективность ввода и извлечения данных. Формы можно настраивать в соответствии с конкретными потребностями организации, позволяя добавлять или удалять поля в зависимости от вводимых или извлекаемых данных.

Разрабатывая приложение, можно задавать значения всех свойств компонентов с помощью форм. При этом требуемые значения либо непосредственно вводятся в поле, либо выбираются в раскрывающихся списках. В последнем случае приложение создается с помощью мыши и не требует набора каких-либо символов на клавиатуре.

Компоненты, используемые для работы с таблицей БД, их основные свойства и значения этих свойств:

<p align="center">
    <img src="pictures/6_1.jpg" style="width: 550px">
</p>

*Например, при разработке базы данных для своего проекта VR-симуляции я изменял данные с помощью форм, так как в ходе разработки приложения определялись более подходящие варианты значений текстовых заголовков, описаний. Изменение данных с помощью форм подходящий вариант для такого случая, так как изменения данных происходили часто, но они не настолько значительные, чтобы организовывать для них новый запрос вручную.*

<p align="center">
    <img src="pictures/6_2.jpg" style="width: 550px">
</p>

#### **Запросы**

Запросы — это инструмент, который позволяет пользователям извлекать данные из базы данных на основе определенных критериев. Запросы позволяют пользователям создавать отчеты и отвечать на вопросы о данных в базе данных. Запросы настраиваются, что позволяет пользователям указывать поля для извлечения и критерии для извлечения данных. Запросы можно сохранять и использовать повторно, что упрощает создание отчетов и ответов на вопросы о данных в базе данных.
Формирование запросов в СУБД может осуществляться в специальном редакторе (командный режим) или через наглядно-диалоговые средства (конструкторы) и пошаговые мастера формирования запросов. Сформированный запрос в виде SQL-инструкции сохраняется в файле базы данных и впоследствии специальной командой СУБД может открываться на выполнение.

С точки зрения решаемых информационных задач и формы результатов исполнения запросов их можно разделить на три группы:
•   запросы на выборку данных;
•   запросы на изменение данных;
•   управляющие запросы.

1.  Пример запроса на выборку:
    SELECT title, price FROM goods
2.  Пример запроса на изменение данных:
    UPDATE goods SET price = price / 2 WHERE price >= 100
3.  Пример управляющего запроса:
    CREATE TABLE “Виджеты”

Запросы на выборку относятся к одному из наиболее часто применяемых видов запросов. Данный вид запросов реализуется SQL-инструкцией SELECT с предложением FROM. Результатом исполнения запроса на выборку является набор данных, который представляет временную таблицу данных со структурой (поля, их типы и параметры), определяемой параметрами запроса и параметрами полей таблиц, из которых выбираются данные. Результаты запросов на выборку помещаются в специальную временную таблицу, размещаемую на период исполнения («открытия») запроса в оперативной памяти.
Важное значение для решения различных технологических информационных задач по ведению базы данных имеют запросы на изменение данных. В отличие от непосредственного ввода данных в режимах открытой таблицы или формы они вносят изменения сразу в группу записей за одну операцию. Таким образом, результатом запросов на изменение является не набор данных, как в запросах на выборку, а изменение данных в самих таблицах.
При исполнении запроса на удаление за одну операцию осуществляется удаление группы записей из одной или нескольких таблиц. Запросы на удаление реализуются SQL-инструкцией DELETE.

*Например, при разработке базы данных для своего проекта я реализовал запрос-выборку записей из таблицы “Виджеты”, где поле “Количество слайдов” равно 1, так как такие виджеты можно создать при запуске приложения без инициализации.*

<p align="center">
    <img src="pictures/6_3.jpg" style="width: 550px">
</p>

#### **Отчеты**

Отчеты — это инструмент для представления данных из базы данных в осмысленной и лаконичной форме. Отчеты формируются на основе данных, извлеченных из базы данных посредством запросов. Отчеты можно настроить так, чтобы они включали определенные поля и параметры форматирования данных, такие как графики, таблицы и диаграммы. Отчеты можно распечатать или отправить в электронном виде в различных форматах, таких как PDF, Excel или HTML.
Для создания отчёта необходимо выбрать источник записей. Источником записей для отчета может быть таблица, именованный или внедренный запрос. Источник записей должен содержать все строки и столбцы данных, которые требуется отобразить в отчете.
Отчёты, в отличие от форм, имеют дополнительные инструменты для улучшения восприятия данных. Примеры инструментов: добавление группировки, сортировки и итогов; выделение данных с помощью условного форматирования; настройка цвета и шрифтов; добавление логотипа или фонового рисунка.

<p align="center">
    <img src="pictures/6_4.jpg" style="width: 550px">
</p>

Используя формы, запросы и отчеты, организации могут эффективно организовать свои процессы обработки данных, упрощая и повышая эффективность ввода, поиска и анализа данных. Используя эти инструменты, организации могут улучшить свои процессы принятия решений и получить представление о своих данных, что позволит им принимать обоснованные решения на основе данных в своей базе данных.

В заключение, формы, запросы и отчеты являются ключевыми инструментами для организации процессов обработки данных в базах данных. Каждый из этих инструментов служит определенной цели, помогая организациям эффективно вводить, извлекать и анализировать данные из своих баз данных. Используя эти инструменты, организации могут улучшить свои процессы принятия решений, получить представление о своих данных и в итоге достичь своих стратегических целей.

Источники:
1. Ian Hickson, Web SQL Database, w3.org, https://www.w3.org/TR/webdatabase/
2. SQLite Documentation - www.sqlite.org/docs.html
3. MS SQL Documentation - www.learn.microsoft.com/ru-ru/sql/sql-server/
4. Базы данных: учебное пособие для вузов - Кузин А. В.
5. Основы SQL: учебное пособие для вузов - Полякова Л. Н.

### 9. Архитектура многопользовательских СУБД. Модель сервера баз данных. Сервер приложений. Трехуровневая модель.

#### **Архитектура многопользовательских СУБД.**

**Система управления базами данных (СУБД)** — совокупность
языковых и программных средств, предназначенных для создания, ведения
и совместного использования БД многими пользователями.

В зависимости от разделения функций между клиентом и сервером выделяются несколько типов архитектур систем БД, среди них:

#### **Модель сервера баз данных.**

Основу данной модели составляют следующие механизмы:
1. Механизм хранимых процедур - как средство программирования SQL-сервера;
2. Механизм триггеров - как механизм отслеживания текущего состояния информационного хранилища
3. Механизм ограничений на пользовательские типы данных, который иногда называется механизмом поддержки доменной структуры.

В этой модели бизнес-логика разделена между клиентом и сервером.
- На сервере бизнес-логика реализована в виде хранимых процедур — специальных программных модулей, которые хранятся в БД и управляются непосредственно СУБД.
- Клиентское приложение обращается к серверу с командой запуска хранимой процедуры, а сервер выполняет эту процедуру и регистрирует все изменения в БД, которые в ней предусмотрены. По итогу, сервер возвращает клиенту данные, релевантные его запросу, которые требуются клиенту либо для вывода на экран, либо для выполнения части бизнес-логики, которая расположена на клиенте.
    
Централизованный контроль в модели сервера баз данных выполняется с использованием механизма триггеров. Триггеры также являются частью БД. Механизм использования триггеров предполагает, что при срабатывании одного триггера могут возникнуть события, которые вызовут срабатывание других триггеров. Этот мощный инструмент требует тонкого и согласованного применения, чтобы не получился бесконечный цикл срабатывания триггеров.

В данной модели сервер является активным, потому что не только клиент, но и сам сервер, используя механизм триггеров, может быть инициатором обработки данных в БД. И хранимые процедуры, и триггеры хранятся в словаре БД, они могут быть использованы несколькими клиентами, что существенно уменьшает дублирование алгоритмов обработки данных в разных клиентских приложениях. Данную модель поддерживают большинство современных СУБД.

Модель сервера баз данных можно видеть на следующей схеме:

<p align="center">
    <img src="pictures/9_1.jpg">
</p>

#### **Сервер приложений. Трехуровневая модель.**

Эта модель является расширением двухуровневой модели. Данная архитектура предполагает наличие дополнительного сервера приложений, который проводит предварительную обработку запросов клиентов, формирует запросы к серверу БД и обрабатывает полученные результаты перед отправкой их клиенту.

В этой модели компоненты приложения делятся между тремя исполнителями:

1. Клиент обеспечивает логику представления, включая графический пользовательский интерфейс, локальные редакторы; клиент может запускать локальный код приложения клиента, который может содержать обращения к локальной БД, расположенной на компьютере-клиенте. Клиент исполняет коммуникационные функции front-end части приложения, которые обеспечивают доступ клиенту в локальную или глобальную сеть. Дополнительно реализация взаимодействия между клиентом и сервером может включать в себя управление распределенными транзакциями, что соответствует тем случаям, когда клиент также является клиентом менеджера распределенных транзакций.

2. Серверы приложений составляют новый промежуточный уровень архитектуры. Они спроектированы как исполнения общих незагружаемых функций для клиентов. Серверы приложений поддерживают функции клиентов как частей взаимодействующих рабочих групп, поддерживают сетевую доменную операционную среду, хранят и исполняют наиболее общие правила бизнес-логики, поддерживают каталоги с данными, обеспечивают обмен сообщениями и поддержку запросов, особенно в распределенных транзакциях.

3. Серверы баз данных в этой модели занимаются исключительно функциями СУБД: обеспечивают функции создания и ведения БД, поддерживают целостность реляционной БД, обеспечивают функции хранилищ данных (warehouse services). Кроме того, на них возлагаются функции создания резервных копий БД и восстановления БД после сбоев, управления выполнением транзакций и поддержки устаревших (унаследованных) приложений (legacy application).

Преимущества трехуровневой модели наиболее заметны в тех случаях, когда клиенты выполняют сложные аналитические расчеты над базой данных. 

Трехуровнивую модель можно видеть на следующей схеме:

<p align="center">
    <img src="pictures/9_2.jpg">
</p>

Трехуровневая модель применяется и в моем проекте «Автоматизация извлечения информации о клиентах для базы 1С с применением машинного обучения». Применяется она следующим образом: вся бизнес-логика реализована на сервере 1С, который может выполнять различные запросы на добавления, чтения, изменения или удаления данных на сервер БД. Результаты работы с БД сервер 1С отправляет пользователям, которые используют тонкий клиент 1С. Результаты работы отображаются в виде изменных форм приложения или различных сообщений, отчетов и прочего.

Схему работы можно видеть ниже:

<p align="center">
    <img src="pictures/9_3.jpg">
</p>

1. **Web SQL Database** - www.w3.org
2. **SQLite Documentation** - www.sqlite.org/docs.html
3. **MS SQL Documentation** - www.learn.microsoft.com/ru-ru/sql/sql-server/
4. **Базы данных : учеб, пособие для вузов** - Кузин А. В.
5. **Основы SQL : учеб, пособие для вузов** - Полякова Л. Н.


### 11. Основные алгоритмические структуры. Операторы условного и безусловного перехода. Циклические структуры, включая цикл foreach.

#### ***Основные алгоритмические структуры.***

**Алгоритмическая структура** - это структура, где каждая часть предназначена для выполнения определенного алгоритма преобразования информации 

Логическая структура любого алгоритма может быть представлена комбинацией трех базовых структур: *следование*, *ветвление*, *цикл*.

Самая простая из них - это **следование**. **Следование** — это часть алгоритма, в которой все команды исполняются одна за другой в порядке их записи. Алгоритм, в котором есть структура «следование» называется линейным. действие_1 действие_2 ... действие_n.

Схема:

<p align="center">
    <img src="pictures/11_1.svg" style="background: white; width: 150px">
</p>

Пример (Си):

    int a = 5;
    int b = 10;
    int c = a + b;

#### ***Операторы условного и безусловного перехода.***

Следующая группа алгоритмических структур - операторы условного и безусловного перехода.

**Ветвление** – это выбор действия в зависимости от выполнения какого-нибудь условия. Алгоритм, в котором есть структура «ветвление» называется разветвляющимся. 

Структура ветвление существует в четырех основных вариантах:

1. **если–то**; в этом случае выполнится действие (или группа действий), расположенное в строке то только при соблюдении условия. Если же условие не соблюдается, то исполнитель перейдет к выполнению действий, следующих за служебным словом все.

    Схема:

    <p align="center">
        <img src="pictures/11_2.svg" style="background: white; width: 150px">
    </p>

    Пример (Си):

        if (a > b) {
            return a;
        }

2. **если–то-иначе**; здесь представлена полная форма ветвления. В полной форме в зависимости от условия в строке если выполняется только одно из действий ( или группа действий), расположенных либо в строке то (условие соблюдено), либо в строке иначе (условие не соблюдено).

    Схема (Общая):

    <p align="center">
        <img src="pictures/11_3.svg" style="background: white; width: 400px">
    </p>

    Пример (Си):

        if (a > b) {
            return a;
        } else {
            return b;
        }

    Или пример с множественным ветвлением (Си):

        if (a > b) {
            return a;
        } else if (b > a) {
            return b;
        } else {
            return c;
        }

3. **Оператор множественного выбора**; Данный оператор сопоставляет значение заданного выражения с заранее предусмотренными вариантами. В большинстве языках реализован через оператор switch. Любую конструкцию выбора можно переписать в стандартное ветвление.

    Схема:

    <p align="center">
        <img src="pictures/11_4.svg" style="background: white; width: 500px">
    </p>

    Пример (Си):

        switch (keyCode) {
            case 1:
                // Действия для значения 1.
                break;
            case 2:
                // Действия для значения 2.
                // Обратите внимание, что нет оператора break;
                // Поэтому при выполнения case 2 выполнится case 3.
            case 3:
                // Действия для значения 3.
                break;
            default:
                // Действия, если не сопоставилось не один case.
                break;
        }

**Оператор безусловного перехода**. Оператор безусловного перехода goto означает «перейти к» и применяется в случаях, когда после выполнения некоторого оператора надо выполнить не следующий по порядку, а какой-либо другой, отмеченный меткой, оператор. Все языки программирования имели в свое время, а некоторые и до сих пор предлагают оператор goto, чье имя образовано слиянием двух английских слов "go to" (перейти к).

Применение оператора безусловного перехода считается «плохим тоном», по причине того, что данный оператор может привести к созданию нечитаемого «спагетти-кода» (очень запутанного кода).

Пример (Си):

    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            if (i == 3) {
                goto exit_jump;
            }
        }
        printf("%i\n", i);
    }

    exit_jump: printf("exit");

#### ***Циклические структуры, включая цикл foreach.***

И наконец, циклическая группа.

**Цикл** – это неоднократное повторение каких–либо действий. Алгоритм, в котором есть структура «цикл» называется циклическим. Обеспечивает многократное выполнение некоторой совокупности действий, которая называется телом цикла.

Основные разновидности циклов:

1. Цикл с предусловием; Цикл, который выполняется, пока истинно некоторое условие, указанное перед его началом. Это условие проверяется до выполнения тела цикла, поэтому тело может быть не выполнено ни разу (если условие с самого начала ложно). В большинстве процедурных языков программирования реализуется оператором while, отсюда его второе название — while-цикл.

    Схема:

    <p align="center">
        <img src="pictures/11_5.svg" style="background: white; width: 150px">
    </p>

    Пример (Си):

        int a = 0;

        while (a++ < 5) {
            printf("%i\n", a);
        }

2. Цикл с постусловием; Цикл, в котором условие проверяется после выполнения тела цикла. Отсюда следует, что тело всегда выполняется хотя бы один раз.

    Схема:

    <p align="center">
        <img src="pictures/11_6.svg" style="background: white; width: 150px">
    </p>

    Пример (Си):

        int a = 1;

        do {
            printf("%i\n", a);
        } while (a++ < 5);

3. Цикл со счетчиком; Цикл, в котором некоторая переменная изменяет своё значение от заданного начального значения до конечного значения с некоторым шагом, и для каждого значения этой переменной тело цикла выполняется один раз. В большинстве процедурных языков программирования реализуется оператором for, в котором указывается счётчик (так называемая «переменная цикла»), требуемое количество проходов (или граничное значение счётчика) и, возможно, шаг, с которым изменяется счётчик.

    Схема:

    <p align="center">
        <img src="pictures/11_7.svg" style="background: white; width: 250px">
    </p>

    Пример (Си):

        for (int i = 1; i <= 5; i++) {
            printf("%i", i);
        }

4. Цикл foreach; Цикл, задающий выполнение некоторой операции для объектов из заданного множества, без явного указания порядка перечисления этих объектов. Такие циклы называются совместными (а также циклами по коллекции, циклами просмотра) и представляют собой формальную запись инструкции вида: «Выполнить операцию X для всех элементов, входящих во множество M».

    Пример (Python):

        lst = [1, 2, 3, 4, 5]

        for lst_elem in lst:
            print(lst_elem)

Все приведенные алгоритмические структуры и используются в моем проекте «Автоматизация извлечения информации о клиентах для базы 1С с применением машинного обучения».

Пример ветвления из проекта (Встроенный язык 1С):

	Если ЭтоДокумент Тогда
		СтандартныеРеквизитыЗаполнены	= ЗначениеЗаполнено(ОбрабатываемыйОбъект.Дата);
		СтандартныйРеквизитОтказаЗаписи	= "Дата";
	Иначе
		СтандартныеРеквизитыЗаполнены	= ЗначениеЗаполнено(ОбрабатываемыйОбъект.Наименование);
		СтандартныйРеквизитОтказаЗаписи	= "Наименование";
	КонецЕсли;

Источники:

1. **WebAssembly Core Specification** - www.w3.org
2. **The GNU C Reference Manual** - www.gnu.org/software/gnu-c-manual
3. **Информатика и программирование. Основы информатики** - Н. И. Парфилова.
4. **Алгоритмы и структуры данных** - Гагарина Л. Г.

### 18. UMLдиаграммы: классов, вариантов использования, деятельности, последовательностей, состояний.

UML – унифицированный язык моделирования. UML - это система обозначений, которую можно применять для объектно-ориентированного анализа и проектирования.
Его можно использовать для визуализации, спецификации, конструирования и документирования программных систем.

    а. Диаграмма классов - структурная диаграмма языка моделирования UML, демонстрирующая общую структуру иерархии классов системы
    б. Диаграммы вариантов использования описывают взаимоотношения и зависимости между группами вариантов использования
       и действующих лиц, участвующими в процессе.
    в. Диаграмма деятельности позволяет более детально визуализировать конкретный случай использования. Это поведенческая диаграмма,
       которая иллюстрирует поток деятельности через систему. Диаграммы активности UML также могут быть использованы
       для отображения потока событий в бизнес-процессе.
    г. Диаграммы последовательности - диаграмма, на которой для некоторого набора объектов на единой временной оси показан
        жизненный цикл объекта (создание-деятельность-уничтожение некой сущности) и взаимодействие акторов 
        (действующих лиц) информационной системы в рамках прецедента.
    д. Диаграмма состояний описывает процесс изменения состояний только одного класса, а точнее – одного экземпляра
       определенного класса, т. е. моделирует все возможные изменения в состоянии конкретного объекта.

### 19. Рекурсивные функции и данные. Пример рекурсивного алгоритма. Достоинства рекурсии и недостатки.

Рекурсия — это поведение функции, при котором она вызывает сама себя. Такие функции называются рекурсивными.
В отличие от цикла, они не просто повторяются несколько раз, а работают «внутри» друг друга.

Недостатки.

      а. Скорость работы рекурсивного кода. Некоторые компиляторы могут преобразовывать хвостовую
         рекурсию в цикл, но, в целом, итеративные реализации работают быстрее практически во
         всех случаях, когда использование циклов НЕ усложняет алгоритм.
      б. требования к размеру стека, но это решается настройками компиляции или выбором языка,
         который не ограничивает размер стека

Достоинства.

      а. Эффективность применения. Некоторые алгоритмы выглядят проще и понятнее с использованием рекурсии.
      б. Рекурсии легче реализовывать, чем итерации.

Пример:

      Вычисление факториала:

      def factorial(x):
          if (x == 0):
              return 1
          return x * factorial(x - 1)

      factorial(5)

    



