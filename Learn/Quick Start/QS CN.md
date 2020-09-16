<div id="chapter0" class="section"></div>

## 序言

CUBA 平台是一个开源框架，目的是为业务应用简化开发流程。 它包括成熟框架、拿来即用的企业级组件和丰富的产品工具，通过它你可以更快地交付现代 web 应用。

本快速开始指南将蜻蜓点水地使用 CUBA 开发一个简单、但是功能完备的会议计划应用。它会展现开发任何 web 应用涉及的四大项： 如何设计数据模型，如何操作数据，如果创建业务逻辑，以及如何创建用户界面。 做为示例，我们会创建一个简单、但是功能完备的应用版本，它用来帮助大会做会议计划。 事实上，这个程序对初次开发 CUBA 应用来说已经足够复杂，现在我们就开始。这个教程会用到 <a target="_self" href="/tools/">CUBA Studio</a>，所以请在开始之前先安装，并选择商业试用版许可，这样可以使用可视化设计器。

示例代码库：[https://github.com/cuba-platform/sample-session-planner](https://github.com/cuba-platform/sample-session-planner).
<div id="chapter1" class="section"></div>

## 创建一个空项目

<div class="timestamp-wrapper">
   <a id="stamp1" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">0:48</div>
  </div></a>
</div>

我们使用 Intellij IDEA 对应的菜单创建一个空的 CUBA 项目，命名为 SessionPlanner， 项目命名空间为 - planner。我们将使用 Java 11 做为默认 JDK。

![1](http://192.168.45.58:1337/uploads/d5e5688d10df4f7895e727786e54e858.png)

我们将使用一个内存数据库 - HSQLDB。

![2](http://192.168.45.58:1337/uploads/96ee03070bf04167b78b0a9dda4e950e.png)

第一次启动 CUBA Studio 时，会提供一个 CUBA Studio 的商业试用版许可，可以使用 Studio 的高级功能。

![3](http://192.168.45.58:1337/uploads/5b77f5dea19a457e96ab3feab4784ed2.png)

我们来申请一个试用版，它有 28 天的免费期， 为我们提供一些有用的、可选的设计器。

<div id="chapter2"  class="section"></div>

## 创建数据模型

<div class="timestamp-wrapper">
   <a id="stamp2" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">2:29</div>
  </div></a>
</div>

第一项任务是创建实体。这个业务领域只包含两个类： Speaker 和 Session（发言人和会议）。它们的关系是一对多。一个发言人可以主持多个会议。

![4](http://192.168.45.58:1337/uploads/5379b3de22a043719690a520aafb9157.png)

我们先来创建 `Speaker` 实体，使用项目欢迎页的链接：

![5](http://192.168.45.58:1337/uploads/91fd78c155c345298d18168de91054a7.png)

或者右键点击屏幕左侧 CUBA 项目树的 **Data Model** 节点，然后选择 **New -> Entity** 。

![6](http://192.168.45.58:1337/uploads/756992b8e9e54c7ca6898a61904c7185.png)

输入实体名称 - `Speaker` 然后根据下面的说明创建属性。

<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>名称</td>

<td>类型</td>

<td>必须</td>

<td>其他约束</td>

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

<td>“Email” 验证</td>

</tr>

</tbody>

</table>
</div>

在 CUBA 中我们使用标准的 JPA 实体，所以你既可以使用代码编辑器，也可以使用可视化设计器创建实体。只需点击 “+” 图标，然后往实体中添加属性，CUBA Studio 会为你生成类成员。

![7](http://192.168.45.58:1337/uploads/dbd351074e2b4932ae1b9a861de79858.png)

在 CUBA 中，通过 <b>Instance Name（实例名称）</b>，可以为一个实体指定一个格式化的字符串做为它的显示名称。对于 speaker， 我们选择姓和名。

![8](http://192.168.45.58:1337/uploads/6c685b716b59497da43369d414334700.png)

如果我们看一下实体设计器底部的 **Text** 标签页，会看到一个带 JPA 注解的 Java 类。生成的源码可以按需修改，之后切换到 <b>Designer</b> 标签页，设计器也会包含相应的修改。

你也可以预览生成的 DDL，需要的话可以在这里创建索引。例如，我们预见会有很多针对 last name（姓）的搜索，为了使搜索更有效，我们为 last name 字段创建一个索引。

![9](http://192.168.45.58:1337/uploads/09616e46cc7a4fd9aa301a59f2a8b2cc.png)

继续，创建 `Session` 实体，然后与 `Speaker` 进行关联。字段说明在下方的表格中。 session end time（会议结束时间）是一个计算值，计算方法为 开始时间 + 持续时间（start date + duration）。

<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>名称</td>

<td>类型</td>

<td>必须</td>

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

<td>关联到 “Speaker” 实体, 多对一关系</td>

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

注意这里，添加一个必须的引到 speaker。关系为多对一，所以，我们定义一个关联属性叫做 speaker，引用 speaker 类。最后，字段定义看起来如下：

![10](http://192.168.45.58:1337/uploads/eeb0b3e1aed24c97b54af7c091c730c4.png)

<div id="chapter3" class="section"></div>

## 创建计算属性

<div class="timestamp-wrapper">
   <a id="stamp3" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">5:01</div>
  </div></a>
</div>

现在，我们需要为会议结束时间创建一个自动计算的属性。首先添加 getter 方法，命名为`getEndDate`。用`@MetaProperty`注解。我们还需要为它指定要加载的字段，为 `startDate` 和 `duration`。然后添加计算逻辑。

```java
@Transient
@MetaProperty(related = {"startDate", "duration"})
public LocalDateTime getEndDate() {
   return (startDate != null && duration != null) ? startDate.plusHours(duration) : null;
}
```

注意 Studio 高亮了这个方法，因为我们需要为这个属性指定一个显示用的文本。按下 **Alt+Enter** 然后往信息包中添加一项。

![11+](http://192.168.45.58:1337/uploads/1560ae3e7f7949b996597ba4f357b288.png)

好了。我们的领域模型创建完毕。

<div id="chapter4" class="section"></div>

## 生成 CRUD 界面

<div class="timestamp-wrapper">
   <a id="stamp4" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">06:00</div>
  </div></a>
</div>

CUBA Studio 包含一个 UI 界面生成向导，能帮助我们创建基础但是非常有用的 UI 界面：

-   浏览界面 - 在数据网格中展示实体列表
-   编辑界面 - 通过表单类界面编辑一个实体实例

首先，我们创建 speaker 的界面。这个实体很简单，我们使用界面创建的默认参数。在实体设计器顶端，点击 **Screens** 菜单下的 **Create Screen** 菜单打开创建向导。

![11](http://192.168.45.58:1337/uploads/ff8c24fa973d4b0daef88170d6880ffd.png)

你也可以通过右键点击 CUBA 项目树下的实体，然后在弹出菜单中选择 **New -> Screen** 。

![12](http://192.168.45.58:1337/uploads/c52eed9e30664a82b9f1780b291543a2.png)

对于 `Speaker` 实体，我们会创建默认的浏览界面和编辑界面。在向导的 <b>Screen Templates</b> 标签页选择 <b>Entity browser and editor screens</b> 。由于这个实体非常简单，我们可以直接使用默认参数创建界面。

![13](http://192.168.45.58:1337/uploads/fc622c87468c42608b0ddba6338e6884.png)

可以看到，每个界面包含两部分： 一个 controller，用 Java 编写，用来处理内部的界面逻辑和事件处理；和一个定义界面外观的 XML 布局描述。在这个例子中，浏览界面包含 `speaker-browse.xml` 和 `SpeakerBrowse.java` 两个文件；相应的，编辑界面包含 `speaker-edit.xml` 和 `SpeakerEdit.java`。源文件的位置在 CUBA 项目树的 **Generic UI -> Screens** 菜单下。

![14](http://192.168.45.58:1337/uploads/4bbfcb7f14e4422794d52e427e9b9680.png)

请注意 XML 界面描述中的 `data` 一节 - 这里定义数据如何从数据库获取。

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

使用窗口上方的按钮，你可以方便地在界面 controller, descriptor 和 关联的实体之间切换。

![15](http://192.168.45.58:1337/uploads/d057028a89b6442e84d599c76fa7e3d9.png)

![16](http://192.168.45.58:1337/uploads/b9f005732859488cae0c0b3a0f0103da.png)

![17](http://192.168.45.58:1337/uploads/df24f4e6db384060ade3013b7a0b87b9.png)

<div id="chapter5" class="section"></div>

## 为 session 创建浏览及编辑界面

<div class="timestamp-wrapper">
   <a id="stamp5" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">07:11</div>
  </div></a>
</div>

运行界面生成向导，选择 <b>Entity browser and editor screens</b>， 停在 <b>Entity browser view</b> 步骤。

CUBA 平台中，Entity View（实体视图）指定哪些字段需要从数据库读取。你可以在一个独立的文件中定义视图然后在应用的不同模块中使用，也可以在创建界面的时候创建内联视图。

我们来创建一个内联视图。只选择必须的数据： end date 和 speaker 引用。

![18](http://192.168.45.58:1337/uploads/841fb5cfd74e4e8193bc5b5c657564dd.png)

下一个步骤里，字段已经被选上了。

![19](http://192.168.45.58:1337/uploads/75a9d27b0bff4654a05c06c29d1deda4.png)

可以看到，对应的字段已经加到界面了。

![20](http://192.168.45.58:1337/uploads/3591ca65c95b43caa3b195799d7323e5.png)

另外，为新会议设置默认持续时间为一个小时。要做到这一点，在 <b>Component Inspector</b> 窗口的 Handlers 标签页订阅 `InitEntity` 事件。

![21](http://192.168.45.58:1337/uploads/d2e2c0d53ec34b6b908e166d034e069a.png)

然后在代码中设置这个值。

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

选择 <b>CUBA -> Generate Database Scripts</b> 菜单来创建数据库 SQL 脚本。保存成项目文件之前，你可以在弹出窗口审阅生成的脚本，你也可以在项目树的 <b>Main Data Store</b> 节点下找到这些脚本。

![22](http://192.168.45.58:1337/uploads/1ad8691035714078b1ccb74187771249.png)

如果想增加一些特别的东西，比如额外的索引，或者是初始化数据的 insert 语句，可以直接修改上述脚本。

![23](http://192.168.45.58:1337/uploads/13f1902068594db0bebdc5c536c13b57.png)

点击 <b>Create database</b> 按钮应用这些脚本，创建数据库。除了应用程序用到的业务表，CUBA 还会创建系统表，用来保存用户、角色、任务等信息。

好了。数据库创建完毕。

<div id="chapter7" class="section"></div>

## 在开发模式运行程序

<div class="timestamp-wrapper">
   <a id="stamp7" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:39</div>
  </div></a>
</div>

运行 CUBA 应用程序，你可以通过 IDE 窗口上方的运行任务配置。

![24](http://192.168.45.58:1337/uploads/5805a55479554e9980f5bc834f3cdebd.png)

或者在主菜单选择 **CUBA -> Start Application Server** 。

![25](http://192.168.45.58:1337/uploads/5623110aa63d4e339d4034ab94af1a02.png)

稍等一会之后，你便可以通过浏览器访问应用。 URL 地址会显示在 IDEA 的 Run 工具箱中。我们这个例子的地址为：http://localhost:8080/app 。 在浏览器打开这个 URL，然后使用 “admin” 用户登录应用。密码默认为“admin”。你可以在 **Application** 菜单下找到实体操作界面。

接下来，我们为数据库添加一些数据：两个 speaker，并利用本周剩余时间为他们计划的两个 session。你可以尝试为 speaker 输入不规范的 email 地址， 然后会看到 email 验证如期生效。

![26](http://192.168.45.58:1337/uploads/0964cbc14ae148fd9af1bc0a92346760.png)

![27](http://192.168.45.58:1337/uploads/9539f76e705041f884ca130e1068a2c6.png)

对于基本操作，生成的界面够用了，但是真实的系统中，UI 往往更复杂。

<div id="chapter8" class="section"></div>

## 定制用户界面

<div class="timestamp-wrapper">
   <a id="stamp8" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">10:05</div>
  </div></a>
</div>

除了数据网格界面，我们来为浏览 session 再添加一个日历界面。 先在浏览界面里添加一个 tab 页控制组件，然后在里面放一个日历，使用日历提供编辑、重新计划会议的的功能，大概界面如下：

![28](http://192.168.45.58:1337/uploads/7d7434f0f5b6425c91a436f2c5258f06.png)

在设计器中打开 `session-browse.xml` 文件，在<b>Component Hierarchy</b>窗口把 session 表格放在 tab 页中。

![29](http://192.168.45.58:1337/uploads/93527dbe315c489d9046ec945713966c.png)

在 `TabSheet` 下再加一个 `tab` 页。

![30](http://192.168.45.58:1337/uploads/29b393fa4d4748d3890ceb38f37c881f.png)

放一个日历组件在上面。

![31](http://192.168.45.58:1337/uploads/089eb1bd177c4d7daef8aacfd07cb985.png)

在 <b>Component Hierarchy</b> 窗口，选择`TabSheet`元素，然后在<b>Component Inspector</b>中选择 `expanded`。 Studio 会要求填写一个 ID。 在 CUBA 代码中，我们通过 ID 来引用一个界面元素。

![32](http://192.168.45.58:1337/uploads/c6c060d1a87c43d988554093e87fd5f5.png)

为标签页指定 id： `calendarTab` 和 `tableTab`。然后相应的为它们设置标题： `Sessions calendar` 和 `Sessions table` 。

![33](http://192.168.45.58:1337/uploads/c463299f4c6d4667a8fdf307c65cd940.png)

更新日历 - 为其分配一个数据容器（data container），并使它撑满界面。

![34](http://192.168.45.58:1337/uploads/876aad2b5ba94fab894e6c675f1b57ec.png)

最后，将 Session 表格撑满界面。

![35](http://192.168.45.58:1337/uploads/4a3da112870e491484340a44fb3e913d.png)

在 CUBA 中，UI 组件可以绑定到实体和实体的属性上。

我们来把日历绑定到界面获取的数据集中。使用搜索域找到属性然后做如下绑定：

-   `endDateProperty` 绑定到 session 的结束时间（end Date）
-   `captionProperty` 绑定到 session 的主题（topic）
-   `descriptionProperty` 绑定到 session 的描述（description）

![36](http://192.168.45.58:1337/uploads/2286f20bc0c8429d8531b7803bcd3fe4.png)

我们来让日历只显示工作日时间。

![37](http://192.168.45.58:1337/uploads/b4aca1431e1045a3a45b6c377551cd56.png)

除了使用可视化设计器，你也可以使用 XML 标记编辑器。我们来添加导航按钮。

![37_1](http://192.168.45.58:1337/uploads/13767361646345ccb67e99a4f3d6ebf6.png)

要看到变化，你不需要重启应用，只需要在应用中重新打开界面。CUBA 框架对界面支持热部署。现在你可以看到会议已经在日历中显示了。

<div id="chapter9" class="section"></div>

## 使用界面 API

<div class="timestamp-wrapper">
   <a id="stamp9" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">12:09</div>
  </div></a>
</div>

当我们与 UI 交互时，会产生事件，因此 CUBA 为你提供一个 API，用来订阅这些事件并处理。我们来处理一个点击日历条目的事件，然后调用 session 编辑页面。CUBA 为界面操作提供了 screen builder API。

在 `session-browse.xml` 中选择 `sessionsCalendar` ，然后在 <b>Component Inspector</b> 窗口选择 Handles 标签页。选择 `CalendarEventClickEvent` 然后点击箭头切换至 controller。

![38](http://192.168.45.58:1337/uploads/d591b22d225f485c943df9dbea42b964.png)

空方法已经为你生成好了。

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {

}
```

我们需要调用编辑界面来修改 session 属性。使用 `EditorScreenFacet` 。 这是一个可以用来预配置编辑界面的组件。

切换到`session-browse.xml`，然后在 <b>Component Palette</b> 找到 <b>EditorScreen</b>，然后把它移到 <b>Component Hierarchy</b> 窗口的 window 元素下。

![39](http://192.168.45.58:1337/uploads/7bdaa35d581840c3b427247174432e31.png)

做如下配置：

-   ID - `sessionEditDialog`;
-   data container - `sessionsDc`;
-   edit mode - `EDIT`;
-   entity class - `Session`;
-   open mode - `DIALOG`;
-   screen class - `SessionEdit`.

![40](http://192.168.45.58:1337/uploads/116842df5cc4404f9c9c76d393f9424c.png)

回到事件处理方法，点击窗口上方的 **Inject** 按钮，然后在弹出窗口的 Screen API 节选择 `sessionEditDialog` 服务。

另一个注入（或者说订阅）的方法是 - 在编辑页按下 **Alt+Insert** 组合键，然后在弹出菜单选择 **Inject**。

![40_1](http://192.168.45.58:1337/uploads/e4af00081f7c4dec959e401a4d8dbbd4.png)

要搜索 service（服务），只需要键入它的名称然后使用 **上** **下** 键选择可以注入的服务。

![40_2](http://192.168.45.58:1337/uploads/8d645c92e6634e0e823490a9c77c78c3.png)

为编辑界面传入接收到的事件中的 session 对象。然后，我们可以展示编辑界面了。

事件处理的代码大致如下：

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
   sessionEditDialog.setEntityProvider(() -> (Session) event.getEntity());
   sessionEditDialog.show();
}
```

好了。你可以在运行的应用中重新打开 session 浏览界面，然后在日历中通过点击 session 来调用编辑页面。

![41](http://192.168.45.58:1337/uploads/727064a12db4440eb3018a0c15349f7f.png)

编辑页面看起来不太好看，我们需要调整一下它的宽高。在 IDE 中，打开界面的 XML 描述，选择 `dialogMode` 属性然后把 `width` 和 `height` 都设置为 `auto`。

![42](http://192.168.45.58:1337/uploads/6cfb628bc3344893a4f84f126d1e8e72.png)

切换到应用程序，关闭编辑界面，然后在日历中点击 session 重新打开它。现在看起来好很多。

![43](http://192.168.45.58:1337/uploads/f87d1d51ffb04a41a86de11fda131326.png)

<div id="chapter10" class="section"></div>

## 添加业务逻辑

<div class="timestamp-wrapper">
   <a id="stamp10" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">13:47</div>
  </div></a>
</div>

现在我们来用 CUBA Studio 创建一个服务，用来实现业务逻辑，然后在界面中使用它。它是一个 session 重新计划的服务，并且保证一个 speaker 在一天之内不超过两个会议。

在 CUBA 项目树中右键点击 service 节点，选择 **New ->Service**。这会打开一个 service 创建对话框。输入 `SessionService` 做为接口名称，实现名称 ` SessionServiceBean`会为你自动生成。

![44](http://192.168.45.58:1337/uploads/18f1b069cf264ae48f795e163cb175d7.png)

在接口中创建一个方法 `rescheduleSession`，大致如下：

```java
public interface SessionService {
   String NAME = "planner_SessionService";

   Session rescheduleSession(Session session, LocalDateTime newStartDate);
}
```

这个方法接受一个 session 和一个新的 session 时间做为输入，它会返回更新后的 Session 实例。

要实现这个方法，打开 `SessionServiceBean` 类的代码编辑器，你可以在 CUBA 项目树的 **Middleware - Services** 节点下方找到它。

![45](http://192.168.45.58:1337/uploads/5632dcb82f0440d6907ea8817ed1f251.png)

你会看到这个类是空的并且无效：

![461](http://192.168.45.58:1337/uploads/cdbdd0a5e8924ead889d7d967709181f.png)

在类定义中按下 **Alt+Insert**，然后在弹出菜单选择 **Implement Methods** 。

![46](http://192.168.45.58:1337/uploads/c5c2e8ec7dc641fa922c8ff86de1ec17.png)

选择 `rescheduleSession` 方法，会生成如下桩代码：

```java
@Service(SessionService.NAME)
public class SessionServiceBean implements SessionService {

   @Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
       return null;
   }
}
```

首先，为 session 计算开始和结束时间。

```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
return null;
}
```

对于 service，我们会使用 CUBA API 来访问数据 - `DataManager` 类。我们用这个类创建一个 JPQL 查询来检查 speaker 在固定的时间是否已有会议计划，把参数值传入。然后检查结果，基于结果，我们为 session 更新一个新的开始时间或者仅仅把原 session 实例返回。

要为 service 注入 DataManager，在类实现中按下 **Alt+Insert**，然后在弹出菜单中选择 **Inject from**：

![481](http://192.168.45.58:1337/uploads/0098502fde934b20bc993736e2a54790.png)

在弹出窗口选择 `DataManager`。

![48](http://192.168.45.58:1337/uploads/1b2760f8585e4cbb90b1e1d1f824d8b0.png)

方法实现如下所示：

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

service 好了，我们来把它加到 session 浏览界面。将会在日历的拖拽事件中调用该 service。

在 `session-browse.xml` 中选择 `sessionsCalendar`，然后在 **Component Inspector** 窗口切换到 **Handlers** 标签页。选择 `CalendarEventMoveEvent` 然后点击箭头切换至 controller。

在订阅拖拽事件的方法中，我们加一些代码来从日历事件中拿到 session 实体，然后传给 service 来重新计划这个会议。然后，我们只需要在浏览界面的数据容器（data container）中更新该实体。

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventMove(Calendar.CalendarEventMoveEvent<LocalDateTime> event) {
   Session session = sessionService.rescheduleSession((Session) event.getEntity(), event.getNewStart());
   sessionsDc.replaceItem(session);
}
```

为了部署新的服务，我们需要重启应用程序，还是使用 IDEA 的 **Run** 按钮。

![49](http://192.168.45.58:1337/uploads/29aa319f7cec4b959ad179186086f31f.png)

重启后，我们打开会议日历 - 搞定！我们实现了日历的拖拽功能！我们来测试一下，添加新的会议，然后重新计划。

<div id="chapter11" class="section"></div>

## 添加品牌

<div class="timestamp-wrapper">
   <a id="stamp11" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:27</div>
  </div></a>
</div>

在 CUBA 中，你可以更新资源文件然后覆盖标准文本显示。我们根据应用的业务领域来将它改成 - conference planning。

打开主消息包文件 - 在 CUBA 项目树的 **Generic UI - Main Message Pack** 节点下。

![50](http://192.168.45.58:1337/uploads/086d63a8de4b47409358c33dac907544.png)

这是一个标准的 `java .properties` 文件，你可以随意修改它，添加新的消息键或者修改现有的。修改信息来使它正确表示应用的目的。示例如下：

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

由于 CUBA 有热部署功能，我们只需要重新登录应用程序就能查看变化。

<div id="chapter12" class="section"></div>

## 插件市场

<div class="timestamp-wrapper">
   <a id="stamp12" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:58</div>
  </div></a>
</div>

框架还带有一个<a target="_self" href="/marketplace/">应用市场</a>，里面有很多组件，所以你可以轻易地为应用添加有用的功能，比方说 <a target="_self" href="/marketplace/charts-and-maps/">图表</a> 或者 <a target="_self" href="/marketplace/maps/">地图</a>。你可以通过 <b>CUBA -> Marketplace</b> 菜单来安装这些组件。

![51](http://192.168.45.58:1337/uploads/f5e7ecea2a404fc1b984c8c5b0adeb99.png)

我们来添加 [Helium](https://www.cuba-platform.cn/marketplace/helium/) 组件。 它是一个新的可视化主题，你可以用它替换标准主题。只需要点击 install 然后 Apply 即可。

现在我们需要停止服务，然后更新数据库来应用插件的数据库初始化脚本。

![52](http://192.168.45.58:1337/uploads/07d40cf677f34df8b7f360f6f8bba299.png)

运行程序，打开 settings 界面。就能在下拉列表中找到新添加的主题，选择它然后应用。

![53](http://192.168.45.58:1337/uploads/bc7b0d6480f847158fe3b75101859450.png)

重新登录系统 - 主题生效。

![54](http://192.168.45.58:1337/uploads/5255eb8521cc447ca76606537767f880.png)

我们还能打开主题设置界面然后进行可预览的修改。

<div id="chapter13" class="section"></div>

## 总结

<div class="timestamp-wrapper">
   <a id="stamp13" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">17:58</div>
  </div></a>
</div>

CUBA 框架提供许多有用的 API 来帮助你快速创建业务应用。本快速开始指南只展示了基础的 CUBA 框架和 CUBA Studio。你可以在我们的主页：<a target="_self" href="/">cuba-platform.cn</a> 发现更多<a target="_self" href="/learn">示例</a>和<a target="_self" href="/documentation">指南</a>。

感谢你对 CUBA 平台的关注。享受用使用 CUBA 的开发过程吧！
