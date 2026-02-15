# Kubernetes: Fluentd

Fluentd is an open source data collector that allows users to **collect**, **parse**, **transform**, and **route** logs and other event data from many sources to many different backends

* Often used as the logging layer in Kubernetes clusters by runninng as a DaemonSet on each node
* Can turn messy logs into structured data (*JSON*)
* Can add additional metadata (*Pod name*, *Timestamps*, *etc.*)