## Overview
This application component can be used for creating one-off custom DB updates that should be executed by an administrator through the UI.

For normal / small-delta changes of the DB, the default CUBA DB-update scripts mechanism should be used. But sometimes there are migrations (e.g., an existing database schema from another framework), which cannot be done with default mechanisms. This is where this application component comes into play.

For more information, please refer to [README](https://github.com/mariodavid/cuba-component-db-update-wizard#cuba-platform-component---db-update-wizard) on GitHub.