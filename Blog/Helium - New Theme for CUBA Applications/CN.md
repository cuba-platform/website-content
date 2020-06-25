CUBA 框架一直以来定位的目标是业务系统的开发。业务系统的界面通常是给后台员工使用的，看重的是功能实现。多年来，界面外观和样式并不是后台管理系统的主要关注点，界面中的控件也更紧凑，唯一的原因就是：在单一屏幕中摆放尽可能多的控件，以提供足够多的功能。

但是在最近十年里，人们使用了越来越多的小尺寸触控屏设备，游戏规则因此而改变。要有易于触控点击的控件，它们之间需要有足够的间距，防止被误点击。应用程序的配色也变得偏于使用杂色和对比色。所以，近些年的现代应用程序在设计上更注重样式，其中非功能性需求都占了很大的一部分。

顺应趋势，我们很高兴推出新可视化主题 - 氦元素（Helium）！与以前的样式主题相比，氦元素主题更简洁、明亮，视觉噪音也更少。另外很重要的一点：能够以最少的工作量为最终用户和 CUBA 开发人员提供样式定制。

## 功能

首先要提到的是，新主题是动态的，意味着您可以随时在线更改应用程序样式！以前，CUBA 使用 SCSS 变量定义颜色，这样每次修改过后都需要重新编译。而新主题依赖 [CSS 定制属性](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties) ，即使没有页面刷新或重新登录，这些属性也可以在运行时立即起效。

![text](http://localhost:1338/uploads/968af991658147b8bd6d5612e791b32b.gif)

### 预设配色

开箱即用支持两种预设的配色方案：浅色（light）和深色（dark）。

终端用户可选择的主题需要通过下列属性配置（主题安装流程请参考后续章节）：

```
cuba.theme.modes = light|dark
cuba.theme.defaultMode = light
```

另外，也可以通过在线主题编辑器创建自定义的配色方案（点击下方的在线示例和编辑器章节名称了解更多细节）。

### 预设大小

主题自带三种内置的控件大小配置：小（small），中（medium）和大（large）。

跟配色方案类似，也可以通过主题属性修改：

```
cuba.theme.sizes = small|medium|large
cuba.theme.defaultSize = medium
```

### 设置界面

设置界面可以通过主菜单的 Help -> Theme Settings 打开。这里可以让最终用户自定义他们喜欢的配色和控件大小。该界面带有几乎所有的主要控件，所以用户可以预览他调整之后的界面大概什么样。

## [在线示例和编辑器](https://demo10.cuba-platform.com/helium-editor)

![text](http://localhost:1338/uploads/35b3cfaa0b3741d6ae83546f6ba04a74.png)

关于新主题的另一个重要部分就是其[在线](https://demo.cuba-platform.cn/helium-editor/)交互式编辑器。这里可以试试调整样式变量，马上就能看到调整结果 - 一旦样式满足您的需求，只需要点击下载按钮并按照提供的说明将其安装到您的 CUBA 应用程序中即可。

其实，在线编辑器最好的一点是：不只是开发人员可以使用。将这个链接发送给您的追求极致的客户，这样他们可以根据自己的偏好自定义配色，然后下载并发送结果给开发人员，只需要几分钟便可以应用新的样式。同时，编辑器本身也允许导入颜色变量，这样可以基于已有的配色做修改。

如需使用自定义配色，需要基于氦元素创建[主题扩展](https://doc.cuba-platform.cn/studio-chs/?_ga=2.97851994.413007509.1591447704-764315394.1514874997#generic_ui_themes)。

## 安装

该主题是通过 [扩展插件](https://www.cuba-platform.cn/marketplace/helium/) 的形式发布。该扩展兼容 7.1.5 以上的 CUBA 应用程序（注意，我们推荐您跟踪 CUBA 框架的 bugfix 版本并更新您的应用程序至最新版）。可以参考 Studio 的 [相关章节](https://doc.cuba-platform.cn/studio-chs/?_ga=2.93273304.413007509.1591447704-764315394.1514874997#add_ons) 了解如何安装扩展插件。

扩展安装完成后，可以通过下面的方式启用新的主题：
启动应用程序，打开主菜单下的设置界面：Help -> Settings。在可视化主题选项中选择 Helium 即可。

如需将氦元素主题设为默认主题，可以在 `web-app.properties` 中添加：

```
cuba.web.theme = helium
```

如果之前自定义设置过 `cuba.themeConfig` 属性，别忘了也需要添加氦元素属性：

```
cuba.themeConfig = +/com/haulmont/addon/helium/web/helium-theme.properties
```

如需`自定义主题属性`，可以在 web 模块的主包路径创建 `helium-theme.properties`，内容如下：

```
@include=com/haulmont/addon/helium/web/helium-theme.properties

cuba.theme.modes = light|dark
cuba.theme.defaultMode = light

cuba.theme.sizes = small|medium|large
cuba.theme.defaultSize = medium\
```

然后在 `cuba.themeConfig` 属性中注册一下该文件：

```
cuba.themeConfig = +/com/example/project/helium-theme.properties
```

如需深度定制该主题，包括应用自定义配色，则需要创建主题扩展。按照[该说明](https://github.com/cuba-platform/helium-theme#applying-custom-color-preset)操作。

## 路线图

我们计划继续开发氦元素主题并添加新功能，包括：

-   深色模式改进
-   在主题编辑器中提供更有用的模板
-   在浅色和深色模式之间自动切换
-   提供可自定义的 border radius 变量，同样也基于 CSS 变量。
-   提供 Figma 的 CUBA 套件。Figma 是 UI/UХ 设计师最流行的工具之一，他们可以用来创建界面模型。

## 结论

我们希望新主题和在线编辑器能用在您的新项目中，也欢迎愿意为应用程序带来新鲜空气项目使用。如果您有任何问题，可以在我们的[论坛](https://forum.cuba-platform.cn/)创建主题讨论。项目源码可以在 Github 找到： [主题](https://github.com/cuba-platform/helium-theme) / [编辑器](https://github.com/cuba-platform/helium-theme-editor)。期待您的反馈！
