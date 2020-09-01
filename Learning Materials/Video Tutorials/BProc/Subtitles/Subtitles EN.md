## Introduction

Hi, everyone!

Today I’ll guide you through the key features of the Bproc add-on and show how to model and run business processes in CUBA applications. 

Modeling business processes is essential for many business applications. The add-on allows you to describe business process steps right in the project using the integrated modeler. Being a part of the application, it enables seamless integration with the runtime application parts and visual process customizing.

The add-on is based on the embedded light-weight Flowable runtime engine. Modeling of business processes is carried out in the integrated BPMN 2.0 visual designer based on the bpmn.io framework. Integrated DMN 1.1 decision table designer is used for complex decision logic.

Users may interact with running process instances using process forms. For simple cases, you can declare input dialogs right in the modeler. Also, you can use screens based on CUBA Generic UI to create process forms of any complexity.

Administrative screens for managing running processes provide a ready solution to view all running process instances, reassign user tasks, analyze processes execution history, etc.

Also, you can use API for working with business processes from the code.

## Demo Application

And now  I’ll show how to use the Bproc add-on in your CUBA application.

You must already be familiar with our petclinic demo project that can be found on the CUBA platform website. It demonstrates an IT system of an imaginary pokemon’s clinic with pre-filled data for clients, pets, vets, and visits. Let’s think of creating a business process for this app.

First, take a look at its original data model. Owner is an entity that represents the personal data of pet owners. Pet contains name and birth date and is associated with the Owner and PetType classes. Visit details are contained in the Visit class and are related to one pet. Vet is an entity containing veterinarian names, extends Person and is associated with the Specialty class.

We will add only two attributes to the Visit entity: Status - that will contain the current status of the visit. And recommendations - the vet’s note for an owner after the visit.

So, let’s look at the things we are going to implement in our demo project.  We’ll create a business process that starts every time an administrator creates a new visit. Then the visit is confirmed or canceled by the administrator. A chief vet should choose a vet to accomplish the visit or cancel it if a pet owner did not come. After creating a  business process we should go to the CUBA Studio and implement process forms and service tasks. Then we’ll finish modeling the business process and deploy the model. In the end, to ensure everything is done properly, we test the demo project.

And now we are ready to start. Go to the petclinic github repository and clone it. Open the project in CUBA Studio.

First, we need to add the Bproc add-on to the project. Go to the Marketplace and install the add-on. If you don’t have a subscription, you can get a trial version of the Bproc add-on on the add-on’s page at the CUBA Platform website.

## Extending Data Model

Before we start modeling a business process we need to extend our data model. 

Go to the Visit entity and switch to the designer. Add two attributes with String type: status and recommendations with unlimited length. These are all the necessary changes in the data model. 

We are almost ready, but let’s update our browse and edit screens a bit. Go to the Visit Browser descriptor and switch to the designer. Select the visitsTable and add one more column with the status attribute to the table.

Then go to the Visit Editor and add the status field. We don’t need to edit this property when creating or editing visits,  so make it non-editable. The same with the recommendations field.

That’s it. Let’s create the database. Go to CUBA -> Generate database scripts. Click “Create database” and confirm that existing scripts will be overwritten.

## Business Process Modeling

So, we’ve made all the preparings and are ready to start modeling a business process.

Run the application and click the link. Log in as admin with the password: admin. We assume that the default administrator user is the petclinic administrator registering and confirming visits. I have added three more users in advance. It is a user with the Chief role. And two users with the Vet role.

You can see the BProc section in the main menu. Expand it and select Modeler. Here we are going to model our process. In the beginning, there is only the StartEvent element on the canvas. On the right, you can see the process properties panel. Let’s set the id of the process. We’ll need it to be used in the code.

We remember that the process will involve three participants. Let’s move the pool element on the canvas and split the pool with three lanes. It allows us not to set assignees for every user task but only for lanes. Select the first lane and give it a name - Administrator. 
The second lane is Chief Vet and the third one is Vet. We’ll configure the Assignee sections for the lanes later. 

Our process will start every time a new visit is created. So a user won’t start the process. Select the StartEvent on the canvas and leave the No form property. Add two process variables: visit with the entity type and administrator with the user type. We will use these variables further in the process. 
We’ll make the process start automatically in the code and write a new visit to the visit variable and a user created the visit to the administrator variable.

We assume that an administrator will get a task to confirm a day before a visit. So let’s add the timer element. A timer intermediate event acts as a stopwatch. When an execution arrives at a catching event activity, a timer is started. The sequence flow going out of the timer intermediate event is followed, when the timer fires. 

In the context menu select the Append Intermediate element and change the type to the Timer Intermediate Catch Event. Set the Timer type to Date and put the process variable confirmVisitDate in the expression. We’ll set this variable in the code when the process starts.

A day before the visit the administrator who created the visit should get the task and confirm it or cancel. Let’s use the Task element. Give it “Confirm the Visit” name and set it as User Task.  Set form type: input dialog. The dialog will display two fields and we’ll put process variables there. The first one - existing variable visit, make it non-editable.  And the new one - owner’s comment. Set its type - multiline string - and make it editable.

At this step, the administrator has two options: to confirm or cancel the visit. An easy way to support this is to use outcomes. For each outcome, a button will be displayed on the form and the result will be stored in a special process variable. 
Let’s create a new outcome using the corresponding button of the Outcomes section. In the outcome editor specify an id - confirm with the same caption and select an icon CHECK in the icons list. And the second outcome - cancel, with an icon - BAN.

Then, add the Gateway element. It is used to model a decision in the process. Two sequence flows should leave the exclusive gateway: one for a User Task and another one for a Service Task.

Set the “Select a Vet” name for the first task. Change the element type to the User Task. Select a flow, set the confirm outcome for it and give it a name - Confirm.  After confirming the visit by an administrator a chief vet will get the UserTask.

Set a name for the second task. The task will set the “Canceled” status for the visit. Change the element type to the Service Task. We’ll be using a Spring bean for the task that changes a visit’s status. But we set it after creating it in the code.Select a flow and give it a name - Canceled. Set the cancel outcome for it. 
After the service task is performed the business process for the current visit finishes. So add the end event.

Now we can set the assignee for the first lane. Select the lane and choose: process variable - > administrator. That is the process variable we get after a new visit is created.

For the user task set the form type: input dialog. Add the variable that the chief vet will see in the dialog:
An existing variable - visit.
And ownerComment.
And the new one - vet with user type. Make it editable and required. Select UI component - LookupField for this field.  And enter the query that selects all the users from the database with the role Vet.

Let’s set an assignee type for the second lane with the User Task. Select the lane and choose the assignee type - User Provider. Later we’ll create a class to choose a chief vet programmatically.  

At the next step - the selected vet will get a task. Add a User Task and give it a name - Visit. Let’s use the Cuba screen for the form that appears for a vet. We’ll be able to select the screen after creating it. 

We will set fields and process variables that appear in the screen in code as well as outcomes. They appear in the list after selecting the created CUBA screen in user task properties.
At this step, a vet has two options: to complete the visit in case it happened or reject it if the patient did not come. 

Again, add the Gateway element. The first flow leaves the gateway - Service Task that sets the “Patient did not come” status. Give the flow a name. We will be able to choose an outcome when the CUBA screen for the previous User Task is set. Let’s use the same Spring bean as for the setting canceled status Service Task. And, also, we can set it after creating it in the code.

The second flow leads to the Service task that completes the visit. Let’s use Java Delegate class for this Service task. This class will be saving the vet’s recommendations, setting the visit status and sending an email to the owner.
Set a name for the flow - Completed. Add two endEvents.

And it will be useful to add one more timer. For the case when the visit is outdated for one day its status will be set automatically to Patient did not come. We should add a Boundary event and set its type to the Timer Boundary Event. Put the process variable overdueVisitDate in the expression

Give the name for the flow. The only thing left is to assign the third lane to the process variable - vet. Okay, we’ve created our business process and we’re ready to do some coding. Save the model as a draft. We’ll do final settings after all the necessary code is added. 

## Start Process Programmatically

Let’s begin with coding the start of the process.

Go to the CUBA Studio and open the Visit editor screen controller. We’ll need two constants: the business process id and the status for a new visit.
Subscribe to the InitEntity event. In the body, we’ll set the status for the new visit and set true for the boolean value that indicates a new entity. 

Then subscribe to the AfterCommitChanges event. Receive the edited visit and if it is new, start the process.  
Put the process variables as pairs of string names and values:
a new visit
a user who created the visit
and two dates:
creating our business model we said that the date for the first timer will be a date that is a day before a visit. But to be able to watch the demonstration of the process in work let’s set it to the current date plus 5 seconds. So the task will appear in the administrator’s tasks 5 seconds after a new visit is created;
and the date if the visit is overdue. Set it as a visit date plus 1 day. 

To start the process we use the BprocRuntimeService. Specify the process definition key, an instance name that is used as an optional process instance business key and process variables.
That’s it. Every time a new visit is created, our business process will start.

## User Provider

The next step is to create a Spring bean that implements the UserProvider interface. We’ll be using this bean in the second lane in our model to assign a task automatically to a chief vet. An interface has the get method that returns a user who should become a task assignee.

Let’s create a new bean with the ChiefVetUserProvider name. Make it implement the UserProvider interface.
Add a constant containing the user role name.

We need to override the get() method. In the body of the method, we get the list of all users with the role Chief. Use the DataManager to load data and our constant with the name of the role as a parameter.

In case the list of users is empty we’ll return an exception with the corresponding message. Otherwise, we’ll return the first user in the list. 
Ok, our User Provider is done.

## CUBA Screen

We remember that we planned to use a CUBA screen as a form for the UserTask appearing when a vet gets a “Visit” task. 

Go to Screens and create a new blank screen. Ensure that it’s not assigned to any web-menu item. Give it a caption - Visit. Switch to the designer and let’s model the screen.

Add the Form element and put the pickerField and two textAreas under the Form. Set the id and caption for the pickerField - Visit. MetaClass - Visit. Make it non-editable. In the first text area we’ll display the owner’s comment. Make it non-editable. The second text area will be used by a vet for entering recommendations. Let’s leave it editable.

Then add an hbox and put two buttons under it. Let’s set id’s and captions for the buttons. One is used for a visit completion and the second for the case when a pet owner did not arrive.

Now, switch to the controller. First, add two constants with the visit status. Then, we need to annotate our screen with the @ProcessForm annotation  to use it as a process form. Then the screen will appear in the process forms lookup in the modeler.

Let’s declare two outcomes that will be used for the sequence flow elements in the modeler. Use the outcomes attribute of the @ProcessForm annotation.

Inject the textAreas and pickerField. Add the @ProcessVariable annotation to declare that the injected UI component is a process variable. The values of Visit and Owner’s comment have already been set in the process, so they will be set to the corresponding fields when the form is displayed. The recommendation variable is not yet set, but the @ProcessVariable will be taken into account when we complete the task in the button click listener.

Inject the ProcessFormContext that contains information about the user task and useful methods for the user task completion.
And now we need to handle ClickEvents. Subscribe to visitCompletedBtn event. Create a TaskCompletion instance. Set the task outcome. Indicate that values of class fields annotated with the @ProcessVariables should be collected and saved as process variables. And complete the task.

Subscribe to PatientDidNotComeBtn event. Set the corresponding outcome. Let’s use the alternative way to define process variables and instead of using the saveInjectedProcessVariables() method define the variables directly. Our custom CUBA screen is ready.

## Service Tasks

And finally, let’s add service tasks. 

We want the process to be finished automatically, when an administrator cancels a visit or a visit happened. The only thing before completion is to set the corresponding status for the visit. Let’s create a very simple bean with the setStatus() method that gets a visit and a string of status as parameters.
In the body of the method set the status and inject the DataManager to commit the changes. That’s it. 

And the second service task finishes the process after the visit is completed. Let’s look at how to use JavaDelegate for this purpose. You should create a  new Class in the core module. Make it implementing the JavaDelegate interface which requires us to implement the execute method. It is invoked every time the process reaches the service task.

Declare the Expression field that is defined in the process model. And add a constant with the status name “Completed”.
Get process variables from the execution: visit and recommendations.
And we should reload the visit, it must contain information about an owner and an owner’s email. Let’s create a new view.

Set a status and recommendations for the visit and commit the data.
To send an email to the owner let’s get the email address. And get the string representation of the email template. Then call the sendEmail method. We don’t have a task to send a real email, so in the body of the sendEmail() method just write an info message to the log file.
For now we have implemented everything we planned in the code. 

## Testing Demo

Now it’s time to finish our business process model and look at it in work. Start the application, log in as administrator and go to the Modeler screen.
Open the business process model from the draft. Set the Spring bean for the service task “Set status “Canceled” and pass to it the process variable visit and the “canceled” status as method parameters.

Do the same for the service task “Set status “Patient did not come”, but pass the corresponding status to it.
Now we can set a User Provider for the second lane.
Check the “Visit” user task and select the created CUBA screen for it. We can see that the outcomes appeared in the task properties. Let’s set the outcomes for the corresponding flows.And set our Java Delegate class.

Now we are ready to deploy the model. After that you can find the process definition in the Process Definition screen. You can open it in the modeler and update or view details in the Editor screen.

Let’s test our business process. Go to the Visits browser and create three new visits. Now you can see predefined visits with no status and three visits we’ve just created with the New status.
Go to the My Tasks screen. We set the first timer to the current date plus 5 seconds, so the administrator already got the visits to confirm or cancel.

Let’s cancel the first one. Switch to the visits browser. The canceled visit got the corresponding status.
Now, confirm the rest of the tasks. Enter some information in the Owner`s comment field. The chief vet has received the tasks. Switch to the chief account with the password equal to the login. The chief vet has to assign visits to vets. 

Switch to the vet account with the password equal to the login. The vet has two visits. Open the first one. Here you can see the CUBA screen we created earlier. The owner’s comment field is now non-editable. Enter some recommendations and click “Visit completed”. At this moment the execute method of our Java Delegate class performs. Let’s look at logs and ensure it works correctly.

And at last check the case when a patient did not come. Open the last visit and click the “Patient did not come” button. Go to the visits browser screen and look at the status of our visits. All entries are correct. 

Ensure, that all process instances are finished, switch to the process instances screen and search for the active ones. The list is empty which means our business process and code work well!

## Conclusion

Allright, we’ve done everything we planned.
Join our community and feel free to ask questions.
Check out other add-ons at CUBA marketplace.
Consult the documentation on our website.
Star us on GitHub, subscribe to our Twitter and YouTube channels!
All links are clickable under the video.

Thank you for watching!


