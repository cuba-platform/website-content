用户资料

## Overview
一旦在CUBA项目中添加了该组件，则能为项目提供这些功能：

*   提供`UserProfileService`服务，带有获取和更新当前登录用户详细信息（用户资料）的方法。
*   公开一个新的REST API端点（Richardson成熟度模型 - 符合二级标准）：`/rest/nxsecup/v1/userProfile`，支持GET（`getProfile`）和PUT（`updateProfile`）请求。还有 `/rest/nxsecup/v1/userProfile/password` 端点，支持以POST请求的方式更新用户密码。
*   添加一个用户资料界面，并在Help主菜单的Settings条目下添加相应的菜单项。
*  （可选）在`Setting`界面中隐藏“更改密码”按钮（因为在`userProfile`界面中复制了该按钮）