## CUBA Platform Quick Start

Hi! Welcome to the CUBA Platform Quick Start Video.
  
CUBA Platform is an open source framework based on the well-known Spring framework.
  
The purpose of CUBA is to streamline the development process of business applications.
  
This quick start video covers the following topics:

* Data model and database creation
* User interface development and customization
* And business logic implementation
  
As an example, we will create a simple, but fully functional version of an application that will help with session planning for a conference. 
  
The video will be just enough for you to start developing with CUBA.

For this video, in addition to the CUBA Platform framework we will use a plugin for IntelliJ Idea - CUBA Studio.

## Project Creation

Let’s create an empty CUBA project with the project namespace - planner. We will use Java 11 as the default JDK and the latest stable CUBA version.

We will use an in-memory database - HSQLDB. This should be enough for the quick start. You can select another database from the drop-down list. Leave database connection parameters unchanged.

The project name will be Session Planner.

After the first launch CUBA Studio offers you to apply a trial subscription to CUBA Studio Premium. It is free for 28 days and gives us some useful optional designers. Let’s apply it.

On the left part of the screen you can see the CUBA Project navigation tree. In the center there is a Welcome page, you can use it for quick access to some CUBA functions, documentation, marketplace, and also versions and subscription information. 

On the right side of the screen you can see the Gradle Tool window. CUBA framework uses Gradle to build and run the application.

The standard IntelliJ’s build window is at the bottom. We can close some windows to get some space on the screen. The CUBA main menu provides quick access to the CUBA-specific features of the IDE.

## Data Model Creation

Now we will create JPA classes along with business rules like mandatory fields, unique fields and relations.
  
The business domain model has only two classes (also called entities): Speaker and Session.

To simplify the development process we added a visual designer to CUBA Studio that makes JPA entities creation easier. Right click on the “Data Model” and select “New -> Entity”.

First, we need to create the Speaker entity. It has three fields - first name which is mandatory, last name - optional, and email - mandatory and unique. Let’s add validation for the email field.  

CUBA provides an ability to specify a proper entity display in the string format in the UI - “Instance Name”. For the speaker, we will select both the first and the last names.

Let’s have a closer look at the entity designer. Next to the designer tab we can see generated text - JPA-annotated java class. They are integrated, so you can change the text and see all the changes in the designer and vice versa. Also, you can see the DDL Preview and can create indexes if required.

For instance, we expect a lot of searches by last name. To make this search more efficient we can create an index for the last name field.

Let’s move further and create the Session entity and link it to our Speaker class. First, define the attribute Topic - a mandatory string. After that, we will add the session start date and the time attribute. Add the duration of the session with validation. The session end date and time will be a calculated value, we’ll configure it a bit later. 

Now we’ll add a mandatory reference to the speaker. The relation is many-to-one, so, we’ll define an association field called speaker that references the speaker class. 
 
In addition to this, we’ll create a description field that will contain a session’s long description. For the instance name we will select a topic field instead of the description.

## Creating Calculated Attribute

Now, we need to create an auto calculated attribute for the session’s end date and time. First add the getter method and name it getEndDate. Mark the method with the @MetaProperty annotation. For the calculated attribute, we need to specify the fields to be loaded. In our case, these are startDate and duration. Then just add calculation logic. 

Notice that Studio highlights the method because we need to specify a text label for the attribute. Let’s add it to the message bundle. 

Now, go back to the designer and have a look. We see that the endDate field is added and it is read only and related attributes are specified.

Our domain model has been created. Now, we can create a simple UI to perform CRUD operations over the tables in the database.

## Generating CRUD Screens

CUBA Studio contains a UI screen generation wizard that helps us to create basic, but useful UI screens:

* Browser - to show the list of entities in a grid
* Editor - to edit one entity instance using a form-like user interface

First, we will create screens to work with speakers. Since this entity is pretty simple, we can accept the default parameters for the screen creation.

As you can see, each screen consists of two parts: XML layout that defines the screen appearance with the preview feature, and a controller, written in java, which is responsible for internal screen logic and events handling.

Let’s take a look at the structure of the screen’s layout: there are two main blocks. The first one defines the way we are working with data and the second block defines the  layout  that specifies the positions of the components on the screen.

Now let’s create a browser and an editor for sessions. Here we need to stop for a while and explain some bits of CUBA.

In the CUBA Platform, Entity View specifies which fields will be fetched from the database. You can define views in a separate file to use them in the different modules of your application, or create inline views while creating the screens.

So, let’s create an inline view. Just select the necessary data.

You can see that the corresponding fields are added to the screens.

In addition, set the default duration value for the new session to one hour. To do this, go to the editor screen and subscribe to the event InitEntity and set this value in the code.

Now, we need to generate scripts to create the database.

## Database Creation

To generate scripts for the database creation, you need to select the CUBA - Generate Database Scripts menu.

To apply those scripts and create the database, just click Create database. Aside from application tables, CUBA creates system tables where we store information about users, roles, tasks, and others.

## Running the Application in Development Mode

Now we can run the application in development mode from the IDE.

Just press “Run” in the IDE to start the application. You can see the application log in the studio’s “Run” window. After some time you can access the application using the browser. Let’s open the URL and log in to the application using  the username “admin”. The password is “admin” by default.

Let’s add some test data to the application: first, let’s add a couple of speakers.

As you can see, email validation works as expected. 

Now let’s add sessions for today and tomorrow. You can see that the endDate is calculated automatically.

Generated screens are good for basic operations, but in the real world, the UI is usually more complex. Let’s add a calendar view to browse visits in addition to the grid view.

## Customizing the User Interface

For the browser screen, we’ll add tab control, put a calendar on it and provide functionality to edit and reschedule sessions using this calendar.

Let’s wrap the sessions table into tab sheet in the sessions browser screen. Add one more tab page and put a calendar control on it.

Expand the TabSheet element to the whole screen. Studio asks for an id. In CUBA, we need IDs to reference a screen element in the code.

Also, let’s set id-s and captions for each tab.

Update the calendar - assign a data container and expand it.

Finally, expand the Session Table.

In CUBA, UI components are bound to entities and their properties. 

We will bind the calendar to the data collection fetched in the screen. Use the search field to find properties and bind:

* startDateProperty to a session’s start Date
* endDateProperty to a session’s end Date
* captionProperty to a session’s topic
* And descriptionProperty to a session’s description

Let’s make the calendar to show only working hours.

In addition to the visual designer, you can use the XML markup editor. Let’s add navigation buttons.

We can reopen the form to see the changes - the CUBA framework supports hot reload for screens. Now we can see sessions displayed on the calendar.

## Using Screen API

Now let’s invoke the session editor screen when we click on an event in the calendar. 

When we interact with the UI, events are generated. We can subscribe to those events to handle them. Let’s handle a click on a calendar’s entry. 

We need to invoke the editor screen to change the session’s properties. Let’s use the EditorScreenFacet. It is a non-visual component that provides an ability to pre-configure an editor screen.

Put it under the window element in the Component Hierarchy window. And set the properties:

* an ID
* We need an editor for the session class. Set the Entity class - session and the corresponding data container. 
* Screen class - SessionEdit.
* Set the edit mode.
* The screen will be opened in the dialog mode.

Go back to the event handler.  Inject the EditorScreenFacet. Pass the session entity received within the event object for editing. After that, we should show the editor.

Let’s reopen the screen and invoke the editor.

As you can see, we need to refine the session editor dialog to make it look better by adjusting its width and height. The simplest way to do this is set “auto” for both width and height.

Let’s reopen the screen again and see the changes.
Now it is time to add some business logic to our application.

## Adding Business Logic

In this section, we will use CUBA Studio to create a service that implements business logic and use this service in a screen controller. It will be a service for session rescheduling that will ensure that one speaker doesn’t have more than two sessions in one day.

Right-click on the service node in the CUBA project tree and create a SessionService interface and SessionServiceBean implementation. Create a method in the interface and implement it in the ServiceBean. 

The method will accept a session and a new session date and time. The service will return the updated Session instance. 

First, calculate the start and end time of the day where the session is planned.

We will use CUBA API for data access - the DataManager class. 

With DataManager we create a JPQL query to check if there are any sessions scheduled for the speaker in a defined time span and add parameter values to it. 

Then we check the query result and, depending on the result, we update the session with a new start date or just return the original session instance.

## Using Service in Screens

The service is ready, now let’s add it to the session browser screen. It will be invoked for the drag-and-drop event in the calendar. 

In the method that is subscribed to the drag-and-drop event, we add some code to extract the session entity from the calendar event and pass it to the service to reschedule this session. After that, we just update this item in the browser’s data container.

To have the new service redeployed, we need to restart the application. 

After restarting we can open the sessions calendar - and voila! We have drag-and-drop rescheduling functionality for the calendar! Let’s see how it works. Just add a couple of extra sessions and try to reschedule one of the sessions to the day that is already full.  

## Branding

In this section we will customize standard captions in the application, adding some branding.  

In CUBA, you can update resource files and override the standard text. Let’s update the text according to the application business domain - conference planning.

With CUBA’s hot deploy feature, all we need to do is to log in to the application again to see the changes.

## Marketplace

The framework comes with a marketplace that contains plenty of components, so you can easily add useful features like charts or maps support to the existing application.

You can install those components right from the studio using the “Marketplace” menu. 

Let’s add the Helium add-on. It’s a new visual theme that you can use instead of the standard themes. Just click install and apply the changes.

Now we need to stop the application and apply the init scripts of the add-on.

Run the application and go to the settings screen. You can find the added theme in the drop-down list. Select it and apply. Log in to the application again - theme is already applied. We can also open the theme settings screen and change settings with preview.

## Conclusion

The CUBA framework provides a lot of useful APIs to help you create business applications quickly. This quickstart shows only the basics of the CUBA framework and CUBA Studio. 

You can find more examples and tutorials on our website: cuba-platform.com.

Thank you for watching!

# Application. Captions

Quick start

A full stack Java framework for business applications development

Powered by

Data model and database creation

User interface development

Business logic implementation

Thank you for watching