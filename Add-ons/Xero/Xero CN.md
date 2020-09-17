## Overview
该应用程序组件用来跟XeroAPI快速集成。

主要是利用了官方[Xero的Java SDK](https://github.com/XeroAPI/Xero-Java)。SDK的配置通过CUBA的应用程序属性来完成。

为了下载所有的依赖，需要在`build.gradle`里添加下列Maven仓库：


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

也可以通过Studio的仓库配置窗口添加仓库：编辑项目属性，然后点击Repository字段的按钮。

[视屏教程介](https://www.youtube.com/watch?v=A5HZM-wlOJA)绍了如何配置Xero SDK以便跟CUBA应用程序集成