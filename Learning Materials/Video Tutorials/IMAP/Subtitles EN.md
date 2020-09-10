## Email Processing in CUBA Applications with IMAP add-on

Hello,

Today we will touch a  topic of processing and sending emails from CUBA applications.

Quite often business applications have requirements not only to send emails, but also to manipulate with mailboxes. For example, help desk register incidents by an email, hotels get reservations and so on.


The process of such emails handling is usually built on IMAP protocol, which allows connection of multiple clients to the same mailbox and is much better in handling big number of messages than the good old POP3.

In this video we’ll talk about the basic principles of email handling with CUBA platform. And will go through the process of adding emailing capabilities to a sample application  using IMAP for getting messages and SMTP for sending them.

Both these protocols are well supported in CUBA platform. SMTP comes out of the box, and IMAP support comes with an open-source add-on.
This free component provides a readily available instrument for integrating IMAP email messaging into any CUBA-based application. And its’ main model is designed to interact with incoming emails using Spring application events.
API methods of the add-on allow CUBA applications to connect to IMAP servers, view and retrieve emails, search through them, operate with custom IMAP flags, et cetera...
Also, the component provides built-in user interface to configure IMAP connections and to browse the messages on the mail servers connected.

## Demo Application

In this demo we’ll be using a simple task-management system that gives end customers an ability to open tickets via emails which are sent to a service mailbox. 

Let’s use a term customer to name the employees of our company who send tasks to IT helpdesk via email and want to get responses and tasks resolutions directly back to their mailboxes.

On the other side, IT department engineers (we’ll call them Users in this tutorial) use task-management system to browse open tasks from the customers, send responses back to them when they need more info, or when the issue was resolved.
The workflow of the ticket is shown in the diagram.

Let’s take a closer look at the part that is connected to email receiving and sending.

An email that hits the tracked mailbox tells the application to create a new TaskMessage and check if a corresponding Task exists in the system.
If not, it creates a new Task object.
After that, this inbound TaskMessage is linked to the Task. 
And the appropriate confirmation email is sent to the customer.

A User can reply to a Task by creating a new TaskMessage with UI.

This creates an email, sends it out to the customer (or task reporter) and moves the task to the REPLIED state.
Because of time constraints we won’t make this application from scratch in this video, but rather take a skeleton application that already has the core entities implemented.

These core Entities are:
Task - it represents a task (for example an IT issue) managed by our demo app
TaskState - an enumeration class to keep the state of the Task.
TaskMessage - this entity keeps IMAP messages that are connected with the task.

Also, the skeleton demo app has UI screens for basic Create/Update/Delete and Browse operations for these entities.

Сlone the skeleton demo application, 
open it in CUBA studio 
and check out what it looks like.
Let’s start with adding emailing capabilities to our application by adding an IMAP add-on to our project. 

To do this 
Click project properties of the demo project at CUBA tab
At the section “App components” click on the plus button and add the IMAP component coordinates that could be taken from the marketplace.
If you prefer to do this manually, it could be done by adding the add-on to the project’s build.gradle file and web.xml files for core and web modules.

The next thing that should be done is configuring the IMAP add-on. Let’s open the add-on’s github page and follow the component’s README.
Open the app.properties file of the core module
Copy and paste configuration parameters from the README file.    

Now, let’s start the application and configure the IMAP synchronization scheduled task.

Log in to the application with administrator user.
And add scheduled task as it is said in README
For the demo purposes let’s set the period of 60 seconds for this task.
Save and activate this task.

Let’s configure a service email box for the demo app we will be working with.

In this demo we are using an email on our corporate mail server, but nothing would be changed if it was a free email service like gmail, for example.

Click “Connect” button to test the connection.

Now we need to set a custom flag, which is required and is used by a component to mark emails that have been processed by your app. 
It could have any value, so let’s set it to cuba-imap

Other options could be kept in the default state.
Ok, IMAP has been configured, let’s see if there are messages there.

Nice, we can see what’s in our IMAP mailbox now. Basic IMAP configuration and integration has been done.
Now let’s configure email sending mechanism. 

Our Application will be using standard CUBA EmailerAPI interface to send messages asynchronously over SMTP.

To enable asynchronous email sending in our application we need to create a scheduled task that calls Emailer → processQueuedEmails.

And activate it.
Next, we need to provide configuration parameters like: ‘from address’, ‘server address’, ‘port’, ‘SMTP user’ and ‘password’. 

Let’s do this.

Open Application properties screen.

And set parameters for address, host, port, user, password and authentication mode.

Alternatively, these parameters could be entered to app.properties file of the middle layer if you prefer to keep them on the source-code level...
This concludes the part of the application configuration. Now it’s time to get our hands dirty and write some Java code!

## Adding IMAP Support

What shall we do to finish our application?

There are several  things left:
The application should react to the incoming emails and create TaskMessage and Task entities and send responses.
Second, It should link TaskMessages to a proper Task if the incoming email has the correct task ID in the subject field.
Third, It should send out a confirmation when an email has been received over IMAP.
And fourth, it has to send out emails when a new TaskMessage has been created from the UI by a user.

Let’s add incoming email handling capability.

First, add a reference from TaskMessage to ImapMessage entity. 
And a string to keep the original server’s IMAP message UID.
We can do this with the entity editor.

That’s how the result looks like in Java code:
Second, let’s create a bean at the middle layer and call it ImapMessageBean.

Let’s add a logger to the handler and test how the message handler works.

Attach it to IMAP events pipe by marking it with @EventListener annotation.

Please note that event handlers that introduced in this way will be called for every IMAP mailbox configured in the application and for every folder in these mailboxes.

Start the debug session.
Set a breakpoint to this logger line.
Create and send an email to our test mailbox.

Fine, we can see that the handler works just perfectly.
In some cases we might not need to track all incoming emails. 

We may want to do this just for one of the IMAP mailboxes if we have more than one IMAP mailbox connected, 
or we might want to track just one of the folders of this mailbox.
Or there might be a need to have different handlers for different mailboxes…

For such cases, the subscription to IMAP events could be configured right from the UI.
Let’s see how it could be done in our case.

First, let’s create a new method in ImapMessageBean
Then open IMAP configuration Editor.
And, attach the listener to “INBOX” IMAP folder.
Now, our listener should be called only when a new email hits INBOX folder of the specific mailbox.

In this video we will use the annotation-based approach. 

Let’s also take a look at other IMAP event types:
NewEmailImapEvent - the one we are using and which is called when a new email appears in the folder at the IMAP server.
EmailSeenImapEvent - is triggered when a message is marked with the SEEN message flag.
EmailAnsweredImapEvent - is called when an email is answered.

For details regarding these and other event types, please consult the documentation at the component’s github page.
Now, it’s time to add some real logic to our event handler.

First, we need to check if the message that we are handling has been processed already. 

Then, we fetch the IMAP message using one of the two principal API interfaces that the IMAP component provides to us: ImapAPI
 
We use ImapAPI.fetchMessage method to get the message data, and then,
Make new TaskMessage entity in a standard way through metadata.create method.
Check if Task entity already exists and create if it’s not.
Fill the Task object with data if it’s new and didn’t exist before.
Link TaskMessage to Task entity.
Commit the changes to the database.

The next step is to send a confirmation email back to the customer, to inform her that the Task has been created or updated.

But before doing that, let’s talk a bit about ImapAPI interface we have used in this method.
The IMAP component offers more than just one method in the single API interface. There are various methods that allow developers to work with messages, IMAP folders, imap flags and attachments.

Please consult the component’s documentation on github for more details regarding ImapAPI and ImapAttachmentsAPI interfaces.

## Sending emails with SMTP

This application uses standard CUBA EmailerAPI interface to send out emails over SMTP.
Since we have configured SMTP earlier, we only need to write code to send the emails.

As we know, we have to send emails from two different classes - TaskMessageListener and ImapMessageBean

So, it would be better to create a dedicated bean to handle this functionality.

Let’s do this and name it ResponseEmailAsyncSenderBean.

In this bean let’s define a couple of methods for sending messages using different email templates.
For new task creation confirmation email.
Task update confirmation.
And response email which is sent when user saves the TaskMessage created as a Reply for the task in the UI.

Create an implementation method and call it sendResponseEmail.
This method will be doing actual email sending job.

First, fill the parameters required by our template.
Then, set fields of EmailInfo object out of provided task, message and imapMessage objects.
And finally, send it out asynchronously.

Now, let’s call proper methods of this email-sending bean in our ImapMessageBean class.
The application is almost done.

The only thing we need to do is to implement sending an email when an application user replies to the task in the UI.
The easiest way to do this, as it was mentioned earlier, is to add email sending call into onBeforeInsert method of  TaskMessageListener class.

This method is called when a new TaskMessage object is about to be committed to the database. It already links reply messages with the parent Task entities. 

So, we can add asynchronous email sending logic there as well.
As the final touch, we may do a small refactoring and move ImapMessageBean and ResponseEmailAsyncSenderBean from global to core module. Since the are used only by middleware layer.
Ok, the application is ready for testing!
Let’s create a new task by sending a new email to our service email box.
And check that the task has been created.
Ensure that the service replied to us with a confirmation email.
Reply to this email adding more details to the original task.
And finally, reply back to our customer, saying that her request was done.
Check that this “completion email” has been received by the customer.

The application works as expected!

## Conclusion

We have completed the main goal of this tutorial.

Join our community.

Take a look at other CUBA add-ons at CUBA marketplace.

Consult the documentation on our website and on add-ons’  home pages.

Star us on github. Subscribe our Twitter and YouTube channel!

Thank you for watching.
