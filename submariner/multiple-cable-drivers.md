## Support multiple cable drivers within the same supercluster

## Summary

Submariner supports several cable driver implementations (libreswan, wireguard et al) however only one can be 
configured within the same supercluster, that is all clusters that join a broker must use the same cable driver.

There are use cases that involve supporting a mix of cable drivers or cable driver configurations within the same
supercluster. For example, a supercluster could contain three clusters, two that are on-premise and the other
in the cloud. Connections to the cloud cluster will need to use encryption while the on-premise clusters can use
unencrypted connections, as they’re connected by a private link, to avoid the performance overhead of encryption.
This could be achieved using different cable driver implementations or the same cable driver implementation with
differing encryption configurations.

## Proposal

### User-facing API

There needs to be a way for a user to specify their intent for two clusters to use a specific cable driver
implementation and/or configuration. At a minimum, the intent must include identifying information for the two
clusters and the cable driver implementation.

The standard mechanism in Kubernetes to specify intent is by creating a Kubernetes API resource whose effect is eventually
realized into the cluster's desired state. To extend the Kubernetes API, one could either use the generic Kubernetes
`ConfigMap` or use a custom resource definition (CRD). There's pros and cons to each but a CRD seems more suitable for
this enhancement.

This following CRD is proposed:

```
appVersion: v1
kind: ClusterLinkConfig
metadata:
  name: east-west-link
spec:
  leftCluster: 
    id: east
  rightCluster: 
    id: west
  cableDriver: 
    name: libreswan
    options:
      encrypt: true
```

The `leftCluster` and `rightCluster` fields identify the two clusters. The simplest identifier is the cluster's unique
ID or name within the supercluster. This could be extended in the future to allow for a label selector to identify one 
or more Submariner `Cluster` resources.

While semantically the identifiers for the two clusters uniquely identify`ClusterLinkConfig` instances, the
`leftCluster` and `rightCluster` field values could be reversed by the user in which case two instances semantically
the same would appear syntactically different. The implementation will need to handle this although it may be possible
to enforce semantic uniqueness on creation at the API server via a web hook. 

The `option` fields specifies cable drive-specific key/value configuration options.

### Implementation

TODO
 

      




 



