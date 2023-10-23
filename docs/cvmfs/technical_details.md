# Technical details of CernVM-FS

CernVM-FS is implemented as a *POSIX read-only [filesystem in user space (FUSE)](https://en.wikipedia.org/wiki/Filesystem_in_Userspace)*
with [repositories](terminology.md#repository) of files that are served via outgoing
[HTTP](https://en.wikipedia.org/wiki/HTTP) connections only, thus avoiding problems with firewalls.

Files in a CernVM-FS repository are [*automatically downloaded on-demand*](what_is_cvmfs.md#features-ondemand)
to a [client](terminology.md#client) system as they are accessed,
from web servers that support the CernVM-FS repository being used.

Internally, CernVM-FS uses [content-adressable storage (CAS)](https://en.wikipedia.org/wiki/Content-addressable_storage)
and [Merkle trees](https://en.wikipedia.org/wiki/Merkle_tree) (like [Git](https://git-scm.com/) also does)
to store file data and metadata.

## Caching

CernVM-FS uses a [caching mechanism](what_is_cvmfs.md#features-caching) with a
[least-recently used (LRU)](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU) cache replacement policy,
in which configurable [local client cache](https://cvmfs.readthedocs.io/en/stable/cpt-details.html#disk-cache)
is populated via either a [forward proxy server](terminology.md#proxy)
(like [Squid](http://www.squid-cache.org/)), or from a [Stratum-1 replica](terminology.md#stratum1) server.

Both the proxy and the replica server could be within the same local network as the client, or not.

To help reduce performance problems regarding network latency and bandwidth, clients can leverage
the [Geo API](https://cvmfs.readthedocs.io/en/stable/cpt-replica.html#geo-api-setup) supported by
CernVM-FS [Stratum-1 replica](terminology.md#stratum1) servers to automatically sort them geographically,
in order to prioritize connecting to the closest ones.

Furthermore, additional caches can be made available to CernVM-FS, such as an
[*alien cache*](https://cvmfs.readthedocs.io/en/stable/cpt-configure.html#alien-cache) on a shared cluster filesystem
like GPFS or Lustre that is not managed by CernVM-FS, and a
[Content Delivery Network (CDN)](https://en.wikipedia.org/wiki/Content_delivery_network)
can be used to help limit the time required to download files that are not cached yet.
