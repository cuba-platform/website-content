
## Dashboards in CUBA Applications

Hello!

Today I’ll show you how to add dashboards to CUBA applications. A dashboard is a good way to visualize information, but quite often JAVA developers face the challenge of adding dashboards to their applications: the implementation may be complicated and resource-intensive, the team may not have a front-end developer and so on.

We are glad to offer an easy way to represent visual information using an open source Dashboards add-on. You can find it on the Мarketplace or on GitHub.

Every dashboard contains widgets which are individual visual elements based on a frame.  Placing widgets on a dashboard is provided by a wide range of layouts. For example, responsive layouts allow viewing dashboards on desktop, tablet or smartphone displays. Also you can set dashboard refreshing and interaction between widgets.

## Demo Application

And now I’ll show you how to create and configure a dashboard in 15 minutes. 

You must have seen our pet clinic demo project that can be found on the CUBA platform site. It demonstrates an IT system of an imaginary pokemon’s clinic with pre-filled data for clients, pets, vets and visits. 
Let’s use this app as a starter and add a couple of widgets and dashboards to it.

## Domain Model

First, take a look at its domain model.

Owner - an entity which represents personal data of pet owners.
Pet contains name, birth date and is associated with the Owner and PetType classes
Visit details are contained in the Visit class and are related to one pet.
Vet - an entity containing veterinarian names extends Person and is associated with Specialty class.

## Cloning the Demo Application

So, we are ready to start. Let’s go to the pet-clinic’s github repository. 

Clone it and open the project in CUBA studio.
Now we need to add the dashboard component to the project. Let’s open Add-ons window and go to the Marketplace. Find the Dashboard add-on there. Click Install and apply the changes. 

You can see that the add-on dependencies have been added to the build.gradle file and to the web.xml in the core and web modules.

And now, let’s run the application. Apply init database scripts...
… and follow the link to open the app and log in as an administrator.
And what can we see? - our main menu now has a new “Dashboard” top-level item.

Let’s create a dashboard with Main Dashboard name. At this moment we can only set a layout for it and we don’t have any widgets available for this dashboard yet. Let’s add some of them.


## Simple Widget

For the warm-up, let’s add a very simple widget.
Our widget will be showing an image of a pikachu that we’ll download from wikipedia.

To create a new widget we need to create a new screen fragment first. Let’s put it into the widgets package and call it, hmmmm…. , pokemon-art-widget.
Then, add the “images” directory under modules/web/ and put the downloaded picture there. Let’s update deploy task in build.gradle as well, to get this image deployed.
Ok, the next step is to put this image into our screen fragment using relativePath image loading option.
And now, we are ready to do the magic!
We need to annotate our screen fragment with @DashboardWidget and pass to it a widget name that the dashboard editor will use.
Great! Our first widget is ready.
Let’s start the application and play with it!
Open our dashboard in the dashboard editor and voila!, we can see our brand-new widget here!
Let’s design our Test dashboard:
Add responsive layout to the dashboard as the root element. Confirm the default responsive settings.
Add our Pokemon Art widget to it 
Give it a caption and an id corresponding to pokemon art
Turn on the Caption visibility
Open the style settings and set its size to AUTO/AUTO
Save the changes and check how our dashboard looks.
Now it seems to be a right time to talk a bit more about widget editing capabilities we have out-of-the-box in the dashboard editor.

Here we can set the Widget caption (which especially matters if “Show widget Caption” is checked)
And “Widget Id” - which could be useful when you look at JSON records that describe the dashboard structure either in the database or export/import JSON file. 

Widget parameters is a list of additional values that can be passed to the widget to have it configured by a user. We’ll talk about them later on.
Widget template editor allows us to save the configured widget - a widget with pre-configured parameters set as a template for later use at other dashboards.
Responsive settings dialog is used to configure the widget weight in the layout. 
The last configuration dialog is Style. Here we can define css style for the widget container and modify its width and height 

## Updatable Widget

For example, what if we’d like to see a random joke from the Internet on the dashboard and we want to get it updated every 15 seconds.
Ok, let’s do it and take a look at a couple of other tools that the dashboard add-on gives us.
Create a widget as we did before and name it random-joke-widget.
Add the textArea for the joke to the xml descriptor and set proper attributes for it.
Mark the widget with @DashboardWidget annotation
Inject textArea into the controller class
Make the widget controller implement the RefreshableWidget interface. This is a marker interface that is used to tell the add-on that it has to call the refresh method of this widget from time to time…
Ok, let’s override this method
And call (so far empty) the displaying new joke method to get a new joke.

But how can we set the very first joke? How our widget can be initialized?

Unlike regular screens, annotation @LoadDataBeforeShow doesn’t work for dashboard widgets. As well as handling the Screen.BeforeShowEvent for itself and for a parent screen.
We need to subscribe to InitEvent or AfterInitEvent to have our widget initialized.
Let’s use AfterInitEvent in our case. 
Subscribe to this event and initialise the textArea in it.
Now we need to get a joke itself.
We’ll be using icanhazdadjoke.com to get our jokes. The documentation can be found here (https://icanhazdadjoke.com/api)

We’ll be using RestTemplate class from the org.springframework.web.client package to do this task.
Okay. Our Random joke widget seems to be ready.
Let’s test it.
Add it to our test dashboard 
Set width and height to it
It works!
But how can we set the refresh rate?
There is an input field named Refresh period, let’s set it to 15 seconds.
This refresh method is not called in the dashboard design mode.
To see these refreshes, let’s save the changes and go to the dashboard View mode.

Here, we can see that jokes are updated every 15 minutes.

## Working with Data

Ok, it’s enough for simple widgets, let’s create the one that will work with data from our database.
The widget will show a calendar with highlighted visits in the selected month.
Let’s add a new screen fragment and name it visits-calendar-widget.
Mark it with @DashboardWidget annotation.
We need to work with data, so let’s add a collectionContainer and set its ID to “visitsDс”,
Set class to Visit,
View to “visit-with-pet”,
And add a data loader.

Then, add a vbox and Label,
datePicker to select the month to display,
and a calendar to our screen fragment XML descriptor.
Now it’s time to update the controller class:
Let’s navigate to the VisitsCalendarWidget and inject our dataLoader, DataContainer and Calendar.
As you remember, widgets don’t load data by themselves, and @LoadDataBeforeShow annotation doesn’t work with them.
So we need to subscribe to onAfterInit event and load data there.
Let’s do that by calling the load() me thod.

After that, let’s also add these Visits to the calendar control in Java code as well.
We also need to handle ValueChange event where we will set startDate and endDate for the calendar.
Now, let’s run the application.
Add “Visits Calendar” to our test dashboard and adjust its size.
We can ensure that our widget works correctly.

## Widget Parameters

As we have seen earlier, the add-on provides a parameters editor that allows creating parameters and passing them to your widget. Let’s look at how we can access these parameters in our widget.
Assume we want to be able to add a picture of our pokemon on a dashboard.
In this demo we’ll be taking these pictures from resources, however, in real application these pictures should be stored in the Pet entity as a FileDescriptor type property.
Let’s add a custom parameter first to our Pokemon Art widget
Set Value type = Entity
Select petclinic_Pet entity class
Set Alias to the pet - we will use this alias to get the parameter in the controller.
Give a name to this parameter, it could be any.

Select an entity instance and a view.

Ok, now our widget can get the parameter.
Let’s see how we can use it.

Put the images into the folder in which we’ve put the image of Pikachu.

Now, we need to create a class field of Pet type with exactly the same name as we earlier set our Alias to.
And annotate it with @WindowParam and @WidgetParam.
Also, let’s inject a widget itself to be able to set a new caption for it as well.
References to a Widget (which is technically a container of our screen fragment), Dashboard and DashboardFrame instances are passed as window params to our screen fragment controller. And could be accessed in the same way.
Also, we’ll need our image control to be injected into the controller as usual.
The only thing left is to rewrite our onInit event handler a bit,
Once again, this realisation is a bit straightforward and won’t work if a Pet object has a name that does not meet a resource name or can be used to load unauthorized resources from the top level folders.
But it’s just ok to demonstrate how to pass parameters into widgets.
Let’s check how it works. We can see that the picture has changed to the pokemon set in the parameter.

## Custom Widget Editor

You might notice that the widget parameters configuration interface is not quite user-friendly. In the end, how does a common user get to know which Entity or Entity List or enum should be used as a parameter and what are the allowed values for it?

Let’s fix this and the issues with security and parameters validation we have discussed earlier.

This can be done with a custom Widget editor. It would give better control over the passed parameters, allow us to validate them and give the end user a much more convenient and friendly UI for configuring a widget.
Let’s create a blank screen fragment first and call it pokemon-art-widget-editor.
Ensure that it’s not assigned to any web-menu item.

Then we need to add a new lookupField to the layout. This component will be used to select a picture to be shown in the widget. 

Disable the ability to select the null value, so a user will have to choose some picture to be shown.

Also, if you have not done it before add a message pack to the package.

Then, go to the screen controller and inject LookupField, petName and petArtResource, add  @WindowParam and @WidgetParam annotations. 

Let’s add some constants.

Subscribe to the Init event.
Handle ValueChangeEvent.

And implement listArtFiles method.
Now, switch to the PokemonArtWidget, open its controller class and specify the widget editor.

Replace an injected entity parameter called pet with two new String parameters - petName and petArtResource

Annotate these parameters with @WindowParam and @WidgetParam and get the caption and the resource from these two fields if they are not null.

And we just need to rewrite onInit method and add setPet method.
Let’s look at the modified widget editor. Configuring widgets became more convenient.

## Adding to the Main Screen

Adding a dashboard to the screen is pretty straightforward.

One of the most common places to present dashboards is the application’s main screen.
Let’s see how it could be done for our pet-clinic application.
Open the main screen window descriptor - ext-mainwindow.xml.
First of all, we need to add a dashboard xml namespace

Then find <main:workarea/> tag that represents the screen area where the application opens the screens - this is the place where we are going to put the dashboard we have created earlier.

We need to change the workarea’s layout a bit first: add a vBox to tell the screen how to arrange the picture we already have and the dashboard we are adding.

Then, we can add <dashboard:dashboard> tag, give it an ID and set code attribute to the code of the dashboard we have created before. In our case it will be “main-dashboard”.

Timer delay parameter plays the same role as the refresh period in the dashboard editor and overrides the last one if both of them exist.


Now, when the configuration is done it’s time to run the application and look at the new main screen.

Everything works as expected. We can see our main dashboard on the main screen.

## Dashboard Parameters

The next thing to do is to discuss dashboard parameters.
These parameters could be passed to dashboard either by using <dashboard:parameter> tag in the <dashboard::dashboard> component or by setting them in the dashboard editor.

Let’ create a parameter for the Main Dashboard in the dashboard editor and set its alias to the font-size (name could be any). Let’s set the parameter type to STRING and value to huge.
This parameter will be passed to ALL widgets of the dashboard, so we can inject it to our widgets using @WindowParam and @WidgetParam annotations and use it as we need to.

Look at the code.

The parameter is injected as we have seen before and used in onAfterInit method.
Let’s look at the results.

The widget font size changed.

## Dashboard Assistant Bean

DashboardViewAssistant is a helper component that can be used to manage your dashboards. It’s a Spring bean with “prototype” scope that gets a managed dashboard instance to its init method.

A dashboard assistant is a good place to keep the business logic for dashboards and widgets. 

Since users can choose which assistant bean should be linked to the dashboard in the UI, the assistant bean is a good choice if you need to allow users to turn some business logic ON and OFF or let users choose from one of the predefined business logic providers...
Let’s create such an assistant bean.

First, we create a package named assistant and a class called VisitScheduleAssistant. Mark it as a Spring component with “prototype” scope and say that it implements DashboardViewAssistant interface.

Now, we need to override the init method. It’s quite simple, we just save the dashboard object for the later use.
Ok, now our dashboard view assistant is ready, but does nothing. So it’s time to add some business logic to it.

Here, we will use a standard mechanism of Spring events and subscribe to the DashboardUpdatedEvent - an event that the add-on sends each time the dashboard needs to be updated.
First we need to check the ID of the visits calendar widget.
It’s “visits-calendar”.
So, let’s get this widget from the dashboard and call a reloadSchedule method for it.
Implement this method in VisitsCalendarWidget.

Do the refactoring and extract the data loading logic from the onAfterInit method.
And then, improve it a bit by loading visits only for the selected month.

To do that, add a condition to the data loader declaration in the XML file with two parameters: 
start date
And end date

Set these parameters to the start and end dates that the calendar is set to before calling the load() method.
And reload the schedule for the selected month in the MonthPickerValueChange event handler.
Our dashboard assistant is created, let’s run the application and check how it works.
Open the Main dashboard in the editor and set the refresh rate to 30 seconds and the assistant bean to the component we have just created.

Save the changes.
Take a look at the calendar in the main window. And create a new visit for the month that is picked on the calendar.

After some time, we can see that this new visit appeared on the calendar.

## Export and Import JSON files

And the last thing I want to show you is how to export and import JSON files. 

Let’s create a new dashboard and add the pokemon widget to it.

You can see two buttons at the bottom of the screen.

 Let’s export our Test Dashboard to the json file. Now you can use it in another project or in this one just by creating a new dashboard and clicking Import json.
But now I’ll show you one more way of adding a dashboard to the main screen. 

Put the Json file to the project package and set the path in the dashboard section instead of code. That’s it.

Let’s check that it works.

## Conclusion

As you can see we’ve done everything we planned. 

Join our community and feel free about asking questions.

Consult the documentation on our website and on the add-on home page.

Download and use other add-ons at CUBA marketplace.

Star us on GitHub and subscribe to our Twitter and YouTube channel!

All links are clickable under the video.

Thank you for watching!
