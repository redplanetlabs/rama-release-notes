# 1.7.0

May 27th, 2026

Clojure API users should use Clojure 1.12.4.

- Rama Cluster UI API is now OpenAPI 3.1.0 conformant. The specification is available at `/openapi.json`. This enables AI agents to explore a cluster and debug issues.
- Expose UUIDv7 helpers in Java as `com.rpl.rama.Helpers#randomUUID7` and Clojure as `com.rpl.rama.ops/random-uuid7`
- Add `com.rpl.rama.test/with-event-hook` that can be used to get fine-grained information during IPC tests about event execution, RocksDB reads/writes, and more
- Many improvements to error messages and stack traces
- Test PStates now implement `vget`
- Bump commons-io dependency to 2.22.0
- Bump Netty to 4.2.13.Final
- Bump Thrift to 0.23.0
- Fix issue where data transfer on module update or far horizon could wrongly stall when getting bandwidth limited
- Fix issue downloading log zipfile from cluster node when there are no log files
- Fix module log on Cluster UI not rendering after unsetting a dynamic option

# 1.6.0

March 16th, 2026

Clojure API users should use Clojure 1.12.4.

- Reduce latency of microbatch processing by eliminating overhead in priming and commit phases and initiating replication of PState changes during processing phase
- Fix handling of Zookeeper connections so Rama is tolerant to Zookeeper nodes being down
- Make topology.microbatch.pstate.flush.path.count dynamic option a PState option rather than just a topology option
- Bump Zookeeper dependency to resolve CVE
- Downgrade log4j so that %throwable works correctly in log templates

# 1.5.0

January 23rd, 2026

Clojure API users should use Clojure 1.12.4.

- Improve depot migrations to continue applying migration function to new appends to ease transition for clients appending old records to new records. This now requires depot migration functions to be idempotent.
- Change foreign clients to have a constant request source to eliminate unbounded session creation when using clients on many threads or virtual threads.
- Add support for Java 25
- Add return type hints to `microbatch-topology` and `stream-topology` in Clojure API
- Bump encore to 3.159.0
- Fix groupBy to work in single-threaded batch blocks

# 1.4.0

December 21st, 2025

Clojure API users should use Clojure 1.12.2.

- Fix replication issues when proxies are active by ensuring replicas always use same transform method as leaders
- Fix issue introduced in 1.3.0 where proxies would resync unnecessarily due to not heartbeating correctly
- Fix `key` navigator not adding key to PStates with a subscription when key was non-existent and set to `null` by the path


# 1.3.0

December 12th, 2025

Clojure API users should use Clojure 1.12.2.

- Improve data transfer reliability during module update and scaling when disk is slow
- Fix replication issues with `subset` and `key` navigators when using proxies on PStates
- Fix `subselect` transforms not working correctly with active proxy on the PState partition
- Remove logback transitive dependency inadvertently introduced in 1.2.0


# 1.2.0

October 15th, 2025

Clojure API users should use Clojure 1.12.2.

- Added "multi source" feature that enables the same depot to be consumed multiple times by the same topology. Additional sources must provide a "source ID" on the "source" call so progress can be tracked across module updates. The sources will sync with each other once they get close enough and process the exact same offsets together, atomically.  The Cluster UI for the topology has a new section showing which sources are synced or not. Once sources are synced, the module can be updated to remove the multiple sources and combine the code into one source.
- Add new foreign API on cluster manager to get the latest processed offsets for all partitions of all depots subscribed to by a microbatch topology
- Make timeout configurable for "wait for microbatch processed count" test facility
- Upgrade dependencies to resolve CVE warnings
- Add `cluster.metadata` config for `rama.yaml` of Conductor that gets displayed on top bar of Cluster UI
- Add `license.dir` config for `rama.yaml` of Conductor
- Add `Ops.CURRENT_TOPOLOGY-TYPE` / `ops/current-topology-type` functions to get the topology type (stream or microbatch) of topology where the code is running
- Add cluster manager function `getDeployedModuleNames` / `deployed-module-name` to get the names of all deployed modules
- Add default serializers for all primitive array types
- Add default serializers for some Collections/unmodifiable* types
- Reduce the amount of RocksDB logs per partition from 10MB to 1MB
- Fix `Ops.CURRENT_MICROBATCH_ID` / `ops/current-microbatch-id` to throw an exception rather than an error when called in a non-microbatch topology
- Fix cluster UI backend to not spam errors in log when monitoring module is shut down
- Fix invoking query topologies in outer joins
- Fix rare issue during module update where a stall would not be detected when some objects are chronically failing while others are succeeding
- Fix issue where license warning wouldn't display when there were multiple licenses installed
- Fix conductor launch to delete its tmp dir so extracted libs don't accumulate

# 1.1.0

June 2nd, 2025

Monitoring module should be destroyed/redeployed when upgrading.

Clojure API users should use with Clojure 1.12.0.

- Added "instant depot migrations" feature that enables all depot records to be transformed to a new value or removed from the depot. Like PState migrations, depot migrations take effect instantly by applying the depot migration function on read while the contents on disk are migrated in the background. https://redplanetlabs.com/docs/~/depots.html#_migrations
- Added "module operation log" to Cluster UI that shows all module activity including updates, scales, dynamic option changes, leadership balancing, and microbatch pauses/resumes
- Directly deleting a subindexed structure now does an efficient ranged delete on disk instead of orphaning the underlying values https://redplanetlabs.com/docs/~/pstates.html#_deleting_subindexed_structures
- Allow new keys to be added to fixed keys schema without explicit migration
- Add Supervisor config "supervisor.redirect.worker.stdout" which when set to `false` will not redirect worker stdout output to a .out file.
- Added feature for module code to specify default dynamic options for launch
- Add .getTaskThreadIds() method to ModuleInstanceInfo
- Move backup GC computation off the task thread to avoid fairness issues
- `CompletableFuture`s in the client API are now delivered on the client thread pool instead of on WORP/Netty threads. This prevents cascading issues from improperly blocking in callbacks.
- Change Supervisor to use `kill -0` instead of `ps -p` to check if worker process is alive, which is more reliable on certain Linux distributions.
- Allow migrations on module launch
- Add telemetry for the distribution of the size of replog entries
- Add telemetry for the distribution of the size of WORP messages
- Add module metadata to backup manifest and display in Cluster UI
- Add agg->init-fn and agg->update-fn helpers to Clojure API
- Add ops/current-random-source function to Clojure API
- Add path/java-path->clojure-path function to Clojure API
- Capture backup duration and display in Cluster UI
- Show Supervisor labels in the Cluster UI
- Foreign clients now validate the cluster version and throw a better error message if there's a version mismatch
- Clean up Rama library jar to not have web UI assets bundled within
- Bump encore to 3.145.0
- Bump truss to 2.1.0
- Fix: don't consider temporary PStates when validating module updates
- Fix: transforms with subselect that remove elements on a PState with proxies on it no longer skips first element
- Fix: error handling in backup runner now properly fails the backup immediately on any failure
- Fix: backups now coordinate with backup GC and won't fail because backup GC is happening on a task thread
- Fix: select-keys now works on maps returned by sorted range navigators

# 1.0.0

March 18th, 2025

Monitoring module should be destroyed/redeployed when upgrading from a pre-1.0.0 release.

Clojure API users should use with Clojure 1.12.0.

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
