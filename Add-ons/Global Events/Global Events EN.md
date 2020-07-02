Description
The Global Events add-on enables receiving Spring's application events on any application block of the distributed system.

## Overview

The add-on allows you to receive information about Spring container events from any application block of the distributed system. 

In the simple configuration running in a single JVM you can send events from `core` to `web` blocks to notify UI screens or managed beans. In a cluster environment, an event published inside a block will be received in middleware blocks and in the `web` and `portal` client blocks.

## Features

- Asynchronous events delivering, including the local deployment.
- Accurate ordering only within a block.
- There is no retransmit — delivery in the distributed deployment is guaranteed “at most once”.
- In the distributed deployment the web block starts receiving notifications once a user is connected.

## Links

- [Demo project](https://github.com/cuba-platform/global-events-demo)
- [Documentation](https://github.com/cuba-platform/global-events-addon/blob/master/README.md)