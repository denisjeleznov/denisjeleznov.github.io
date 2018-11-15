## Базовые понятия. Объекты vs Структуры данных

Структуры данных - можно сравнить со стандартными блоками конструктора LEGO. Они реализуют хорошо известные (часто используемые) абстракции и формируют базу для реализации Объектов. 

Работа программиста (написание кода программы) заключается в том, чтобы сформулировать решение задачи на "человеческом" языке а затем - трансформировать это решение в компьютерный язык (реализовать Объекты и их взаимодействие через методы).

Объекты **скрывают** структуру своих данных. Структуры - напротив, позволяют работать со своими данными **напрямую**.

ArrayList и LinkedList из JDK, формально, являются объектами классов, однако, по сути, это структуры - работа с ними **предполагает знание внутреннего устройства**.


## Базовые понятия. Модули vs Компоненты
Модули это **артефакты языка** или среды разработки. Они дают разработчику возможность группировать логически связанный код для повторного использования, в частности, для использования в **различных** системах. 

Компоненты это законченные и готовые к использованию в условиях конкретной информационной системы **единицы поставки функционала**. Как правило, "в чистом виде" (без доработок) компоненты **не переиспользуются** между различными системами.

Итого: 
* модуль это единица переиспользования кода на стадии разработки
* компонент это единица поставки фунционала на стадии сопровождения
* модуль это способ объединения кода в целях упрощения повторного использования
* компопент это способ разделения функционала в целях упрощения незавимой поставки
* зависимости между модулями, как правило, возникают compile time
* зависимости между компонентами, как правило, возникают runtime

Например, library - библиотека кода для повторного использования является модулем, а OrderService - компонент, обрабатывающий заказы в соответствии с бизнес-правилами конкретной информационной системы, является компонентом.

Одна из важнейших задач Архитектора - разделить будущую систему на компоненты и определить границы между ними. Для этого можно применить аналогию из физики - сильное взаимодействие  (обеспечивает устойчивость атомных ядер). В мире ИТ аналогом сильного взаимодействия можно считать бизнес-транзакции. Соответственно, границы между компонентами логично провести по границе бизнес-транзакций (транзакция должна полностью "помещаться" внутри одного компонента. Как следствие - разработка архитектуры должна начинаться с изучения бизнес-процессов с целью сбора информации о бизнес-транзакциях.


## Базовые понятия. События vs Сообщения
Событие (event) связывает **конкретный источник** события с **анонимным получателем** (обработчиком события). Как правило, событие характеризуется источником и типом.

Сообщение (message) связывает любого (возможно, анонимного) **Отправителя** с **конкретным Получателем** (адресатом сообщения). Как правило, сообщение характеризуется Получателем и телом (полезной нагрузкой). Факт того, что событие однозначно связано с Получателем позволяет реализовать последовательную обработку (обработка следующего сообщения может ссылаться на состояние, хранящееся в Получателя, которое было сформировано при обработке предыдущего сообщения).

Обработка событий может порождать отправку сообщений и наоборот.

События и сообщения являются основными носителями информации в EDA - событийно ориентированной архитектуре. Этот подход позволяет достичь максимальной изоляции компонентов системы. Однако, высокая степень изоляции существенно осложняет реализацию функционала, который требует синхронизации между компонентами (например, реализация новой бизнес-транзакции). Поэтому, в EDA нужно либо особенно тщательно подходить к вопросу определения границ между компонентами системы либо - заранее предусмотреть роль координатора (учитывая, что он повышает не желательную связность становится "единой точкой отказа").


## Базовые понятия. Concurrency vs Parallelism
Concurrency is an aspect of the problem domain — your program needs to handle multiple simultaneous (or near-simultaneous) events. Parallelism, by contrast, is an aspect of the solution domain.

Concurrency is not a way to make a program run faster. It is not a complex juggling trick that ninja coders use to keep multiple balls in the air at one time. On the contrary, it is the apparent sequential execution of a program that is the complex trick. Sequential execution is an illusion maintained by a cabal of compiler writers and hardware architects. Concurrency is simply the relaxation of a fabricated constraint.”

(C) Meike, G. Blake. “Android Concurrency”

Таким образом, современный runtime практически всегда предполагает конкурентное выполнение кода. В этом смысле, Concurrency напоминает Р-компонент из знаменитой CAP-теоремы. Они оба - "данность" современного мира разработки ПО (должно работать в многоядерном runtime и уметь горизонтально масштабироваться по множеству узлов - быть partition tolerance).


## Функциональное программирование vs Объектно ориентированное в условиях современного runtime
Выбор подхода к разработке ПО для современной конкурентной среды выполнения (concurrency runtime) в первую очередь зависит от способа работы с данными.

ООП предполагает **совместный** доступ к данным внутри объекта из методов объекта. Конкурентная среда выполнения создаёт условия для **одновременного** доступа к данными, что приводит к  возникновению неопределённостей типа "гонки" (race condition). Устранение неопределённости требует синхронизации что существенно влияет на производительность (фактически, "засинхронизированный" многопоточный код выполняется медленнее однопоточного).

В функциональном подходе данные представляются в "математическим" смысле (результат работы функции не может изменить её аргументы) а работа с ними напоминает конвейер. В этом подходе, практически, отсутствует проблема совместного доступа к данным, однако, требуются дополнительные ресурсы на обеспечение независимости данных (много операций копирования - больше нагрузка на подсистему управления памятью).

Таким образом, становится очевидным причина возобновления интереса к функциональному подходу для создания систем, эффективно работающих в современных runtime.


## Проблема координации изменений кода
Возникает в любом проекте естественным образом в результате процесса повышения энтропии (усложнения системы).

Для решения проблемы координации изменений нужно поддерживать слабую связность между компонентами системы. Достичь этого можно двумя подходами:
1. Архитектурой - например, микросервисной, либо - модуляризацией монолита
2. Инженерными практиками - например, CI-автоматизацией, обеспечивающей обратную связь

## Определение Архитектуры (по ГОСТ)
Архитектура - принципиальная организация системы, воплощённая в её элементах, способах их взаимоотношений  друг с другом и со средой а также - принципы, направляющие развитие системы.

Неожиданно для меня, ГОСТ-определение архитектуры ПО оказалось лидером по количеству смысла на единицу текста.


## Основное требование к архитектуре ПО
Исторически, программное обеспечение (softwate vs hardware) появилось в ответ на требование обеспечить гибкость - легко и быстро менять поведение машин. Для обеспечения гибкости, нужно как можно дольше иметь как можно больше вариантов развития (эволюции) системы.

Таким образом, хорошая архитектруа должна **обеспечивать высокую скорость эволюции информационной системы**, что достигается за счёт:
1. предоставления разнообразия вариантов использования
2. простоты внесения изменений, тестирования, развёртывания и сопровождения

Поэтому, при разработке архитектуры основной акцент внимания уделяется не реализации функциональных требований на текущий момент а обеспечению развития (эволюции) системы во времени.

**Архитектура** программной системы — это **форма**, которая образуется делением системы на **компоненты**.  

Любую программную систему можно, условно, разделить на два основных компонента:              
1. ядро (бизнес-логика). 
2. оболочка (взаимодействие бизнес-логики с окружением).

Цель архитектора — создать такую форму, которая сделает ядро самым важным компонентом, а оболочку — не зависимой от ядра. Это позволит откладывать и задерживать принятие решений об оболочке для того, чтобы накопить информацию (во время работы над ядром) для обоснованного выбора.


## Источник вдохновения для проектирования информационной системы
Поиск метафоры в "мире механизмов" (механических систем) обречён на провал из-за того, что механические системы, как правило, сочетают в себе те черты, которых нужно избегать при проектировании информационных систем - хрупкость и жёсткость.

Аналогии нужно искать в биологии.


## Принципы проектирования и сопровождения высоконагруженной системы:

### Принципы проектирования:
1.	Разбиение на блоки – распределение нагрузки
2.	Резервирование подсистем – устойчивость к внутренним отказам
3.	Автономность – устойчивость к отказам внешних систем

### Принципы развёртывания:
1.	Плавность – последовательное внедрение (пилотный блок, основной блок, резервный блок)
2.	Рубильники – возможность оперативного включения/отключения/переключения версии индивидуально для каждого изменения
3.	Обратная совместимость изменений


## Надёжность распределённой системы. Паттерн "Наблюдение"
Система считается надёжной, если сбой в одном или нескольких её компонентов не приводит к отказу - система продолжает предоставлять сервис с заявленными параметрами качества.

Один из шаблонов для реализации надёжной системы - "Наблюдение" (supervision) предполагает эскалацию "проблем" при вызове сервиса его "Наблюдателю" (supervisor). В данном контексте, к "проблемным" относятся ситуации, возникновения системных (инфраструктурных) ошибок, например, потери соединения с БД или внешним сервисом. 

Применение шаблона "Наблюдение" позволяет отделить кодовую базу обработки ошибок бизнес-логики от логики обработки системных ошибок. При использовании этого шаблона важно чётко "соблюдать водораздел" - "Наблюдатель" может управлять жизненным циклом объектов наблюдения, но не должен знать о деталях реализуемой ими бизнес-логики.

Шаблон "наблюдение" широко используется в модели программирования, основанной на акторах, а также - в решениях для балансировки нагрузки.


## 100% надёжность не требуется реальной информационной системе
Ни один пользователь не сможет убедиться и оценить 100% надёжную систему из-за того, что для доступа к ней используется большое количество промежуточных систем (WiFi, DNS, провайдеры,...) с надёжностью ниже 100%.

Промежуточные системы привносят случайные сбои, дискредитирующие надёжность целевой системы.

Кроме того, нужно учитывать, что активность по усилению  надёжности, как правило, влияет на скорость внесения изменений - тормозит развитие системы.

Управление надёжностью информационной системы предполагает выявление основных рисков и управление ими а не стремление повысить количество "девяток".


## Компромисс между доступностью и согласованностью распределённой системы
Информация в физическом мире распространяется с конечной скоростью - изменения в распределённой системе не могут моментально появиться во всех её частях. CAP-теорема, фактически, постулирует это ограничение в отсутствии возможности создать распределённую систему со 100% доступностью и согласованностью.

Доступность и согласованность реальной распределённой системы это компромисс с ограничениями физического мира, финансовыми рисками и удобством использования. 

Например, в случае отсутствия связи с банком, банкомат не откажет в выдаче денег (сервис доступен), но ограничит максимально запрашиваемую сумму (в рамках установленных финансовых рисков).

Другой пример - CRDT-структуры данных, использованием которых предполагает, что система доступна, но периодически находится в не согласованном состоянии.


## Количественные характеристики доступности/качества обслуживания
Качество обслуживания информационной системы характеризуют три SL*-параметра:
1. SLI - service level indicator - описывает измеримые для внешнего наблюдателя параметры работы системы (время отклика, пропускная способность, количество ошибок,...)
2. SLO - service level objective - целевой показатель для SLI
3. SLA - service level agreement - последствия выхода SLI за пределы SLO


## Изолированная согласованность
Реализация строгой (в каждой точке, в любой момент времени) согласованности в распределённой системе не достижима из-за конфликта требования по доступности с конечной скоростью распространения информации.

Изолированная согласованность это компромиссное решение, которое предполагает поддержание согласованности только в рамках отдельных частей системы - "автономных доменов". 

Изолированная согласованность достижима за счёт того:
1.	что данные внутри домена не распределены
2.	транзакции не выходят за рамки домена.

Одна из задач, решаемых в ходе разработки архитектуры распределённой системы - выделение "автономных доменов" данных. Как правило, границы доменов определяются границами **бизнес**-транзакций.

Для перемещения данных между "автономными доменами" используется паттерн "сага".


## Cluster vs Distributed application
Кластер, по сути, представляет собой группу "сильно связанных узлов", совместно представляющих собой одну "виртуальную" машину.

Таким образом, система, предназначенная для работы на кластере, может разрабатываться "как обычная" (не распределённая).

Распределённая система должна изначально разрабатываться с учётом проблем (доступность, согласованность данных, ...), возникающих при взаимодействии между слабо связанными узлами.


## "Жизнь" после распределённых транзакций (по мотивам [статьи](http://adrianmarriott.net/logosroot/papers/LifeBeyondTxns.pdf) Pat Helland)
Идея, лежащая за различными механизмами распределённых транзакций, заключается в предоставлении разработчику абстракции глобального строгого порядка (global serializability).

Абстракция глобального строгого порядка (global serializability) позволяет атомарно изменять  данные, распределённые по множеству узлов распределённой системы.

Распределённые транзакции не доступны для создания масштабируемых распределённых систем из-за их несовместимости с требованием по обеспечению высокой доступности.

Строгий порядок операций в масштабируемой распределённой системе достигается только внутри ограниченной области видимости (scope) - внутри одного узла сети (или кластера).

Масштабируемые распределённые системы строятся вокруг абстракции сущности (entity), которая представляет собой хранимое состояние (произвольной структуры) адресуемое по уникальному идентификатору. Например, сущность может быть файлом или набором записей в различных таблицах, адресуемых по единому уникальному идентификатору. 

В целях достижения масштабируемости, распределённая система накладывает следующие ограничения на сущности:
1. Строгий порядок операций и атомарные транзакции ограничены границами одной сущности - при этом сущность может адресоваться только уникальным (постоянным) идентификатором - (синхронизация между идентификатором и альтернативным индексом не масштабируется).
2. Данные сущностей не должны пересекаться
3. Возможность сущности перемещаться между узлами сети накладывает ограничение на участие нескольких сущностей в одной транзакции.

Для построения успешной масштабируемой распределённой системы, разработчики бизнес-процессов, должны  проектировать бизнес-процессы вокруг абстракции сущность (entity) и знать об ограничениях с этим связанных (см. выше). В частности - отсутствие синхронизации между состоянием различных сущностей должно учитываться в бизнес-процессах.

Гибкость масштабируемой распределённой системы обеспечивается её "слоистой" структурой, предполагающей наличие двух слоёв - верхний и нижний.  Верхний слой этой структуры реализует бизнес-логику, манипулирующую внутренней структурой сущностей. Нижний слой обеспечивает масштабирование, доставляя сообщения и распределяя сущности по узлам сети в зависимости от нагрузки на систему и доступности узлов.

Примером слоистой архитектуры можно считать систему, построенную поверх кластера узлов. Код нижнего слоя формирует из множества узлов единую среду выполнения, внутри которой работает код верхнего слоя.

Информация в распределённой системе передаётся в виде событий и сообщений. Как правило, события передаются с гарантией доставки "максимум один раз" (at most once),  а сообщений - "как минимум один раз" (at least once). Кроме того, события и сообщения могут передаваться с нарушением порядка (out of order).

Сообщения, как правило,  передаются между сущностями и адресуются уникальными идентификаторами сущностей. Для корректной обработки (предотвращения дублирования) с учётом доставки сообщений at least once и out of order, сущности хранят в себе "состояние партнёра" (pathner activity).

Шаблоны проектирования распределённой масштабируемой системы:
1. Entity - определяет границы согласованного (атомарного) изменения данных
2. Workflow - машина-состояний для решения "проблемы неопределённости" (отсутствия синхронизации между состояниями различных Entity). Entity хранит в workflow свою версию состояния для каждого "партнёра"
3. Tentative opration - "оптимистичное" выполнение, предполагающее возможность отмены - ещё один инструмент для решения "проблемы неопределённости". 
4. Activity - механизм хранения состояние. используется для решения проблем идемпотентности и неопределённости. Entity использует аctivity для хранения своего представления и состоянии партнёров и отслеживания сообщений, принятых от партнёров.


## Распределённая блокировка
Традиционно, для организации монопольного доступа к ресурсу используются блокировки.

В случае распределённых систем эффективная реализация блокировки требует дополнительной проверки на стороне стороне ресурса - он должен проверять не только наличие маркера доступа но и его версию.

Например, узел А получает маркер доступа к ресурсу, запускает операцию и... впадает в ступор, например, из-за начавшейся сборки мусора. За это время маркер доступа узла А к ресурсу "протухает" и доступ к ресурсу получает узел B. 

Через некоторое время, узел А "просыпается" и решает продолжить операцию с ресурсом (состояние которого уже было изменено узлом B) - возникает конфликт.

Для того, чтобы избежать конфликта, при обработке запроса ресурс должен проверять не только наличие маркера доступа, но и его версию и откатывать операции с "протухшей" версией маркера.


## Latency vs Response Time vs Service Time
Latency - время ожидания. Это время которое запрос ожидает начала его обработки. Ожидающий запрос, обычно, находится в очереди на соответствующем слое абстракции (очередь входящих соединений сокета, очередь необработанных запросов сервиса и т.д.). 

Service Time -  чистое время, необходимое системе для обработки запроса. 

Response Time - время отклика. Время от посылки запроса до получения ответа. Измеряется на стороне клиента.

Эти времена часто отображаются в виде процентилей. На практике для приближённого расчёта процентилей используются алгоритмы [forward decay](http://dimacs.rutgers.edu/~graham/pubs/papers/fwddecay.pdf), [t-digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf) и [HdrHistogram](https://github.com/HdrHistogram/HdrHistogram).


## Балансировка нагрузки (по мотивам [GitHub Network Load Balancer](https://githubengineering.com/glb-director-open-source-load-balancer/))
### ECMP
Routers have a feature called Equal-Cost Multi-Path (ECMP) routing, which is designed to split traffic destined for a single IP across multiple links of equal cost. ECMP works by hashing certain components of an incoming packet such as the source and destination IP addresses and ports. By using a consistent hash for this, subsequent packets that are part of the same TCP flow will hash to the same path, avoiding out of order packets and maintaining session affinity.

### L4/L7 split design
At the L4 tier, the routers use ECMP to shard traffic using consistent hashing to a set of L4 load balancers - typically using software like ipvs/LVS. LVS keeps connection state, and optionally syncs connection state with multicast to other L4 nodes, and forwards traffic to the L7 tier which runs software such as haproxy. We call the L4 tier “director” hosts since they direct traffic flow, and the L7 tier “proxy” hosts, since they proxy connections to backend servers.

This L4/L7 split has an interesting benefit: the proxy tier nodes can now be removed from rotation by gracefully draining existing connections, since the connection state on the director nodes will keep existing connections mapped to their existing proxy server, even after they are removed from rotation for new connections. Additionally, the proxy tier tends to be the one that requires more upkeep due to frequent configuration changes, upgrades and scaling so this works to our advantage.

If the multicast connection syncing is used, then the L4 load balancer nodes handle failure slightly more gracefully, since once a connection has been synced to the other L4 nodes, the connection will no longer be disrupted.


## DevOps как способ устранения конфликта Разработки и Сопровождения
Разработка заинтересована в том, чтобы иметь возможность как можно чаще "выкатывать" изменения. Любые изменения снижают стабильность системы.

Сопровождение заинтересовано в том, чтобы система работала (была стабильной).

Таким образом, Разработка и Сопровождение имеют противоположные точки зрения в вопросе управления жизненным циклом системы. Это неизбежно приводит к конфликту.

DevOps-подход предполагает привлечение Разработки к решению задач Сопровождения. Это приводит к тому, что Разработка делает систему более устойчивой к сбоям и удобной в сопровождении.

В рамках DevOps-подхода Разработка совместно с Сопровождением обеспечивают развитие системы поддерживая её стабильность в соответствии с SLA, который удовлетворяет бизнес-требованиям.
