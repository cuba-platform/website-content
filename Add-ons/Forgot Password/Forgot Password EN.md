Description
This application component enables displaying an optional Forgot password link in the main login window

## Overview
This application component gives the following features once added to a CUBA project:

*   Enables displaying an optional _Forgot password_ link in the main login window, allowing users to send themselves an email with a reset password link
*   Enables logging in by both login name and email (this works also when using REST API). **PLEASE NOTE** that requires a `UNIQUE` constraint on the `email` attribute of the `User` entity (simply put, users' emails must be unique in the system)
*   Exposes a new REST service (`extsec_UserManagementService`) that enables REST clients to use the forgot password functionality via API calls
*   *Only for framework versions 6.6-6.8.* Adds an `allowAnonymous` boolean attribute to the `rest-services.xml` file, allowing for only some methods to be called without an authorization token via REST API (v2). This is the mechanism that allows the _extsec_UserManagementService_ to be used before authentication, but can be leveraged by custom services too

For more information, please refer to [README](https://github.com/pfurini/cuba-component-forgot-password#cuba-forgot-password-component) on GitHub.