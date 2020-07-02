## Overview
Single-sign-on (SSO) for CUBA applications allows a user to log in to the multiple running applications by entering a single login name and password once in a browser session.

When using SSO, there are two types of applications:

*   Identity Provider (IDP) is an application that provides user authentication. It contains a login form for entering user credentials and checks the credentials against the list of registered users. Only one Identity Provider is allowed in an SSO environment.
*   Service Provider (SP) is a regular application that redirects to IDP for user authentication. SP should contain the same list of users as IDP (passwords do not matter though). SP provides authorization using CUBA security roles and access groups. There may be any number of Service Providers in an SSO environment.

An application can be an Identity Provider and a Service Provider at the same time, so you don’t have to setup a dedicated IDP.

_CUBA SSO uses custom HTTP-based protocol and currently does not provide integration with systems using standard authentication protocols like SAML or OIDC._

In SSO environment, when a user enters a Service Provider URL, the SP redirects to the IDP page for entering login name and password. After successful authentication, IDP redirects back to the SP application and the user transparently logs in to SP.