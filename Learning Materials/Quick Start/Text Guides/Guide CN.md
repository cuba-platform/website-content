<div id="chapter0" class="section"></div>

## 序言

CUBA 平台是一个开源框架，目的是为了将业务应用系统的开发过程流程化。框架集成了可靠的架构、开箱即用的企业级组件和高效的工具，因此您能比以往更快的交付现代 Web 应用程序。

在这个快速开始教程，我们将介绍一些 CUBA 的入门知识，开发一个非常简单，但是功能完备的会议计划应用系统。这个例子会展示创建任何 Web 应用程序都需要的三个功能：如何设计数据模型、如何操作数据、如何创建业务逻辑，此外，还将演示如何用 CUBA 创建用户界面。做为示例，我们将创建一个简单的、但是功能完整的应用版本，用来帮助大会做会议计划。实际上通过观看这个入门教程，足以使您能开始开发您自己的 CUBA 应用程序，我们现在就开始。这个入门教程中，我们将会使用 <a target="_self" href="/tools/">CUBA Studio</a>, 所以请在开始前先安装并接受试用许可协议，以便能使用可视化设计器。

示例代码仓库： [https://github.com/cuba-platform/sample-session-planner](https://github.com/cuba-platform/sample-session-planner)。
<div id="chapter1" class="section"></div>

## 创建一个空项目

<div class="timestamp-wrapper">
   <a id="stamp1" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">0:48</div>
  </div></a>
</div>

我们使用 Intellij IDEA 菜单创建一个空的 CUBA 项目，并命名为 SessionPlanner。
我们将使用 Java 8 作为默认的 JDK。
 
![](/images/learn/v14/qs-screen1.png)

我们将使用一个内存数据库 - HSQLDB。

![](/images/learn/v14/qs-screen2.png)

第一次启动CUBA Studio的时候，它会提示你申请一个CUBA Studio的商业试用版。

![](/images/learn/v14/qs-screen3.png)

免费试用28天，提供一些非常有用的可选的设计器，我们来开始使用这个商业试用版。

<div id="chapter2"  class="section"></div>

## 创建数据模型

<div class="timestamp-wrapper">
   <a id="stamp2" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">2:29</div>
  </div></a>
</div>

第一个任务 - 创建实体。业务领域模型只包含两个类：Speaker 和 Session。其关系为一对多。一位发言人可以主持多个会议。

![](/images/learn/v14/qs-screen4.png)

做为新手, 我们来创建 ```Speaker``` 实体。可以用 IDEA 中项目欢迎页的一个链接来创建：

![](/images/learn/v14/qs-screen5.png)

或者右键点击 Studio 界面左侧 CUBA 项目树的 **Data Model** 节点，然后选择 **New -> Entity**。

![](/images/learn/v14/qs-screen6.png)

输入实体名 - ```Speaker``` 并按照需求创建其属性：

<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>Name</td>

<td>Type</td>

<td>Mandatory</td>

<td>Other constraints</td>

</tr>

<tr>

<td>firstName</td>

<td>String (255)</td>

<td>Yes</td>

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

<td>Yes</td>

<td>“Email” validator</td>

</tr>

</tbody>

</table>
</div>

在 CUBA 中我们使用标准的 JPA 实体，您可以用代码编辑器或者可视化设计器创建实体。只要点击 “+” 图标为实体添加属性，CUBA Studio 会为您生成类成员变量。

![](/images/learn/v14/qs-screen7.png)

在 CUBA 中，您可以指定一个格式将实体以字符串的形势展示在 UI 中，这就是 <b>Instance Name</b>。对于 speaker，我们选择姓和名（first 和 last name）。

![](/images/learn/v14/qs-screen8.png)

如果我们看一看实体设计器底部的 **Text** 标签页，可以看到这就是一个使用了 JPA 注解的 Java 类。如果需要，也可以手动修改生成的代码，修改后切换到 <b>Designer</b> 标签页，设计器也会反映出代码的改动。

同样，您也能看到DDL预览文件，需要的话，可以自己创建索引。假如我们预见会有很多对 “姓（last name）” 的搜索，为了使搜索更有效率，我们可以对 last name 字段添加索引。

![](/images/learn/v14/qs-screen9.png)

接下来，我们创建 ```Session``` 实体并将其关联至我们的 ```Speaker``` 类。字段需求表如下。会议结束时间是一个计算值，为开始时间加持续时间。

<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>Name</td>

<td>Type</td>

<td>Mandatory</td>

</tr>

<tr>

<td>topic</td>

<td>String (255)</td>

<td>Yes</td>

</tr>

<tr>

<td>startDate</td>

<td>LocalDateTime</td>

<td>Yes</td>

</tr>

<tr>

<td>endDate</td>

<td>LocalDateTime</td>

<td></td>

</tr>

<tr>

<td>speaker</td>

<td>Association to “Speaker” entity, many-to-one cardinality</td>

<td>Yes</td>

</tr>
<tr>

<td>duration</td>

<td>Integer</td>

<td>Yes</td>

</tr>
<tr>

<td>description</td>

<td>String (2000)</td>

<td></td>

</tr>

</tbody>

</table>
</div>

我们要加一个对speaker的必须的引用。关系为多对一。所以我们定义一个 association 关联，叫做 speaker，它指向 speaker 类。最终，字段定义结果大概如下：

![](/images/learn/v14/qs-screen10.png)

<div id="chapter3" class="section"></div>

## 创建计算属性

<div class="timestamp-wrapper">
   <a id="stamp3" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">5:01</div>
  </div></a>
</div>

现在我们为session的结束时间创建一个可以自动计算的属性。首先添加getter方法，命名为 ```getEndDate```。用 ```@MetaProperty``` 注解这个方法。对于计算属性，需要指定需要加载的属性。在我们这个例子中，需要加载的是 ```startDate``` 和 ```duration```，接下来就是添加计算逻辑。

```java
@Transient
@MetaProperty(related = {"startDate", "duration"})
public LocalDateTime getEndDate() {
   return (startDate != null && duration != null) ? startDate.plusHours(duration) : null;
}
```    
注意这里Studio会高亮这个方法，因为我们需要给属性指定文本标签，键入 **Alt+Enter** 然后把文本加到消息包中。

![](/images/learn/v14/qs-screen11plus.png)

这就行了。领域模型创建好了。

<div id="chapter4" class="section"></div>

## 生成 CRUD 界面

<div class="timestamp-wrapper">
   <a id="stamp4" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">06:00</div>
  </div></a>
</div>

CUBA Studio 带有 UI 界面生成向导，可以帮我们创建基本，但是很有用的 UI 界面：

*   浏览界面 - 在数据网格中展示实体列表
*   编辑界面 - 使用类似表单的界面编辑一个实体实例

首先，我们创建处理 Speaker 的界面。由于实体结构非常简单，在创建界面时我们可以直接使用默认参数。点击实体设计器顶部的 **Screens** 菜单下的 **Create Screen** 菜单项即可打开界面创建向导。

![](/images/learn/v14/qs-screen11.png)

也可以在 CUBA 项目树中右键点击一个实体，然后选择 **New -> Screen** 菜单项打开界面创建向导。

![](/images/learn/v14/qs-screen12.png)

对于 ```Speaker``` 实体，我们为其创建默认的浏览界面和编辑界面。在向导的 <b>Screen Templates</b> 标签页选择 <b>Entity browser and editor screens</b>，然后点击 <b>Next</b>。这个实体很简单，我们可以直接使用默认参数创建界面。

![](/images/learn/v14/qs-screen13.png)

可以看到，每个界面包含了两个部分：一个控制器类，由 Java 编写，用来处理界面内部逻辑和事件响应，以及一个 XML 布局，定义了界面的展示形式。在我们的例子中，浏览界面由 ```speaker-browse.xml``` 和 ```SpeakerBrowse.java``` 文件组成，而编辑界面由 ```speaker-edit.xml``` 和 ```SpeakerEdit.java``` 组成。源文件都在 CUBA 项目树的 **Generic UI -> Screens** 中。

![](/images/learn/v14/qs-screen14.png)

请注意界面的 XML 描述中的 ```data``` 部分 - 定义了数据如何从数据库获取。

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
通过 CUBA Studio 窗口顶部的按钮可以很容易在界面控制器、界面描述文件以及关联实体之间切换：

![](/images/learn/v14/qs-screen15.png)

![](/images/learn/v14/qs-screen16.png)

![](/images/learn/v14/qs-screen17.png)

<div id="chapter5" class="section"></div>

## 为session创建浏览和编辑界面

<div class="timestamp-wrapper">
   <a id="stamp5" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">07:11</div>
  </div></a>
</div>

运行界面生成器向导，选择 <b>Entity browser and editor screens</b>，然后停在 <b>Entity browser view</b> 步骤。

在CUBA平台，实体视图（Entity View）指定了需要从数据库读取哪些字段。你可以在一个单独的文件里定义view，然后在不同的模块里使用；也可以在创建界面的时候创建内联的view。

这次我们创建一个内联的view，只选择必要的数据（字段）： end date 和 speaker引用。

![](/images/learn/v14/qs-screen18.png)

下一步你会发现相关的字段已经选上了。

![](/images/learn/v14/qs-screen19.png)

你会发现对应的字段已经加到界面了。

![](/images/learn/v14/qs-screen20.png)

然后，为新session设置默认的持续时间为1小时。方法是在 <b>Component Inspector</b> 窗口的 Handlers 标签页订阅 ```InitEntity``` 事件。

![](/images/learn/v14/qs-screen21.png)

然后在代码中设置它。

```java
@Subscribe
public void onInitEntity(InitEntityEvent<Session> event) {
   event.getEntity().setDuration(1);
}
```

<div id="chapter6" class="section"></div>

## 创建数据库

<div class="timestamp-wrapper">
   <a id="stamp6" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:11</div>
  </div></a>
</div>

选择 <b>CUBA -> Generate Database Scripts</b> 菜单来为数据库创建生成sql。你可以在弹出窗口审阅生成的脚本，然后再保存为项目文件。请注意，这些脚本也是项目的一部分，可以在项目树的 <b>Main Data Store</b> 节点找到这些脚本。

![](/images/learn/v14/qs-screen22.png)

如果需要添加一些特殊的内容，您可以手动修改脚本，比如额外的索引或者初始化数据的插入语句。

![](/images/learn/v14/qs-screen23.png)

点击 <b>Create database</b> 按钮应用这些脚本然后创建数据库。除了我们创建的应用程序表之外，CUBA 会创建一些系统表，用来存储用户、角色、任务等信息。

好了，数据库已经创建。

<div id="chapter7" class="section"></div>

## 运行开发模式的应用程序

<div class="timestamp-wrapper">
   <a id="stamp7" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:39</div>
  </div></a>
</div>

要启动应用程序，可以通过点击 IDE 顶部的 “Run” 按钮。

![](/images/learn/v14/qs-screen24.png)

也可以在主菜单选择 **CUBA -> Start Application Server** 。

![](/images/learn/v14/qs-screen25.png)

等待一会您就可以使用浏览器访问应用了。URL 会在 IDEA 的运行工具框内显示。我们这个例子里是 http://localhost:8080/app。在浏览器里打开这个地址，然后使用  “admin” 用户登录。默认密码是 “admin”。你可以在 **Application** 菜单下看到实体操作界面。
然后我们给数据库添加一些数据：添加两个 speaker，然后添加两个 session，计划时间为本周剩余的时间。您可以尝试为 speaker 输入无效的 email 看看验证器是否能正常工作。

![](/images/learn/v14/qs-screen26.png)

![](/images/learn/v14/qs-screen27.png)

生成的界面适合只有基本操作的情况，但是现实中的 UI 通常更加复杂。

<div id="chapter8" class="section"></div>

## 定制用户界面

<div class="timestamp-wrapper">
   <a id="stamp8" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">10:05</div>
  </div></a>
</div>

除了表格视图之外，我们再添加一个日历视图。因此，在浏览界面，我们需要添加标签页控件，再将日历放入标签页，提供在日历中编辑和重新计划会议的功能。就像这样：

![](/images/learn/v14/qs-screen28.png)

在设计器打开  ```session-browse.xml``` 文件并在组件树 <b>Component Hierarchy</b> 中把sessionsTable放到TabSheet中。

![](/images/learn/v14/qs-screen29.png)

在 ```TabSheet``` 下再添加一个 ```tab``` 。

![](/images/learn/v14/qs-screen30.png)

在上面放一个 calendar 日历组件。

![](/images/learn/v14/qs-screen31.png)

在 <b>Component Hierarchy</b> 窗口选择 ```TabSheet``` 元素，然后在 <b>Component Inspector</b> 中选择 ```expanded``` 。Studio会要求一个ID。在CUBA中，代码通过ID标记一个界面元素。

![](/images/learn/v14/qs-screen32.png)

为 ```calendarTab``` 和 ```tableTab``` 指定ID。然后为他们设置标题：  ```Sessions calendar``` 和 ```Sessions table``` 。

![](/images/learn/v14/qs-screen33.png)

修改calendar控件 - 指定 data container，然后使它撑满界面。

![](/images/learn/v14/qs-screen34.png)

最后，让Sessions表格撑满界面。

![](/images/learn/v14/qs-screen35.png)

在 CUBA 中，UI 组件能绑定到实体及其属性上。

我们将日历组件绑定到界面中获取的数据集。用 search 域找到属性然后绑定：
- ```startDateProperty``` 到会议的开始时间
- ```endDateProperty``` 到会议结束时间
- ```captionProperty```  到会议的主题
- And ```descriptionProperty``` 到会议的描述

![](/images/learn/v14/qs-screen36.png)

然后将日历只显示工作时间。

![](/images/learn/v14/qs-screen37.png)

除了可视化编辑器，你也可以使用 XML 编辑。我们来添加导航按钮。

![](/images/learn/v14/qs-screen37_1.png)

要查看 UI 的改动，您不需要重启应用程序，只需要在应用中重新打开界面。CUBA 框架支持界面的热部署。现在您可以看到会议都显示在日历中了。

<div id="chapter9" class="section"></div>

## 使用界面API

<div class="timestamp-wrapper">
   <a id="stamp9" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">12:09</div>
  </div></a>
</div>

当我们与 UI 进行交互的时候，会产生交互事件，因此，CUBA 提供了 API 可以订阅这些事件并进行处理。我们试试处理日历条目的点击事件，并调用会议编辑器。CUBA 提供了界面构建 API 用来操控界面，我们一会能用到。

在 ```session-browse.xml``` 选择 ```sessionsCalendar```，然后在<b>Component Inspector</b> 窗口的 Handlers 标签中，选择 ```CalendarEventClickEvent``` 然后点击箭头，将它放到controller中。

![](/images/learn/v14/qs-screen38.png)

会为您生成下面的空方法。

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
  
}
```

我们需要调用编辑界面来修改 session 的属性。我们来使用 ```EditorScreenFacet```。这是一个可以用来预配置编辑界面的组件。

在 ```session-browse.xml``` 的 <b>Component Palette</b>窗口，找到 <b>EditorScreen</b>，然后把它挪到 <b>Component Hierarchy</b> 窗口的 window 元素下。

![](/images/learn/v14/qs-screen39.png)

然后做以下设置：

- ID - ```sessionEditDialog```;
- data container - ```sessionsDc```;
- edit mode - ```EDIT```;
- entity class - ```Session```;
- open mode - ```DIALOG```;
- screen class - ```SessionEdit```.

![](/images/learn/v14/qs-screen40.png)

回到事件处理代码，点击窗口顶部的 **Inject** 按钮然后在弹出窗口的 Screen API 区域，选择  ```sessionEditDialog``` 服务。

injection (和 subscription) 的另一个方法是 - 在编辑器中按下 **Alt+Insert** 然后在弹出菜单中选择 **Inject** :

![](/images/learn/v14/qs-screen40_1.png)

需要搜索的话，键入名字然后使用键盘的上下键浏览可用的 injection。

![](/images/learn/v14/qs-screen40_2.png)

把事件object中的实例作为需要编辑的实例传入，然后我们展示编辑界面。

事件处理的代码大致如下：

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
   sessionEditDialog.setEntityProvider(() -> (Session) event.getEntity());
   sessionEditDialog.show();
}
```

这就可以了。重新打开session浏览界面，然后通过点击日历中的session来打开编辑页面。

![](/images/learn/v14/qs-screen41.png)

编辑页面看上去不太好看，我们调整一下宽高。在IDE中，打开界面的 XML 描述，选择 ```dialogMode``` 属性，然后把 ```width``` 和 ```height``` 设为 ```auto```。

![](/images/learn/v14/qs-screen42.png)

在应用中关闭编辑页面然后从日历中再次打开，现在看上去好些了。

![](/images/learn/v14/qs-screen43.png)

<div id="chapter10" class="section"></div>

## 添加业务逻辑

<div class="timestamp-wrapper">
   <a id="stamp10" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">13:47</div>
  </div></a>
</div>

现在我们将用 CUBA Studio 来创建一个服务，实现业务逻辑，然后在一个界面中使用这个服务。此服务将用于重新安排会议，检查同一个发言人不会在同一天内有多于两场会议。

在 CUBA 项目树中右键点击 service 节点然后选择 **New ->Service**。会弹出服务创建对话框。输入 ```SessionService``` 做为接口名称，Studio 会自动生成 ``` SessionServiceBean``` 作为实现类的名称。

![](/images/learn/v14/qs-screen44.png)

在接口中按照下面的代码创建一个 ```rescheduleSession``` 方法： 

```java
public interface SessionService {
   String NAME = "planner_SessionService";

   Session rescheduleSession(Session session, LocalDateTime newStartDate);
}
```

该方法接收 session 和会议的新 startDate 作为参数，并返回更新后的 Session 实例。

要实现该方法，打开 ```SessionServiceBean``` 类的代码编辑器，可以在 CUBA 项目树的 **Middleware - Services** 节点找到该类：

![](/images/learn/v14/qs-screen45.png)

可以看到类是空的，并且有错误：

![](/images/learn/v14/qs-screen461.png)

在类中任意处按下 **Alt+Insert**，在弹出菜单中选择 **Implement Methods** :

![](/images/learn/v14/qs-screen46.png)

选择 ```rescheduleSession``` 方法，会自动生成如下代码：

```iava
@Service(SessionService.NAME)
public class SessionServiceBean implements SessionService {

   @Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
       return null;
   }
}
```
首先，计算session计划所在那天的开始和结束时间。

```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
return null;   
}
```

在服务中，我们将使用 CUBA API 来访问数据 - ```DataManager``` 类。我们用它创建JPQL查询来检查在定义的时间段内是否有为 Speaker 安排的会议，并且传参数给它。然后我们检查查询结果，依据结果，我们用新的开始时间更新session实例或者直接返回原来的session实例。

在类中按下 **Alt+Insert** ，Inject DataManager，然后在弹出菜单中选择  **Inject from** ：

![](/images/learn/v14/qs-screen481.png)

在弹出框中选择 ```DataManager``` :

![](/images/learn/v14/qs-screen48.png)

方法的实现大概如下：

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

service好了，我们把它加到session浏览界面。它会在calendar的拖拽 drag-and-drop 事件中被调用。

在 ```session-browse.xml``` 中，选择 ```sessionsCalendar```，然后在 **Component Inspector** 窗口的 **Handlers** 标签中，选择 ```CalendarEventMoveEvent``` ，然后点击箭头加到controller中。

在订阅了拖拽事件 drag-and-drop 的方法中，我们加一些代码，从事件中拿到session实体，然后传给service重新计划它。然后，我们在浏览界面的data container 中更新它。

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventMove(Calendar.CalendarEventMoveEvent<LocalDateTime> event) {
   Session session = sessionService.rescheduleSession((Session) event.getEntity(), event.getNewStart());
   sessionsDc.replaceItem(session);
}
```

要使得新service部署，我们需要重启应用，还是用IDEA的 **Run** 按钮。

![](/images/learn/v14/qs-screen49.png)

应用重启之后，我们可以打开会议日历 - 搞定！可以拖拽会议进行重新安排了！测试一下，我们再添加一个会议并尝试重新安排。

<div id="chapter11" class="section"></div>

## 添加品牌信息

<div class="timestamp-wrapper">
   <a id="stamp11" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:27</div>
  </div></a>
</div>

在CUBA中，你可以直接修改资源文件，覆盖标准文本。我们来试试基于应用的业务领域 - conference planning 修改文本。

在CUBA项目树的  **Generic UI - Main Message Pack**  节点下，打开主消息包文件 - ```messages.properties``` 。

![](/images/learn/v14/qs-screen50.png)

这是个标准的 ```java .properties``` 文件，所以可以自由编辑 - 添加新的消息键值或者修改已有的键值。我们修改消息来体现应用程序的用途。示例如下：

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
由于 CUBA 有热部署功能，我们只需重新登入就能看到变化。

<div id="chapter12" class="section"></div>

## 应用市场

<div class="timestamp-wrapper">
   <a id="stamp12" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:58</div>
  </div></a>
</div>

框架带有 <a target="_self" href="/marketplace/">应用市场</a>，市场内有很多组件，这样您可以非常方便的添加有用的功能，比如为已有的应用程序添加 <a target="_self" href="/marketplace/charts-and-maps/">图表</a> 或 <a target="_self" href="/marketplace/maps/">地图</a> 。可以通过 Studio 的 <b>CUBA -> Marketplace</b> 菜单直接安装这些组件。

![](/images/learn/v14/qs-screen51.png)

我们来添加 [Helium](https://www.cuba-platform.cn/marketplace/helium/) 扩展。这是一个可视化主题，你可以用它替换标准主题，点击 install 然后 apply 即可。

现在我们需要停止程序然后应用组件的数据库初始化脚本。

![](/images/learn/v14/qs-screen52.png)

运行程序，打开 settings 界面，你会看到下拉列表中出现了添加的主题，选择它，然后应用。

![](/images/learn/v14/qs-screen53.png)

重新登入 - 主题已经生效。

![](/images/learn/v14/qs-screen54.png)

我们也可以打开主题设置界面，修改它并预览。

<div id="chapter13" class="section"></div>

## 总结

<div class="timestamp-wrapper">
   <a id="stamp13" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">17:58</div>
  </div></a>
</div>

CUBA 框架提供很多有用的 API，能帮您快速的创建业务系统。这次快速开始只演示了最基本的 CUBA 框架和 CUBA Studio 功能。访问我们的网站: <a target="_self" href="/">cuba-platform.cn</a>，你会看到更多的 <a target="_self" href="/learn">示例</a> 和 <a target="_self" href="/documentation">指南</a> 。

感谢您对 CUBA 平台有兴趣。享受用 CUBA 开发吧！
