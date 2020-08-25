## Overview
IMAP扩展提供了一种可用工具，能通过IMAP协议将电子邮件消息集成到任何基于CUBA的应用程序中。该组件的主要模型设计是通过Spring应用程序事件与收到的email进行交互。

组件包含以下功能：

* 集成任何IMAP服务和CUBA应用程序。
* 调用email服务器功能的基本API方法：
    *   连接至服务器；
    *   获取和浏览邮件；
    *   邮件搜索；
    *   操作自定义标记。
*   预定义的事件，用来实现自定义的业务逻辑以及处理不同的更新。
*   配置IMAP连接设置和事件的用户界面。

[示例项目](https://github.com/cuba-platform/imap-addon-demo)