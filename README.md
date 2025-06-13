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
