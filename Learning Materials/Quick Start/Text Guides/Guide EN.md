<div id="chapter0" class="section"></div>

## Preface

CUBA Platform is an open source-framework intended to streamline the development process of business applications. It combines proven architecture, ready-to-use enterprise-grade components and productive tooling, so you can deliver modern web applications faster than ever before. 

In this quick start, we will scratch the surface of CUBA and develop a very simple, but fully functional conference planner application. It will show three main things for creating any web application: how to design a data model, how thew to manipulate data, how to create business logic and, finally, how to create a user interface. As an example, we will create a simple, but fully functional version of an application that will help with session planning for a conference. In fact, this tutorial will be just enough for you to start your own CUBA application, so let's get started. In this tutorial, we will use <a target="_self" href="/tools/">CUBA Studio</a>, so please install it before you begin and accept a trial subscription to have access to the visual designers.  

Sample code repo: [https://github.com/cuba-platform/sample-session-planner](https://github.com/cuba-platform/sample-session-planner).
<div id="chapter1" class="section"></div>

## Creating an empty project

<div class="timestamp-wrapper">
   <a id="stamp1" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">0:48</div>
  </div></a>
</div>

Let’s create an empty CUBA project using the corresponding Intellij IDEA menu. Name it SessionPlanner with the project namespace - planner. We will use Java 11 as the default JDK. 
 
![](/images/learn/v14/qs-screen1.png)

We will use an in-memory database - HSQLDB.

![](/images/learn/v14/qs-screen2.png)

After the first launch CUBA Studio offers you to apply a trial subscription to CUBA Studio Premium.

![](/images/learn/v14/qs-screen3.png)

Let’s apply a trial subscription. It is free for 28 days and gives us some useful optional designers.

<div id="chapter2"  class="section"></div>

## Data Model Creation

<div class="timestamp-wrapper">
   <a id="stamp2" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">2:29</div>
  </div></a>
</div>

The first task is creating entities. The business domain model has only two classes: Speaker and Session. The relation is one-to-many. One speaker can conduct many sessions. 

![](/images/learn/v14/qs-screen4.png)

For a starter, let’s create the ```Speaker``` entity. To do this, we can use a link on project’s welcome page:

![](/images/learn/v14/qs-screen5.png)

Or just right-click on the **Data Model** node in CUBA Project Tree on the left side of the screen and select **New -> Entity**.

![](/images/learn/v14/qs-screen6.png)

Enter entity’s name - ```Speaker``` and create attributes according to the specification:

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

In CUBA we use standard JPA entities, so you can create them using either code editor or visual designer. Just click “+” icon and add attributes to the entity, CUBA Studio will generate class members for you.

![](/images/learn/v14/qs-screen7.png)

In CUBA, you can specify a format for a proper entity display as a string in the UI using <b>Instance Name</b>. For the speaker, we will select both the first and the last names.

![](/images/learn/v14/qs-screen8.png)

If we have a look at the **Text** tab on the bottom of the entity designer, we can see just a JPA-annotated java class. The generated code can be updated if needed, designer will be updated accordingly when you switch to the <b>Designer</b> tab.

Also, you can see the DDL Preview and can create indexes if required.
For instance, we expect a lot of searches by last name. To make this search more efficient we can create an index for the last name field.

![](/images/learn/v14/qs-screen9.png)

Let’s move further and create the ```Session``` entity and link it to our ```Speaker``` class. Fields specification table follows. The session end time will be a calculated value, being the start date plus duration.

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

Look at the adding a mandatory reference to the speaker. The relation is many-to-one, so, we’ll define an association field called speaker that references the speaker class. Finally, the field definition should look like this:

![](/images/learn/v14/qs-screen10.png)

<div id="chapter3" class="section"></div>

## Creating Calculated Attribute

<div class="timestamp-wrapper">
   <a id="stamp3" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">5:01</div>
  </div></a>
</div>

Now, we need to create an auto calculated attribute for the session’s end date and time. First add the getter method and name it ```getEndDate```. Mark the method with the ```@MetaProperty``` annotation. For the calculated attribute, we need to specify the fields to be loaded. In our case, these are ```startDate``` and ```duration```. Then just add calculation logic.

```java
@Transient
@MetaProperty(related = {"startDate", "duration"})
public LocalDateTime getEndDate() {
   return (startDate != null && duration != null) ? startDate.plusHours(duration) : null;
}
```    
Notice that Studio highlights the method because we need to specify a text label for the attribute. Press **Alt+Enter** and add a text to the message bundle.

![](/images/learn/v14/qs-screen11plus.png)

That’s it. Our domain model has been created.

<div id="chapter4" class="section"></div>

## Generating CRUD Screens

<div class="timestamp-wrapper">
   <a id="stamp4" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">06:00</div>
  </div></a>
</div>

CUBA Studio contains a UI screen generation wizard that helps us to create basic, but useful UI screens:

*   Browser - to show the list of entities in a grid
*   Editor - to edit one entity instance using a form-like user interface

First, we will create screens to work with speakers. Since this entity is pretty simple, we will use the default parameters for the screen creation. Start a wizard by clicking on **Create Screen** menu item in **Screens** menu on the top of the entity designer.

![](/images/learn/v14/qs-screen11.png)

You can also start screen generation wizard by right-clicking on an entity in CUBA Project tree and selecting **New -> Screen** from the context menu.

![](/images/learn/v14/qs-screen12.png)

For the ```Speaker``` entity, we will create default browser and editor.  Select <b>Entity browser and editor screens</b> on the <b>Screen Templates</b> tab in the wizard and click <b>Next</b>. Since this entity is pretty simple, we can accept the default parameters for the screen creation. 

![](/images/learn/v14/qs-screen13.png)

As you can see, each screen consists of two parts: a controller, written in java, which is responsible for internal screen logic and events handling and an XML layout that defines the screen appearance. In our case, the browser will consist of files ```speaker-browse.xml``` and ```SpeakerBrowse.java``` and editor - ```speaker-edit.xml``` and ```SpeakerEdit.java``` accordingly. Source files are located under **Generic UI -> Screens** menu in CUBA Project tree.

![](/images/learn/v14/qs-screen14.png)

Please note the ```data``` section in the XML screen descriptors - it defines how the data is fetched from the database.

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
You can easily navigate between screen controller, descriptor and linked entities with CUBA Studio using buttons on the top of the window:

![](/images/learn/v14/qs-screen15.png)

![](/images/learn/v14/qs-screen16.png)

![](/images/learn/v14/qs-screen17.png)

<div id="chapter5" class="section"></div>

## Creating a browser and editor for sessions

<div class="timestamp-wrapper">
   <a id="stamp5" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">07:11</div>
  </div></a>
</div>

Run the screen generation wizard, select <b>Entity browser and editor screens</b> and stop at the <b>Entity browser view</b> step.

In the CUBA Platform, Entity View specifies which fields will be fetched from the database. You can define views in a separate file to use them in the different modules of your application, or create inline views while creating the screens.

Let’s create an inline view. Just select the necessary data: end date and speaker reference.

![](/images/learn/v14/qs-screen18.png)

At the next step the necessary fields are already selected.

![](/images/learn/v14/qs-screen19.png)

You can see that the corresponding fields are added to the screens. 

![](/images/learn/v14/qs-screen20.png)

In addition, set the default duration value for the new session to one hour. To do this, go to the <b>Component Inspector</b> window and subscribe to the event ```InitEntity``` in the Handlers tab.  Then set this value in the code. 

![](/images/learn/v14/qs-screen21.png)

```java
@Subscribe
public void onInitEntity(InitEntityEvent<Session> event) {
   event.getEntity().setDuration(1);
}
```

<div id="chapter6" class="section"></div>

## Database Creation

<div class="timestamp-wrapper">
   <a id="stamp6" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:11</div>
  </div></a>
</div>

Select <b>CUBA -> Generate Database Scripts</b> menu to create SQL for the database creation. You can review the generated scripts in the pop-up window before saving them as project files. Please note that those scripts are a part of the project, you can find them under <b>Main Data Store</b> node in the project tree.

![](/images/learn/v14/qs-screen22.png)

You can modify the scripts if you want to add some specific things like additional indexes or insert statements for the initial data. 

![](/images/learn/v14/qs-screen23.png)

Click <b>Create database</b> button to apply those scripts and create the database.  Apart from application tables, CUBA creates system tables where we store information about users, roles, tasks, etc.

That’s it. The database has been created.

<div id="chapter7" class="section"></div>

## Running the application in development mode

<div class="timestamp-wrapper">
   <a id="stamp7" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:39</div>
  </div></a>
</div>

To run the CUBA application you can use either run configuration on the top on the IDE window.

![](/images/learn/v14/qs-screen24.png)

Or select **CUBA -> Start Application Server** from the main menu. 

![](/images/learn/v14/qs-screen25.png)

After some time you can access the application using the browser. The URL will be displayed in the Run toolbox in IDEA. In our case it will be http://localhost:8080/app . Open the URL in your web browser and log into the application using “admin” as a username. The password is “admin” by default. You can find screens for entities manipulation under **Application** menu. 
Then let’s add some data to the database: a couple of speakers and two sessions for them scheduled for the rest of the week. You can try to enter invalid email for a speaker and see that email validator works as expected. 

![](/images/learn/v14/qs-screen26.png)

![](/images/learn/v14/qs-screen27.png)

Generated screens are good for basic operations, but in the real world, UI is usually more complex. 

<div id="chapter8" class="section"></div>

## Customizing the User Interface

<div class="timestamp-wrapper">
   <a id="stamp8" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">10:05</div>
  </div></a>
</div>

Let’s add a calendar view to browse sessions in addition to the grid view. For the browser screen, we’ll add tab control, put a calendar on it and provide a functionality to edit and reschedule sessions using this calendar like in the picture below:

![](/images/learn/v14/qs-screen28.png)

Open ```session-browse.xml``` file in designer and wrap the sessions table into tab sheet in the <b>Component Hierarchy</b> window.

![](/images/learn/v14/qs-screen29.png)

Add one more ```tab``` page under the ```TabSheet```.

![](/images/learn/v14/qs-screen30.png)

Put a calendar control on it.

![](/images/learn/v14/qs-screen31.png)

Select the ```TabSheet``` element in the <b>Component Hierarchy</b> window and select expanded in the <b>Component Inspector</b>. Studio asks for an ID. In CUBA, we need IDs to reference a screen element in the code.

![](/images/learn/v14/qs-screen32.png)

Assign ids ```calendarTab``` and ```tableTab``` for the tabs. Then set captions ```Sessions calendar``` and ```Sessions table``` for these tabs accordingly. 

![](/images/learn/v14/qs-screen33.png)

Update the calendar - assign a data container and expand it.

![](/images/learn/v14/qs-screen34.png)

Finally, expand the Sessions Table.

![](/images/learn/v14/qs-screen35.png)

In CUBA, UI components can be bound to entities and their properties. 

Let’s bind the calendar to the data collection fetched in the screen. Use the search field to find properties and bind:
- ```startDateProperty``` to a session’s start Date
- ```endDateProperty``` to a session’s end Date
- ```captionProperty``` to a session’s topic
- And ```descriptionProperty``` to a session’s description

![](/images/learn/v14/qs-screen36.png)

Let’s make the calendar to show only working hours.

![](/images/learn/v14/qs-screen37.png)

In addition to the visual designer, you can use the XML markup editor. Let’s add navigation buttons.  

![](/images/learn/v14/qs-screen37_1.png)

To see the changes in the UI, you don’t need to restart the application, just reopen the screen in the application. CUBA framework supports hot deploy for screens. Now you can see sessions are displayed on the calendar. 

<div id="chapter9" class="section"></div>

## Using Screen API 

<div class="timestamp-wrapper">
   <a id="stamp9" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">12:09</div>
  </div></a>
</div>

When we interact with the UI, events are generated, therefore CUBA gives you an API that you can use to subscribe to those events to handle them. Let’s handle a click on a calendar’s entry and invoke the session editor. For screen manipulations, CUBA provides a screen builder API that we will use.

In the ```session-browse.xml``` select ```sessionsCalendar``` and go to the Handlers tab in the <b>Component Inspector</b> window. Select ```CalendarEventClickEvent``` and click the arrow to move to the controller.

![](/images/learn/v14/qs-screen38.png)

The empty method will be generated for you.

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
  
}
```

We need to invoke the editor screen to change the session’s properties. Let’s use the ```EditorScreenFacet```. It is a component that provides an ability to pre-configure an editor screen. 

Go to ```session-browse.xml``` and find the <b>EditorScreen</b> in the <b>Component Palette</b> and move it under the window element in the <b>Component Hierarchy</b> window.

![](/images/learn/v14/qs-screen39.png)

Set the following settings:

- ID - ```sessionEditDialog```;
- data container - ```sessionsDc```;
- edit mode - ```EDIT```;
- entity class - ```Session```;
- open mode - ```DIALOG```;
- screen class - ```SessionEdit```.

![](/images/learn/v14/qs-screen40.png)

Go back to the event handler. Click on **Inject** button on the top of the window and select ```sessionEditDialog``` service form Screen API section in the popup window. 

Another way for the injection (and subscription) - press **Alt+Insert** key combination in the editor and select **Inject** in the popup menu:

![](/images/learn/v14/qs-screen40_1.png)

To search for the service, just start typing its name and then use **Up** and **Down** keys to navigate between services available for injection.

![](/images/learn/v14/qs-screen40_2.png)

Pass the session entity received within the event object for editing. After that, we should show the editor.

In the handler’s code it will look like this:

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
   sessionEditDialog.setEntityProvider(() -> (Session) event.getEntity());
   sessionEditDialog.show();
}
```

That’s it. You can reopen the sessions browser screen in the running application and invoke the editor by clicking on the session in the calendar. 

![](/images/learn/v14/qs-screen41.png)

The editor doesn’t look nice, so we need to adjust its width and height. In the IDE, open the screen’s XML descriptor, select ```dialogMode``` property and set ```width``` and ```height``` properties to ```auto```.

![](/images/learn/v14/qs-screen42.png)

Go to the application and reopen the editor by closing it and clicking on the session in the calendar again. Now it looks nicer.

![](/images/learn/v14/qs-screen43.png)

<div id="chapter10" class="section"></div>

## Adding Business Logic

<div class="timestamp-wrapper">
   <a id="stamp10" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">13:47</div>
  </div></a>
</div>

Now we will use CUBA Studio to create a service that implements business logic and use this service in a screen. It will be a service for session rescheduling that will ensure that one speaker doesn’t have more than two sessions in one day.

Right-click on the service node in the CUBA project tree and select **New ->Service**. This will open a service creation dialog. Enter ```SessionService``` as an interface name, ``` SessionServiceBean``` as an implementation name will be generated automatically. 

![](/images/learn/v14/qs-screen44.png)

Create a method ```rescheduleSession``` in the interface like in the code snippet below: 

```java
public interface SessionService {
   String NAME = "planner_SessionService";

   Session rescheduleSession(Session session, LocalDateTime newStartDate);
}
```

The method will accept a session and a new session date and time. The service will return the updated Session instance.

To implement the method, open code editor for the ```SessionServiceBean``` class, you can find it under **Middleware - Services** node in the CUBA project tree:

![](/images/learn/v14/qs-screen45.png)

You’ll see that the class is empty and invalid:

![](/images/learn/v14/qs-screen461.png)

Press **Alt+Insert** in class’ body and select **Implement Methods** in the popup menu:

![](/images/learn/v14/qs-screen46.png)

Select ```rescheduleSession``` method, the code stub will be generated:

```iava
@Service(SessionService.NAME)
public class SessionServiceBean implements SessionService {

   @Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
       return null;
   }
}
```
First, calculate the start and end time of the day where the session is planned.

```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
return null;   
}
```

For the service, we will use CUBA API for data access - ```DataManager``` class. With this class we create a JPQL query to check if there are any sessions scheduled for the speaker in a defined time span and add parameter values to it. Then we check the query result and, depending on the result, we update the session with a new start date or just return the original session instance.

Inject the DataManager into the service by pressing **Alt+Insert** in the class body and select **Inject from** the popup menu:

![](/images/learn/v14/qs-screen481.png)

Select ```DataManager``` in the popup window:

![](/images/learn/v14/qs-screen48.png)

The method implementation is on the snippet below:

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

The service is ready, now let’s add it to the session browser screen. It will be invoked for the drag-and-drop event in the calendar. 

In the ```session-browse.xml``` select ```sessionsCalendar``` and go to the **Handlers** tab in the **Component Inspector** window. Select ```CalendarEventMoveEvent``` and click the arrow to move to the controller.

In the method that is subscribed to the drag-and-drop event, we add some code to extract the session entity from the calendar event and pass it to the service to reschedule this session. After that, we just update this item in the browser’s data container.

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventMove(Calendar.CalendarEventMoveEvent<LocalDateTime> event) {
   Session session = sessionService.rescheduleSession((Session) event.getEntity(), event.getNewStart());
   sessionsDc.replaceItem(session);
}
```

To have the new service redeployed, we need to restart the application, we can use the same **Run** button in IDEA . 

![](/images/learn/v14/qs-screen49.png)

After restarting we can open sessions calendar - and voila! We have drag-and-drop rescheduling functionality for the calendar! Let’s test it by adding an extra session and trying to reschedule it.


<div id="chapter11" class="section"></div>

## Adding Branding

<div class="timestamp-wrapper">
   <a id="stamp11" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:27</div>
  </div></a>
</div>

In CUBA, you can update resource files and override the standard text. Let’s update the text according to the application business domain - conference planning.

Open main message pack file - ```messages.properties``` which is situated under **Generic UI - Main Message Pack** node in CUBA project tree. 

![](/images/learn/v14/qs-screen50.png)

It is a standard ```java .properties``` file, so you can edit it freely, adding new message keys and updating existing ones. Update messages to reflect the purpose of the application. The example is below:

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
With CUBA’s hot deploy feature, all we need to do is to log in to the application again to see the changes.

<div id="chapter12" class="section"></div>

## Marketplace

<div class="timestamp-wrapper">
   <a id="stamp12" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:58</div>
  </div></a>
</div>

The framework comes with a <a target="_self" href="/marketplace/">marketplace</a> that contains plenty of components, so you can easily add useful features like <a target="_self" href="/marketplace/charts-and-maps/">charts</a> or <a target="_self" href="/marketplace/maps/">maps</a> support to the existing application. You can install those components right from the studio using the <b>CUBA -> Marketplace</b> menu.

![](/images/learn/v14/qs-screen51.png)

Let’s add the [Helium](https://www.cuba-platform.com/marketplace/helium/) add-on. It’s a new visual theme, that you can use instead of the standard themes. Just click install and apply the changes. 

Now we need to stop the application and update the database to apply the init scripts of the add-on. 

![](/images/learn/v14/qs-screen52.png)

Run the application and go to the settings screen. You can find the added theme in the drop-down list. Select it and apply.

![](/images/learn/v14/qs-screen53.png)

Log in to the application again - theme is already applied.

![](/images/learn/v14/qs-screen54.png)

We can also open the theme settings screen and change settings with preview.

<div id="chapter13" class="section"></div>

## Conclusion

<div class="timestamp-wrapper">
   <a id="stamp13" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">17:58</div>
  </div></a>
</div>

CUBA framework provides a lot of useful APIs to help you with creating business applications quickly. This quickstart shows only the basics of the CUBA framework and CUBA Studio. You can find more <a target="_self" href="/learn">examples</a> and <a target="_self" href="/documentation">tutorials</a> on our website: <a target="_self" href="/">cuba-platform.com</a>. 

Thank you for your interest in CUBA Platform. Enjoy development with CUBA! 
