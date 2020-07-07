Description

An implementation of a single database multi-tenancy support for CUBA applications.

## Overview

The add-on implements a single database multitenancy support for CUBA applications.
It enables using a single application instance to serve multiple tenants — groups of users that are invisible to each other and do not share any data they have write access to.

The component supports two types of data:

- Common data — shared across tenants.
- Tenant-specific data — not accessible to other tenants.

All tenants have their own admin users which can create tenant users and assign tenant-specific roles and permissions.

## Features

- Access differentiation to data for groups of users within a single application.
- Simple configuring of tenant-specific entities.
- User interface for managing tenants.

## Links

- [Demo project](https://github.com/cuba-platform/multitenancy-addon-demo)
- [Documentation](https://github.com/cuba-platform/multitenancy-addon/blob/master/README.md)