## Overview
The add-on is designed for coordinating CUBA cluster using ZooKeeper.

The default CUBA implementation of service discovery assumes that there is a static fixed list of available middleware servers. This list must be provided both for inter-server communication mechanism based on JGroups and for clients connecting to the cluster.

The add-on allows cluster members and clients to communicate through a  [ZooKeeper](https://zookeeper.apache.org/) instance or ensemble. It enables simple and uniform configuration of middleware and client blocks: they only need the ZooKeeper address to form a cluster and connect to it. In other words, the add-on provides dynamic service discovery.

For more information, please refer to [README](https://github.com/cuba-platform/cuba-zk#cuba-zookeeper-integration-add-on) on GitHub.