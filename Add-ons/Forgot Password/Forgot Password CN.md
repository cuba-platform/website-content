## 概述

一旦在CUBA项目中添加了该组件，则能为项目提供这些功能：

*   在主登录窗口展示一个可选的“忘记密码”链接，用户可以给自己发送包含重置密码链接的邮件
*   允许通过登录名和电子邮件登录（这在使用REST API时也适用）。请注意，要求用户实体的电子邮件属性具有`UNIQUE`约束（简单地说，用户的电子邮件在系统中必须是唯一的）
*   开放新的REST服务（`extsec_UserManagementService`），使REST客户端能够通过API调用忘记密码功能
*   将`allowAnonymous`布尔属性添加到`rest-services.xml`文件，在没有授权令牌的情况下仅允许通过REST API（v2）调用某些方法。通过这个机制，允许在身份验证之前使用_extsec_UserManagementService_，但也可以通过自定义服务实现。