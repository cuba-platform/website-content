## Overview
This application component lets you easily integrate with the Xero API.

It mainly leverages the official [Xero Java SDK](https://github.com/XeroAPI/Xero-Java). The configuration of the SDK is done via CUBA's application properties.

In order to download all dependencies it is required to add the following Maven repositories to your  `build.gradle`:

```
buildscript {  
    //...   
    repositories {
        maven { url "https://raw.github.com/XeroAPI/Xero-Java/mvn-repo" }
        maven { url "https://raw.github.com/XeroAPI/XeroAPI-Schemas/mvn-repo" }
        //...       
    }
}
```

You can add the repositories also using Studio repositories configuration dialog: edit Project Properties, then click the button in the Repository field.

A [video tutorial](https://www.youtube.com/watch?v=A5HZM-wlOJA) about how to configure the Xero SDK in order to integrate it with a CUBA application.