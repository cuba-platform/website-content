## 概述

密码历史和密码有效期策略是企业级环境需要考虑的一个关键要素。CUBA Password-plus 是一个实现了密码历史验证的扩展。

用户修改密码时，这个扩展会根据表中存储的密码列表对用户输入的密码进行验证，新密码验证通过后，旧密码会被存储起来以便将来检查。
管理员可以开启或关闭密码跟踪功能，也可以设置跟踪的密码数量。

扩展了 `UserChangePasswordDialog` 界面添加了新的验证，扩展了 `UserManagementServiceBean` 类的 `changeUserPassword` 方法将密码保存到新的数据库表。

[文档 ](https://github.com/pakuda/pswdplus/blob/master/README.md)