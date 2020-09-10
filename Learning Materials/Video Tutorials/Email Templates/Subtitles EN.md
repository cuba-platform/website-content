## Introduction
Hi everyone! In this video, we will get an overview of email templates addon using simple application for managing users subscriptions. The add-on provides the ability to create an outbound email based on templates. By using this addon you can create, edit and delete templates, set groups for templates, customize them in the application code and send result emails. In this webinar, we will learn how exactly to do these things.

## Application description
For this webinar, we have prepared a sample subscription management application. In this application, we will send emails on subscription lifecycle events using email templates addon. There are two core entities:
Customer - contains information about the customer with an email address
Subscription - contains information about the subscription. One customer can have multiple subscriptions.

## Addon setup
Let’s start. First, we need to add email templates component to our application using CUBA Studio. To add the component, we just need to copy its coordinates from the Marketplace and then paste them to “Custom application component dialogue” in Studio.

## Design template
All configured templates can be found by default in Administration > Email Templates menu. We will create our first template using the visual editor. 
Email template requires “Name” and “Code” fields. The “Name” field is the human-readable template name. The “Code” field is a unique template name, this field can be used to find the correct template using CUBA API. 
In the template form, you can see the “Group” field. This field is not mandatory and can be useful if you have many templates in your project or you want to apply any security restrictions based on template groups. Those groups can be configured from the “Email templates” browser. 
Also, on the template form, you can configure default values for email, such as “Subject”, “From” address, “Copy” address and “Hidden Copy” address.
Our template’s name will be “Subscription Created” and the template code will be “subscription_created”. Also, we will fill the default email subject with “Subscription Created”. 
Let’s create our first email template content. We can simply drag all required elements from the palette on the right side to the canvas. 
The template will contain three rows. In the first row, we will add the company logo image. We can provide a link to the image or upload image from the file storage. 
The middle block will contain a message for the customer. In the template, we can use the template’s parameters. Those parameters will be created automatically. 
For example, if we type “Hi, ${customer.name} ${customer.surname}!​​​​​​​”  with parameters mask.
Then the system will create one template parameter with “customer” alias which will have type “Customer” entity. 
All parameters can be customized and configured in the “Parameters and Formats” tab. For each template parameter, you can set the correct parameter type and if it is required to configure the default value. You can apply format for parameters in the template too. 
For example, we can type “Your subscription will be expired on” and add subscription expiry date as a template parameter with a special mask with value format ${subscription.expiryDate?string(“YYYY-MM-dd”)}”. 
You can see that the new “Subscription” variable with the type “Subscription” entity is created. 
Our template is almost complete and we are able to check how this template will look on different device types. We can disable element borders and check how the template can be displayed on a PC, tablet or a smartphone. 
Let’s discover other visual editor features. For each visual element, we can configure style, specific properties and check elements structure for the template. 
Also, we can import already existing HTML code, view source code, preview HTML template in a new window and export the template as a report. 
Custom report from the template can be useful if you want more complex logic for your template or you need a report with multiple templates. 
To learn how to configure a report please visit “Reports” documentation page on CUBA platform web site.


## Template attachments
You can add attachments to your template. There are two types of attachments: report and simple file attachments. 
For the report attachment, you can set a new attachment name and default report parameters. These attachments will be processed as CUBA reports with provided parameters and result will be attached to the produced email.
File attachments will be added to an email as is. You can use image file attachments in the email body as inline email images.

## Report template
You can create a “Report” based on an email template. Template details and default values fields are similar to the fields in “Design” based template. For this template, you can select a report which will generate HTML content. 
Also, you can specify default report parameter values. If you want to use report document name as email subject you need to check “Use subject from report” checkbox. 
Let’s configure our report template with “Subscription updated” name,   “subscription_updated” code and “Updated subscription” report.

## Send template screen with preview
We have configured our templates and we can use these templates to send emails for our customers. To achieve this, we can use “Send” action from email templates browser. 
On the “Send email” screen you can see default parameter values for the email, email content and for each email attachment. If you want to review email content that will be generated, you can use the “Preview” button. If you press it, the generated email content will be opened in a new window. 
“Send” button will send the email which was generated from the template.

## API usage
Let’s check how we can add some automation to the application. 
First, we can add “Send” subscription action for the subscription browser. We have already created a simple screen where we are able to select an email template. From this screen, we will open the default “Send” email screen from the email templates addon. If we pass parameters to this screen and these parameters exist in selected email template then these parameters will be set as default in the send email form. 
To send our email templates automatically from subscription lifecycle we will create subscription entity listener.
In this listener, we will use email templates API to send a “Subscription created”  email when the subscription becomes active and “Subscription updated” email when the subscription was updated.
Email templates API provides a fluent builder interface to customize your email template. 
Let’s set the addressee and templates parameters for the “Subscription created” email template and then immediately send the email. 
“Subscription updated” email we will send async and we will add system administrator in the hidden copy.

## Application demo
Our application is ready for testing!
Let's create our first subscription for the customer and ensure that the resulting email was sent and correct. 
Then we will update subscription expiry date and check received “Subscription updated” email.
The application works as expected!

## Conclusion
We have completed the main goal of this tutorial.
Join our community.
Take a look at other CUBA addons at CUBA marketplace.
Consult the documentation on our website and on addons github pages.
Thank you for watching.
