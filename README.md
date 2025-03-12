# 1.0.0

Monitoring module should be destroyed/redeployed when upgrading from a pre-1.0.0 release.

- Add option to specify metadata string with module deploys and updates. Metadata is displayed in the Cluster UI.
- Add new dynamic option `topology.microbatch.force.delay.millis` to force a delay between microbatches. This creates fewer, larger microbatches that increases latency but reduces overall overhead.
- Add validation on module update that regular depot doesn't change to tick depot and vice-versa
- Add validation that global setting for depots/PStates doesn't change on module update
- Strip PState migrations in module launch in IPC so tests can be run
- Optimize backups implementation to eliminate interference with concurrent topology execution
- Optimize partitioners
- Optimize event handling for stream and query topologies
- Optimize monitoring module CPU load by lowering data retention checking frequency
- Bump babashka versions
- Bump data.json version
- Bump Netty version
- Fix yielding selects failing to efficiently paginate on subindexed structures
- Fix benign exceptions from syncing task 0 microbatcher state on other tasks
- Fix rare issue of supervisor failing to read worker heartbeats
