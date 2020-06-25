We are happy to present our new visual theme - Helium! Helium is much cleaner and brighter with less visual noise comparing to its predecessors. Theme comes with editor which allows to rapidly create custom color presets.

CUBA has always kept its target at business applications development. Business applications commonly introduce user interface for the back-office staff, where the most important part is how functional it is. For many years the look and feel was not the main subject for the back-office type of UI. Controls were rather compact with the only reason: to fit as many of them as possible into a single screen, making it as functional as possible.

For the last decade, more and more touch-screen devices with the shorter diagonal came into the game. This has changed the rules. Controls should be easy to tap on, having enough spacing in between. Colors go more motley and contrasty. Styling is now at a much higher priority than before, taking a significant part of non-functional requirements to any modern application.

Following the trends, we are happy to present our new visual theme - Helium! Helium is much cleaner and brighter with less visual noise comparing to its predecessors. Another important thing about the new theme is the ability to customize styling with minimal efforts for both end-users and CUBA developers.

## Features

First of all, the new theme is very dynamic, so you can change application styling right on the fly! Previously, CUBA used SCSS variables to define colors and this approach required recompilation every time we change them. The new theme relies on the [CSS custom properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties), which get applied immediately in runtime, even without page refresh or re-login.

![text](http://192.168.45.58:1337/uploads/2e15e9fab550420b8933b87477a7a9f4.gif)

### Color Presets
There are two color presets provided out of the box - light and dark.

To manage presets available for end-users modify the following properties in theme.properties (theme installation process is provided in the self-titled section below):

```
cuba.theme.modes = light|dark
cuba.theme.defaultMode = light
```

Also, you can create your own color presets using the online theme editor (more details to follow in the Online Demo and Editor section). 

### Size Presets

There are three built-in presets for size: small, medium and large:

Similarly to the colors presets, you can modify presets for sizes in theme.properties via the following properties:

```
cuba.theme.sizes = small|medium|large
cuba.theme.defaultSize = medium
```
### Settings Screen

The settings screen can be found in the main menu under Help -> Theme Settings. It will help each end-user to personalize their color and size presets. The screen illustrates all major controls, so users can preview how their application will look.

## [Online Demo and Editor](https://demo10.cuba-platform.com/helium-editor/)

![text](http://192.168.45.58:1337/uploads/c4e2f5bda0034208a72a075d1e4e29a3.png)

Another important thing about the new theme is its new interactive editor [available online](https://demo10.cuba-platform.com/helium-editor/). Here you can play around with the available styling variables and see the result right away - once the theme meets your requirements just tap the download button and follow the provided instructions to apply it in your CUBA application.

The best part of the online theme editor is that not only developers can use it. Send this link to your picky customer, so they can customize the colors in accordance with their preference and send the result, which can be applied within a few minutes. The editor itself also allows to import color variables to continue work on a preset.

To apply custom preset you will have to create a [theme extension](https://doc.cuba-platform.com/studio/#generic_ui_themes) based on Helium.

## Installation

The theme is distributed as an [add-on](https://www.cuba-platform.com/marketplace/helium/). The add-on is compatible with CUBA applications starting from version 7.1.5 (note that we always recommend following our bugfix releases and keep your application at the latest one). Find the add-on installation guide in [this chapter](https://doc.cuba-platform.com/studio/#add_ons) of the Studio documentation.

Since the add-on is installed you can enable the new theme in the following ways:
Start the application and open the settings screen available under the main menu: Help -> Settings. Pick Helium in the Visual Theme lookup field.
To make Helium available by default add 
```
cuba.web.theme = helium
```
to  `web-app.properties`. 

If you have the custom `cuba.themeConfig` property, do not forget to include the Helium properties: 
```
cuba.themeConfig = +/com/haulmont/addon/helium/web/helium-theme.properties
```

If you want to **customize the theme properties**, create the `helium-theme.properties` file in your main package in the web module with the following content:

```
@include=com/haulmont/addon/helium/web/helium-theme.properties

cuba.theme.modes = light|dark
cuba.theme.defaultMode = light

cuba.theme.sizes = small|medium|large
cuba.theme.defaultSize = medium
```

Also register this properties file in the `cuba.themeConfig` property:

```
cuba.themeConfig = +/com/example/project/helium-theme.properties
```

If there is a need to make **deep customization** including applying custom color preset, you will have to create theme extension. Please use the following [instruction](https://github.com/cuba-platform/helium-theme#applying-custom-color-preset).

## Roadmap

We plan to evolve the Helium theme and introduce new features, including:
* Dark look improvements
* More useful templates in the theme editor
* Auto-switching between light and dark looks
* Customizable border radius variable, also based on CSS variables
* CUBA component kit for Figma. Figma is one of the most trending tools for UI/UХ designers where they create screen mock-ups.

## Conclusion
We hope the new theme and its live editor will be useful for new projects and also for those who wanted to bring a breath of fresh air in their applications. If you have any questions, please create a topic on our [support forum](https://www.cuba-platform.com/discuss/). The source code is available on GitHub: [theme](https://github.com/cuba-platform/helium-theme) / [editor](https://github.com/cuba-platform/helium-theme-editor). Looking forward to seeing your feedback!