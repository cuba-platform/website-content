## Overview

Password history and expiration is a key feature for corporate environments. CUBA Password-plus is an add-on for CUBA Platform that implements password history validation.

When the user changes his password, the add-on validates the entered password against a list of stored passwords in a table. Once the new password is validated, the old one is stored in that table in order to be checked in the future.

The administrator can turn on or off the password tracking feature and set the length of the password tracking table.

It extends `UserChangePasswordDialog` to add the new validation and the method `changeUserPassword` of class `UserManagementServiceBean` to store the passwords on the new table.

For more information, please refer to [README ](https://github.com/pakuda/pswdplus/blob/master/README.md) on GitHub.