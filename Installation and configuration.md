# Installation and Configuration

- Synchronize clocks across nodes
- Disable swap – sudo swapoff -all
- Ports
	- 7000: inter node cluster communication
	- 7001: SSL inter node cluster communication
	- 7199: JMX monitoring
	- 9042: client port
	- 9160: thrift
- http://docs.datastax.com/en/cassandra/2.1/cassandra/install/installRecommendSettings.html
- Startup
	- cassandra -f : run in foreground
	- cassandra -p <filename> : log process ID in named file; useful to stop cassandra by PID

---
