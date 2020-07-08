## Overview
This application component automatically checks your CUBA application if it is considered "healthy". It will show the results of the health check to the administrator of the application with information on what went wrong and information on how to solve the issue.

This helps the administrator of your software in two ways:

1.  Installing and configuring the software as you (as a developer) intended
2.  Highlighting issues with the running software to the administrator

You can define your own health checks at development time or at runtime to check the state of the application. Here are some examples of possible checks:

*   Is a dependent service alive and able to take requests?
*   Does the database contain a Report you created and need in the app?
*   Do the custom filters stored in the database fit to the entity model?
*   Have all database changelog scripts been applied successfully?

For more information, please refer to [README](https://github.com/mariodavid/cuba-component-health-check#cuba-platform-component---health-check) on GitHub.