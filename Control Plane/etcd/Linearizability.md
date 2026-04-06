
* `etcd` provides robust consistency through linearizable reads and writes
* Guarantees strong consistency in distributed systems
* Makes read and write operations feel like they happen instantly on a single machine or even across multiple nodes
* Ensures that the final value is correct when multiple entities are reading and writing to a same resource
* Achieved via consensus (ex: *Raft*) but trades off some availability during network issues per CAP theorem