- State: discussing
- Start date: ?
- End date: ?
- Docs: ?

# Summary

Release job authors should be able to find out in their drain scripts whether instance is being updated, removed, deleted, etc.

# Motivation

Use cases:

- skip draining data when deployment is deleted
- export data out of cluster when deployment is deleted
- move data to a different node (if it's the only copy) when instance is deleted
- check that data is replicated elsewhere before instance (and its disk) is deleted
- do not move data to a different node when instance is deleted
- deregister etcd member when scaling down instance group
- drain kubelet (talks to master) during update, but not during delete

# Details

## Scenarios

- instance is deleted
  - deployment is deleted
  - IG is deleted
  - instance is deleted
- instance is stopped
  - via IaaS
  - bosh stop
  - bosh stop --hard
  - bosh delete-vm
- instance is updated
  - bosh deploy
- instance is created
  - bosh deploy
- vm is deleted
  - bosh deploy
  - instance is deleted
- vm is created
  - bosh deploy/recreate/start/...
  - resurrection

## Proposed changes

Following environment variables should be passed into `bin/drain` script with modifications.

```
BOSH_JOB_STATE={persistent_disk: ...}
BOSH_JOB_NEXT_STATE={persistent_disk: ...}
```

possible job states: running, stopped, deleted

possible disk states: available, unavailable, deleted

- TBD: job vs instance?
- TBD: colocation of helper jobs: data-exporter?

# Drawbacks

...

# Unresolved questions

...
