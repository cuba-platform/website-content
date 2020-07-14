## Intro

Hi! Welcome to CUBA Platform Quick Start Video.
  
CUBA Platform is an open source framework based on the well-known Spring framework.
  
The purpose of CUBA is to streamline the development process of business applications.
  
This quick start video covers the following topics:

* Data model and database creation
* User interface development and customization
* And business logic implementation
  
As an example, we will create a simple, but fully functional version of an application that will help with session planning for a conference. 
  
The video will be just enough for you to start developing with CUBA.

For this video, in addition to the CUBA Platform framework we will use a plugin for IntelliJ Idea - CUBA Studio.

Let’s create an empty CUBA project and name it SessionPlanner. We will use Java 8 as a default JDK. CUBA framework uses Gradle as a build tool.

## Project

## Data Model

Now we will create JPA classes along with business rules like mandatory fields, unique fields and relations.
  
The business domain model has only two classes (also called entities): Speaker and Session.

First, we need to create the Speaker entity. It has three fields - first name (mandatory), last name (optional) and email (mandatory and unique). Let’s add validation for the email field. 
  
CUBA provides an ability to specify a proper entity display in the string format in the UI - “Instance Name”. For the speaker, we will select both first and last name.

If we have a look at the Java tab of the entity designer, we can see just a JPA-annotated java class. 

Let’s move further and create the Session entity and link it to our Speaker class. First, define the attribute Topic - a mandatory string. After that, we will add session start and session end date and time attributes. The session end will be a calculated value, being one hour from the start. 
  
Now we add a reference to the speaker. The relation is many-to-one, so, we’ll define an association field called speaker that references to the speaker class. 
  
For the reference field, let’s use the dropdown+lookup type. 
  
In addition to this, we create a description field that will contain a session description.

## Adding calculated field

Now it is time to create a calculated attribute - session’s end date. 
  
In CUBA, you can add field calculation logic using entity’s lifecycle callbacks. You need to create a method and mark it with a proper java annotation. The method will be invoked automatically.

Let’s add a method that will be invoked at the “PrePersist” lifecycle phase. 
  
Name the method “updateEndDate” and mark it with @PreUpdate annotation in addition to @PrePersist. 
  
We will create a separate method for the session end time calculation, to reuse it in the application later. 
  
In this method, we just add one hour to an input parameter.
  
And now let’s just add method invocation to the lifecycle method.

Our domain model entities have been created. Now we need to generate scripts to create a database.

## Creating User Interface

CUBA Studio contains a UI screen generation wizard that helps us to create basic, but useful UI screens:

* Browser - to show the list of entities in a grid
* Editor - to edit one entity instance using a form-like user interface

First, we will create screens to work with speakers. Since this entity is pretty simple, we can accept the default parameters for the screen creation.

As you can see, each screen consists of two parts: a controller, written in java, which is responsible for internal screen logic and events handling and an XML layout that defines the screen appearance.

Please note “DATA” section in the XML - it defines how the data is fetched from the database.

You can easily navigate between screens and linked entities with CUBA Studio.
Now let’s create a browser and an editor for sessions.

Here we need to stop for a while and explain some bits of CUBA.  Let’s talk about “Browse view” and “Edit view” fields.
In CUBA Platform, Entity View specifies which fields will be fetched from the database. 
  
For the session entity, we will not expose its end date for editing, but leave it for viewing. Let’s get started.

We will create a separate view for the “Session” entity to include a speaker reference for browser screen.

While creating a view for a session editing, we will deliberately exclude the end date end from the view, so the screen generator will not create a widget for it.

As you can see, there is no “End Date” field in the editor screen. Let’s update “description” field to use a multi-line editor.

And we are able to see the session’s end date in the browser screen.

Now we can run the application in development mode from the IDE.

## DB Creation Script Generation

By default, CUBA Studio uses HSQL database during the development stage.

To generate scripts for the database creation, you need to select CUBA - Generate Database Scripts menu.

To apply those scripts and create the database, just select CUBA - Create Database menu. Tables will be created for you. Apart from application tables, CUBA creates system tables where we store information about users, roles, tasks, etc.

## First Application Run

Just press “Run” in the IDE to start the application. After some time you can access the application using the browser. Let’s open the URL and log into the application using “admin” username. The password is “admin” by default.

Let’s add some test data to the application: first, a couple of speakers, then two sessions scheduled for the rest of the week.

As you can see, email validation works as expected.

Now let’s add a couple of sessions for today and tomorrow.

Generated screens are good for basic operations, but in the real world UI is usually more complex. Let’s add a calendar view to browse visits in addition to grid view.

## Customizing Interface

For the browser screen, we’ll add tab control, put a calendar on it and provide a functionality to edit and reschedule sessions using this calendar.
Let’s add tab control and two tab pages to the sessions browser screen.

It is recommended to assign an id for each control, so we can reference to it.

Now let’s set a caption for each tab.

Let’s drag the sessions table control to the second tab and expand tab control to the whole screen by setting “Expand” property in the parent component.

The next step - put a calendar control on the first tab, assign an id and expand it.

In CUBA, UI components are bound to entities and their properties. 
  
We will bind the calendar to the data collection fetched in the screen and bind:

* startDateProperty to a session’s start Date
* endDateProperty to a session’s end Date
* captionProperty to a session’s topic
* And descriptionProperty to a session’s description

In addition to the visual designer you can use the XML markup editor. Let’s make the calendar to show only working hours and add navigation buttons.
We can reopen the form to see the changes - CUBA framework supports hot reload for screens. Now we can see sessions displayed on the calendar.

Now let’s invoke session editor screen when we click on an event in the calendar.

## Using API

When we interact with the UI, events are generated. We can subscribe to those events to handle them. Let’s handle a click on a calendar’s entry. We need to invoke editor screen. For screen manipulations, CUBA provides a screen builder API that we will use. Let’s add ScreenBuilder class to the screen controller.

With ScreenBuilders API, invoking a screen becomes a chain of method calls. 
  
We need editor for the session class with this as a parent screen. Then we want to edit the session entity received within the event object. The screen will be opened in the dialog mode. 
  
When the editor is closed, we need to reload all data to the browser screen.
  
After that we should show the editor that we have just built.

Let’s reopen the screen and invoke the editor.

As you can see, we need to refine session editor dialog to make it look better by adjusting its width and height.

Let’s reopen the screen again and see the changes.

Now it is time to add some business logic to our application.

## Business Logic

In this section we will use CUBA Studio to create a service that implements business logic and use this service in a screen. It will be a service for session rescheduling that will check that one speaker doesn’t have two sessions at the same time.

Right-click on the service node in the CUBA project tree and create a SessionService interface and SessionServiceBean implementation. Create a method in the interface and implement it in the ServiceBean. 
  
The method will accept a session and a new session date and time. If we are able to reschedule the session, we save it with new start date and time and if not - just return false as the service execution result.

We will use CUBA API for data access - DataManager class. Let’s add it into the service first and then use it in the method. 
  
With DataManager we create a JPQL query to check if there are any sessions scheduled for the speaker in a defined time span and add parameter values to it. 
  
Then we check the query result and, depending on it, we update the session with a new start date or just exit from the method with a corresponding result.

## Using Service

The service is ready, now let’s add it to the session browser screen. It will be invoked for the drag-and-drop event in the calendar. 
  
In the method that is subscribed to the drag-and-drop event we add some code to extract the session entity from the calendar event and then invoke the service to reschedule this session. 
  
If the session cannot be rescheduled, we show an on-screen notification using the notification API that we will add to the screen.

To have the new service redeployed, we need to restart the application. 
  
After restarting we can open sessions calendar - and voila! We have drag-and-drop rescheduling functionality for the calendar! Let’s see it if works. We can add an extra session and try to reschedule it.

## Brand

In this section we will customize standard captions in the application, adding some branding.

In CUBA, you can update resource files and override standard text. Let’s update text according to the application business domain - conference planning.

With CUBA’s hot deploy feature, all we need is to log in to the application again to see the changes.

## Market place

The framework comes with a marketplace that contains plenty of components, so you can easily add useful features like charts or maps support to the existing application.

You can install those components right from the studio using the “Marketplace” menu.

## Conclusion

CUBA framework provides a lot of useful APIs to help you with creating a business applications quickly. This quickstart shows only the basics of the CUBA framework and CUBA Studio.
You can find more examples and tutorials on our website: cuba-platform.com.
Thank you for watching!

# Application. Captions

Quick start
A full stack Java framework for business applications development
Powered by
Data model and database creation
User interface development
Business logic implementation
Project creation
Data model creation
Creating calculated Attribute
Database creation
Generating CRUD Screens
Running the application in development mode
Customizing the user interface
Using Screen API
Adding Business Logic
Adding Branding
Marketplace
Conclusion
Thank you for watching