## Overview
This application component gives the following features once added to a CUBA project:

*   Provides a `UserProfileService` with methods for getting and updating the details of the current logged in user (what I call the _user profile_)
*   Exposes a new REST API endpoint (_Richardson Maturity Model - Level 2_ compliant), called `/rest/nxsecup/v1/userProfile` supporting GET (`getProfile`) and PUT (`updateProfile`) requests, and a `/rest/nxsecup/v1/userProfile/password` endpoint supporting POST requests for updating the user's password
*   Adds a `userProfile` screen, and corresponding menu item after the Settings one in the Help main menu
*   Optionally hides the _Change password_ button from the `Settings` screen (because that button is replicated in the `userProfile` screen)

For more information, please refer to [README](https://github.com/pfurini/cuba-component-user-profile#cuba-user-profile-component) on GitHub.