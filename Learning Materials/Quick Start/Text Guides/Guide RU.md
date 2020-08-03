<div id="chapter0" class="section"></div>

## Введение
Платформа CUBA - это фреймворк с открытым кодом, цель которого - унификация разработки корпоративных приложений. Фреймворк сочетает в себе проверенные решения для построения информационных систем уровня предприятия и высокоэффективные инструменты разработки, все это значительно ускоряет процесс создания приложений.

Мы рассмотрим самые основы платформы, и разработаем простое, но полнофункциональное приложение для планирования докладов на конференции. Будут рассмотрены все этапы создания веб-приложений: проектирование модели данных и манипуляции с данными, создание сервисов для выполнения бизнес-логики и, наконец, разработка пользовательского интерфейса. В целом, полученных знаний будет достаточно для того, чтобы начать создавать свои собственные приложения с использованием фреймворка CUBA. В процессе мы будем использовать среду разработки <a target="_self" href="/tools/">CUBA Studio</a>. Пожалуйста, установите её на свой компьютер. Для работы с визуальными дизайнерами активируйте лицензию или ознакомительный режим.  

Репозиторий с готовым проектом: [https://github.com/cuba-platform/sample-session-planner](https://github.com/cuba-platform/sample-session-planner).
<div id="chapter1" class="section"></div>

## Создание проекта

<div class="timestamp-wrapper">
   <a id="stamp1" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">00:49</div>
  </div></a>
</div>

Используя меню CUBA Studio, создадим пустой CUBA-проект с именем SessionPlanner и пространством имен planner. Будем использовать Java 11 и последнюю стабильную версию платформы.

![](/images/learn/v14/qs-screen1.png)

Будем использовать указанную по умолчанию базу данных HSQLDB.

![](/images/learn/v14/qs-screen2.png)

После первого запуска студия предлагает активировать пробный период коммерческой подписки.

![](/images/learn/v14/qs-screen3.png)

Активируем коммерческую подписку на пробный период, который составляет 28 дней. Мы получим доступ к опциональным дизайнерам, которые упростят разработку приложения. 

<div id="chapter2"  class="section"></div>

## Создание модели данных

<div class="timestamp-wrapper">
   <a id="stamp2" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">02:42</div>
  </div></a>
</div>

Первый этап - создание сущностей модели данных. В нашей модели будет только два класса: Докладчик (```Speaker```) и Доклад (```Session```), связанные отношением один-ко-многим, т.е. каждый докладчик может читать несколько докладов. 

![](/images/learn/v14/qs-screen4.png)

Для начала создадим сущность Докладчик. Дизайнер сущности можно вызвать через ссылку на начальной странице проекта в IDE. 

![](/images/learn/v14/qs-screen5.png)

Второй способ - щелкнуть правой кнопкой мыши на узле **Data Model** в дереве проекта и выбрать в меню **New -> Entity**.

![](/images/learn/v14/qs-screen6.png)

Введите название сущности: ```Speaker``` и создайте атрибуты в соответствии с таблицей ниже: 

<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>Название атрибута</td>

<td>Тип</td>

<td>Обязательный</td>

<td>Другие ограничения целостности</td>

</tr>

<tr>

<td>firstName</td>

<td>String (255)</td>

<td>Да</td>

<td></td>

</tr>

<tr>

<td>lastName</td>

<td>String (255)</td>

<td></td>

<td></td>

</tr>

<tr>

<td>email</td>

<td>String (1024)</td>

<td>Да</td>

<td>Соответствие формату адреса электронной почты</td>

</tr>

</tbody>

</table>
</div>

В CUBA используются стандартные сущности JPA, их можно изменять, используя либо редактор кода, либо визуальный дизайнер. Мы используем второе. Щелкните по кнопке со значком “+” и добавьте атрибуты в сущность, CUBA Studio сгенерирует необходимые члены класса и расставит аннотации. 

![](/images/learn/v14/qs-screen7.png)
 
<div id="anch1"></div>
<p>В CUBA мы можем задать формат текстового отображения сущностей в пользовательском интерфейсе - имя экземпляра. Для докладчика мы выберем поля “Имя” и “Фамилия”.</p>

![](/images/learn/v14/qs-screen8.png)

Если посмотрим на Java код в дизайнере сущности, мы увидим, что создали обычный класс с JPA аннотациями. При необходимости, этот код можно исправить в текстовом редакторе, визуальный редактор отобразит эти изменения. 

Кроме того можно посмотреть оператор DDL для создания таблицы на вкладке **Preview** и, если необходимо, добавить индексы. Например, мы ожидаем много поисков по фамилии. Чтобы сделать поиск более эффективным, добавим индекс для фамилии. 

![](/images/learn/v14/qs-screen9.png)

Давайте продолжим и создадим сущность Доклад и свяжем ее с сущностью Докладчик. Спецификация атрибутов представлена в таблице ниже. Дату и время окончания доклада будем вычислять автоматически - плюс один час со времени начала доклада.

<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>Название атрибута</td>

<td>Тип</td>

<td>Обязательный</td>

</tr>

<tr>

<td>topic</td>

<td>String (255)</td>

<td>Да</td>

</tr>

<tr>

<td>startDate</td>

<td>LocalDateTime</td>

<td>Да</td>

</tr>

<tr>

<td>endDate</td>

<td>LocalDateTime</td>

<td></td>

</tr>

<tr>

<td>speaker</td>

<td>Связь с сущностью “Speaker”, тип связи - ассоциация, многие-к-одному</td>

<td>Да</td>

</tr>

<tr>

<td>duration</td>

<td>Integer</td>

<td>Да</td>

</tr>

<tr>

<td>description</td>

<td>String (2000)</td>

<td></td>

</tr>

</tbody>

</table>
</div>
Принципиальных различий по сравнению с первой сущностью нет, кроме ссылочного атрибута <code>speaker</code>. Это отношение - многие-к-одному, так что сделаем поле-ассоциацию. В интерфейсе значение этого атрибута следует выбирать из списка опций (ранее созданных докладчиков), а не вводить вручную. 

В итоге, определение атрибута ```speaker``` в визуальном дизайнере должно выглядеть так:

![](/images/learn/v14/qs-screen10.png)

<div id="chapter3" class="section"></div>

## Создание вычисляемого атрибута

<div class="timestamp-wrapper">
   <a id="stamp3" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">05:29</div>
  </div></a>
</div>

Теперь добавим вычисляемое поле - окончание доклада (```endDate```). Создадим геттер и назовем его getEndDate, поставим аннотацию ```@MetaProperty```. Для вычисляемого атрибута нужно определить поля, которые будут загружаться: начало доклада и длительность. Затем напишем алгоритм вычисления времени окончания доклада. 

```java
@Transient
@MetaProperty(related = {"startDate", "duration"})
public LocalDateTime getEndDate() {
   return (startDate != null && duration != null) ? startDate.plusHours(duration):null;
}
```    
Обратите внимание, что студия подсвечивает метод, потому что нужно определить текст атрибута для отображения на экранах. Нажмите **Alt+Enter** и добавьте текст. 

![](/images/learn/v14/qs-screen11plus.png)

На этом все. Объектная модель данных создана. 

<div id="chapter4" class="section"></div>

## Создание стандартных экранов

<div class="timestamp-wrapper">
   <a id="stamp4" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">06:30</div>
  </div></a>
</div>

В CUBA Studio есть генератор пользовательского интерфейса. С его помощью мы создадим простые, но полезные экраны: 
*   Браузер - для просмотра данных в табличной форме
*   Редактор - для редактирования одной строки данных

Сначала создадим экраны для работы с докладчиками. Сущность Докладчик довольно простая, поэтому для неё оставим настройки экранов по умолчанию. Запустите генератор, щелкнув на пункте **Create screen** меню **Screens** в верхней части дизайнера сущностей.

![](/images/learn/v14/qs-screen11.png)

Генератор экранов также можно запустить, щелкнув правой кнопкой мыши на сущности в дереве проекта и выбрав **New -> Screen** из контекстного меню.

![](/images/learn/v14/qs-screen12.png)

Для сущности Докладчик мы создадим браузер и редактор. Выберите пункт **Entity browser and editor screens** на вкладке **Screen Templates** мастера создания экранов и нажмите кнопку **Next**. Сущность Докладчик довольно простая, поэтому просто оставим значения по умолчанию для генерации.

![](/images/learn/v14/qs-screen13.png)

Видим, что в коде проекта каждый экран состоит из двух частей: контроллера, написанного на языке Java, который отвечает за логику работы экрана, и разметки в формате XML, которая отвечает за внешний вид и расположение элементов экрана. В нашем случае, браузер состоит из файлов ```speaker-browse.xml``` и ```SpeakerBrowse.java```, а редактор - ```speaker-edit.xml``` и ```SpeakerEdit.java``` соответственно. Файлы можно найти в разделе **Generic UI -> Screens** в дереве проекта CUBA. 

![](/images/learn/v14/qs-screen14.png)

Отметьте для себя раздел ```data``` в XML-дескрипторе - он определяет, как данные выбираются из базы данных.

```java
<data readOnly="true">
   <collection id="speakersDc"
               class="com.company.planner.entity.Speaker"
               view="_local">
       <loader id="speakersDl">
           <query>
               <![CDATA[select e from planner_Speaker e]]>
           </query>
       </loader>
   </collection>
</data>
```
Между контроллером, разметкой и ассоциированной сущностью можно быстро переключаться используя кнопки навигации в верхней части окна.

![](/images/learn/v14/qs-screen15.png)

![](/images/learn/v14/qs-screen16.png)

![](/images/learn/v14/qs-screen17.png)

<div id="chapter5" class="section"></div>

## Создание браузера и редактора докладов

<div class="timestamp-wrapper">
   <a id="stamp5" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">07:40</div>
  </div></a>
</div>

Теперь запустим мастер создания экранов для сущности Доклад. Tак же как и ранее, выберем раздел **Entity browser and editor screens**. Остановимся на шаге **Entity browser view**. 

В CUBA view - представление - определяет то, значения каких полей сущности извлекаются из базы данных.  Создавать представления можно в отдельных файлах, чтобы затем использовать в разных модулях приложения. Либо прямо в XML-дескрипторе.

В нашем примере мы определим представление при создании экрана, просто выбрав нужные данные.

![](/images/learn/v14/qs-screen18.png)

На следующем шаге необходимые поля уже выбраны. 

![](/images/learn/v14/qs-screen19.png)

Видим,что соответствующие поля добавлены на экраны.

![](/images/learn/v14/qs-screen20.png)

И определим длительность доклада по умолчанию - 1 час. Для этого подпишемся на событие ```InitEntity``` на вкладке **Handlers** в инспекторе компонента. 

![](/images/learn/v14/qs-screen21.png)

Пропишем значение в коде.

```java
@Subscribe
public void onInitEntity(InitEntityEvent<Session> event) {
   event.getEntity().setDuration(1);
}
```
<div id="chapter6" class="section"></div>

## Создание базы данных

<div class="timestamp-wrapper">
   <a id="stamp6" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">09:24</div>
  </div></a>
</div>

CUBA Studio генерирует SQL-скрипты, необходимые для создания и обновления БД, с учетом выбранной СУБД. По умолчанию, на начальных стадиях разработки в CUBA используется СУБД HSQL. Выберите **CUBA -> Generate Database Scripts** в главном меню и SQL-скрипты для создания базы данных будут сгенерированы. Далее вы можете посмотреть текст скриптов перед тем, как сохранить их в файловую систему как часть исходного кода проекта. Все скрипты для создания и изменения БД можно найти в разделе **Main Data Store** дерева проекта CUBA. 

![](/images/learn/v14/qs-screen22.png)

Полученные скрипты можно отредактировать и добавить выражения SQL, например, для создания дополнительных индексов или добавления данных, необходимых для начального запуска приложения. 

![](/images/learn/v14/qs-screen23.png)

Чтобы выполнить скрипты, нажмите кнопку **Create Database** в главном меню IDE. Помимо таблиц приложения, CUBA также создает системные таблицы для хранения информации о пользователях, ролях, привилегиях и т.д.

Вот и все. База данных готова, теперь можно создать простые экраны для выполнения базовых операций над данными. 

<div id="chapter7" class="section"></div>

## Первый запуск

<div class="timestamp-wrapper">
   <a id="stamp7" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">09:58</div>
  </div></a>
</div>

Чтобы запустить приложение, можно воспользоваться кнопкой на панели инструментов IDE.

![](/images/learn/v14/qs-screen24.png)

Также можно запустить его из главного меню **CUBA -> Start Application Server**. 

![](/images/learn/v14/qs-screen25.png)

После старта сервера приложение можно открыть в веб-браузере, используя URL, который показан в окне запуска. В нашем случае, это будет http://localhost:8080/app. После открытия будет выведено окно логина, имя пользователя - ```admin```,  пароль - ```admin```. 
Экраны приложения можно открыть из меню **Application**.
Давайте добавим тестовые данные: двух докладчиков и два доклада. В качестве эксперимента можно ввести неправильный email для докладчика, чтобы убедиться, что валидация работает как нужно. 

![](/images/learn/v14/qs-screen26.png)

![](/images/learn/v14/qs-screen27.png)

Сгенерированные при помощи мастера экраны хороши для базовых операций, однако, в реальной жизни интерфейс пользователя обычно более сложен. 

<div id="chapter8" class="section"></div>

## Разработка пользовательского интерфейса

<div class="timestamp-wrapper">
   <a id="stamp8" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">11:29</div>
  </div></a>
</div>

Давайте создадим календарное представление докладов в дополнение к их табличному представлению. Для этого в браузер добавим лист с двумя закладками, разместим на них календарь и таблицу и добавим функциональность для редактирования и переноса докладов.  

![](/images/learn/v14/qs-screen28.png)

Откроем дескриптор ```session-browse.xml``` и поместим таблицу с докладами на лист с закладками.

![](/images/learn/v14/qs-screen29.png)

Добавим еще одну закладку.

![](/images/learn/v14/qs-screen30.png)

На нее поместим календарь.

![](/images/learn/v14/qs-screen31.png)

Для компонента ```TabSheet``` установим свойство ```expanded``` в инспекторе компонента. Студия запрашивает ID. В CUBA ID нужны для того, чтобы потом на этот элемент можно было ссылаться.

![](/images/learn/v14/qs-screen32.png)

Назначим ID ```calendarTab``` и ```tableTab``` для закладок и заголовки ```Sessions calendar``` и ```Sessions table``` соответственно.

![](/images/learn/v14/qs-screen33.png)

Обновим календарь - назначим контейнер данных и развернем календарь на всю площадь закладки.

![](/images/learn/v14/qs-screen34.png)

Развернем таблицу с докладами.

![](/images/learn/v14/qs-screen35.png)

В CUBA компоненты пользовательского интерфейса могут быть связаны с сущностями и их атрибутами. 

Давайте свяжем календарь с коллекцией данных, которая загружается из БД в экран. Используйте поле поиска и свяжите:

- Свойство ```startDateProperty``` с датой и временем начала доклада
- Свойство ```endDateProperty``` с датой и временем окончания доклада
- Свойство ```captionProperty``` с темой доклада
- И свойство ```descriptionProperty``` с описанием доклада

![](/images/learn/v14/qs-screen36.png)

Пусть календарь показывает только рабочие часы.

![](/images/learn/v14/qs-screen37.png)

Мы можем использовать не только визуальный редактор экрана, но и редактор XML. Покажем кнопки навигации в календаре.

![](/images/learn/v14/qs-screen37_1.png)

Чтобы увидеть изменения, не нужно перезапускать приложение. CUBA поддерживает горячую перезагрузку экранов без остановки приложения. Если закрыть браузер докладов и открыть его, то мы увидим сделанные изменения. 

<div id="chapter9" class="section"></div>

## Использование экранного API

<div class="timestamp-wrapper">
   <a id="stamp9" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">13:41</div>
  </div></a>
</div>

Когда мы взаимодействуем с пользовательским интерфейсом, например, щелкая мышью по элементам, изменяя размеры экрана, вводя текст и т.д., генерируются специальные события. Фреймворк CUBA предоставляет API, который позволяет, подписавшись на эти события, определить реакцию на них. Давайте обработаем событие “щелчок левой кнопкой мыши” по докладу в календаре - мы будем вызывать редактор доклада. Для этого мы будем пользоваться API для работы с экранами, который также предоставляет фреймворк. 

В ```session-browse.xml``` выберите ```sessionsCalendar``` и перейдите на вкладку **Handlers** в окне **Component Inspector**. Выберите ```CalendarEventClickEvent``` и по стрелке перейдите в контроллер.

![](/images/learn/v14/qs-screen38.png)

Будет сгенерирован пустой метод. 

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
  
}
```

Нам нужно вызвать редактор. Будем использовать ```EditorScreenFacet```, который  дает возможность предварительной настройки экрана редактора.

Перейдем в ```session-browse.xml``` и на панели компонентов найдем <b>EditorScreen</b>, перетащим его на дерево компонентов.

![](/images/learn/v14/qs-screen39.png)

Set the following settings:

- ID - ```sessionEditDialog```;
- контейнер данных - ```sessionsDc```;
- режим редактирования - ```EDIT```;
- класс сущности - ```Session```;
- режим окна - ```DIALOG```;
- класс экрана - ```SessionEdit```.

![](/images/learn/v14/qs-screen40.png)

Вернемся в контроллер и добавим фасет. Для этого в контроллере экрана нажмите на кнопку **Inject** в верхней части окна редактора и выберите ```sessionEditDialog``` из секции Screen API во всплывающем окне.

Вызвать меню добавления сервиса (и подписки на события) можно, нажав комбинацию клавиш **Alt+insert** прямо в коде контроллера и выбрать **Inject** во всплывающем меню.

![](/images/learn/v14/qs-screen40_1.png)

Чтобы найти нужный сервис, можно просто начать печатать его название в диалоговом окне, а затем использовать кнопки “вверх” и “вниз”, чтобы перемещаться по списку доступных сервисов. 

![](/images/learn/v14/qs-screen40_2.png)

В редакторе мы будем использовать доклад, который пользователь выбирает щелчком мыши. После этого показываем экран редактора. 

Текст метода представлен ниже:

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
   sessionEditDialog.setEntityProvider(() -> (Session) event.getEntity());
   sessionEditDialog.show();
}
```

Теперь переоткрываем браузер докладов и вызываем редактор, щелкнув левой кнопкой мыши по докладу в календаре. 

![](/images/learn/v14/qs-screen41.png)

Как видим, необходимо поправить ширину и высоту редактора. В IDE откройте дескриптор экрана, выберите ```dialogMode``` в дереве компонентов и установите свойствам ```width``` и ```height``` значение ```auto```.

![](/images/learn/v14/qs-screen42.png)

Переключитесь в приложение и откройте редактор заново. Теперь он должен выглядеть гораздо лучше. 

![](/images/learn/v14/qs-screen43.png)

<div id="chapter10" class="section"></div>

## Добавление бизнес-логики

<div class="timestamp-wrapper">
   <a id="stamp10" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">15:29</div>
  </div></a>
</div>

В CUBA для реализации бизнес-логики существует механизм сервисов. Далее при помощи CUBA Studio мы создадим такой сервис и подключим его к экрану. Это будет сервис для изменения расписания докладов, который будет проверять, что у докладчика в день не более двух докладов.

Правой кнопкой мыши щелкнем на узле **Сервисы** в дереве CUBA проекта и выберем **New ->Service**. Появится мастер создания сервиса. Введем ```SessionService``` в качестве имени интерфейса. Название ```SessionServiceBean``` для реализации будет сгенерировано автоматически.

![](/images/learn/v14/qs-screen44.png)

Создадим метод ```rescheduleSession``` в интерфейсе как показано в коде ниже: 

```java
public interface SessionService {
   String NAME = "planner_SessionService";

   Session rescheduleSession(Session session, LocalDateTime newStartDate);
}
```

Этот метод будет принимать параметрами доклад и новое время начала, а результатом выполнения будет измененный доклад. 

Далее, откройте класс ```SessionServiceBean``` в редакторе. Класс можно найти в дереве проекта в разделе **Middleware - Services**:

![](/images/learn/v14/qs-screen45.png)

Класс не имеет реализации методов интерфейса, IDE подсвечивает его как ошибочный. 

![](/images/learn/v14/qs-screen461.png)

Внутри тела класса нажмите **Alt+Insert** и выберите **Implement methods** во всплывающем меню:

![](/images/learn/v14/qs-screen46.png)

Выберите метод ```rescheduleSession```, будет сгенерирована заготовка метода.

```iava
@Service(SessionService.NAME)
public class SessionServiceBean implements SessionService {

   @Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
       return null;
   }
}
```
Сначала вычислим время начала и окончания доклада в день, когда запланирован доклад.

```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
return null;   
}
```

В сервисе мы будем использовать CUBA API для доступа к данным - класс ```DataManager```. С его помощью выполним JPQL запрос для проверки: есть ли для заданного докладчика ещё доклады в указанном промежутке времени. Затем проверим результат выполнения запроса и, в зависимости от него, или обновим доклад, присвоив новую дату начала, или вернем исходный доклад.

Добавьте DataManager в код, нажав **Alt+Insert** в теле класса и выбрав **Inject** из всплывающего меню.

![](/images/learn/v14/qs-screen481.png)

Выберите ```DataManager``` из списка:

![](/images/learn/v14/qs-screen48.png)

Итоговый код метода представлен ниже:

```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
   Long sessionsSameTime = dataManager.loadValue("select count(s) from planner_Session s where " +
           "(s.startDate between :dayStart and :dayEnd) " +
           "and s.speaker = :speaker " +
           "and s.id <> :sessionId", Long.class)
           .parameter("dayStart", dayStart)
           .parameter("dayEnd", dayEnd)
           .parameter("speaker", session.getSpeaker())
           .parameter("sessionId", session.getId())
           .one();
   if (sessionsSameTime >= 2) {
       return session;
   }
   session.setStartDate(newStartDate);
   return dataManager.commit(session);
}
```

Сервис готов, давайте добавим его в браузер докладов. Настроим его вызов на событие перетаскивания доклада в календаре. 

В ```session-browse.xml``` выберите ```sessionsCalendar``` и перейдите на вкладку  **Handlers** инспекторе компонента. Выберите событие ```CalendarEventMoveEvent``` и перейдите по стрелке в контроллер.

В методе-обработчике события календаря добавим код, чтобы извлечь доклад из сгенерированного события и вызовем сервис для переноса доклада. Поле этого просто обновим доклад в коллекции данных.

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventMove(Calendar.CalendarEventMoveEvent<LocalDateTime> event) {
   Session session = sessionService.rescheduleSession((Session) event.getEntity(), event.getNewStart());
   sessionsDc.replaceItem(session);
}
```

Чтобы сервис заработал, необходимо перезапустить приложение. Это можно сделать при помощи кнопки **Run** панели инструментов IDEA:

![](/images/learn/v14/qs-screen49.png)

После перезапуска открываем календарь докладов и - вуаля! Теперь можем переносить доклады при помощи мыши прямо в календаре. Проверим, как это работает, для этого добавим два доклада для одного докладчика на один день и убедимся, что не сможем назначить ему дополнительный доклад на этот день.

<div id="chapter11" class="section"></div>

## Изменение стандартных надписей

<div class="timestamp-wrapper">
   <a id="stamp11" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">18:27</div>
  </div></a>
</div>

В CUBA приложениях можно изменять текст стандартных надписей, например, в основном экране или на окне логина. Давайте поменяем заголовки в приложении, чтобы они соответствовали его назначению - планированию конференций. 

Откройте файл конфигурации надписей - ```messages.properties```, который можно найти в разделе **Generic UI - Main Message Pack** дерева проекта CUBA.

![](/images/learn/v14/qs-screen50.png)

Это обычный текстовый файл ```.properties```, так что его можно отредактировать, добавляя новые свойства и обновляя существующие. Изменим надписи, как показано в примере ниже: 

```java
application.caption = Session Planner
application.logoLabel = Session Planner
application.logoImage = branding/app-icon-menu.png
application.welcomeText = Welcome to Session Planner!

loginWindow.caption = Session Planner Login
loginWindow.welcomeLabel = Welcome to Session Planner!
loginWindow.logoImage = branding/app-icon-login.png

menu-config.application-planner = Sessions and Speakers
menu-config.planner_Speaker.browse=Speakers
menu-config.planner_Session.browse=Sessions
```
Поскольку CUBA может перезагружать экраны “на ходу”, просто выйдем из приложения и войдем в него заново, чтобы увидеть внесенные изменения.

<div id="chapter12" class="section"></div>

## Магазин компонентов

<div class="timestamp-wrapper">
   <a id="stamp12" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">19:11</div>
  </div></a>
</div>

Вместе с фреймворком поставляется <a target="_self" href="/marketplace/">магазин компонентов</a> , в котором содержится довольно много дополнений, так что вы можете легко добавить новые возможности в приложение. Например, это может быть <a target="_self" href="/marketplace/maps/">поддержка картографии</a> или <a target="_self" href="/marketplace/charts/">рисование диаграмм</a>. Эти компоненты можно легко установить из CUBA Studio, используя главное меню: **CUBA -> Marketplace**.

![](/images/learn/v14/qs-screen51.png)

Давайте добавим компонент [Helium](https://www.cuba-platform.com/marketplace/helium/). Это новая визуальная тема, которую можно использовать вместо стандартных тем. Нажимаем **Install** и применяем изменения. 

Теперь нужно остановить приложение и выполнить скрипты компонента. 

![](/images/learn/v14/qs-screen52.png)

Запустим проект и перейдем к настройкам. Добавленная тема появилась в списке. Выберем ее и сохраним изменения.

![](/images/learn/v14/qs-screen53.png)

Перелогинимся - тема уже применилась. 

![](/images/learn/v14/qs-screen54.png)

В окне настроек темы можно предварительно посмотреть, как будут выглядеть изменения.

<div id="chapter13" class="section"></div>

## Заключение

<div class="timestamp-wrapper">
   <a id="stamp13" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">19:27</div>
  </div></a>
</div>

Платформа CUBA предоставляет большое количество функциональности, чтобы помочь вам сделать бизнес-приложения быстро и качественно. В этой статье мы рассмотрели только базовые вещи. На нашем сайте <a target="_self" href="https://www.cuba-platform.ru">cuba-platform.ru</a> вы найдете больше <a target="_self" href="/learn/live-demo/">примеров</a> и [учебников (англ.)](https://www.cuba-platform.com/guides/), которые помогут вам глубже освоить возможности CUBA, а также <a target="_self" href="/discuss/">форум</a>, на котором можно задавать вопросы касательно разработки на платформе CUBA.

Спасибо за интерес к CUBA!
