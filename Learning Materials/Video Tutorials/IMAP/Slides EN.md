## Diagram 1

Demo Application
Custom component
IMAP add-on
Mail Server

# We will talk about

1.Email handling principles
2. Demo application:
  1. Overview the app
  2. Adding and configuring IMAP addon
  3. Configuring SMTP ( EmailerAPI )
  4. Implementing IMAP event handling methods

## Diagram 2

Customer
Email
Task-management system
Create task from email
Send status update
Browse tasks
Reply task
Change status
Create task
User (IT specialist)

## Diagram 3

Create tasks from email
Get email responses
Browse tasks
Review task
Send responses
Change task status

## Diagram 4

receive email
email was processed
email is new
fetch imap email
create new TaskMessage
retrieve task by email subject
no task found
create new task
task is retreived
link message to task
commit changes
send response email

## Diagram 5

User opens Task
User replies to the Task
System creates TaskMessage
System sends out Email
System moves Task to REPLIED state

## Titles

Data Model
IMAP Event Types
IMAP Add-on API
Task browser and editor
TaskMessage browser and editor
IMAP events processing
Sending emails with SMTP
Thank you!

## Things to implement

. React to incoming emails
. Create Task and TaskMessage objects out of emails
. Link TaskMessage object to a proper Task
. Send confirmation emails for IMAP messages
. Send email when a new TaskMessage created from the UI