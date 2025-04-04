# kubecon2025
## Day 3
### Starting & scaling platform teams
book: platform engineering: a guide for technical, product, and people leaders (o'reilly 2024)

when to start?
team skills
![Alt text](images/pf_lifecycle.jpg?raw=true "Platform execution lifecycle")
  - challenge complexity: software engineering, w/o sft eng it's hard to handle cmplx
  - challenge leverage: operational ownership, supporting & operating components, build solid foundations, system skills/engineers
  - challenge productivity: curated product approach, avloid cost-optimized offering devs don't like, focus on app teams

scaling:
platform product execution lifecycle
take other's innovation and make it accessible for others
taking ideas and develop a product takes to long
force adoption, make it ease to adopt
avoid tight coupling of platf to app
user-support/consulting, minimize migration pain
communicate with stakeholders: not customer, but their managers, budget process participants
let them know that you push value out
avoid beeing seen as optional nice to have team

when glue code slows you down, start an engineering team
main goals of platform: manage cmplx, create leverag, improve productivity

PM: customer empathy skillset
delivering quick wins, avoid long-running projects

4 failed pf teams:
plan: automation
reality: reactive ops for every new product load
no time to improve, etc

reset #1 => move to vendor solutions
plan:
reality: OSS behaves diffeeently between clouds
product teams lack knowledge and called support
less contact with product team
lost ops context

rest#2; SLAs/SLOs
plan: write SLA and processes
reality: rearchitect systems due to SKA
prod teams don't want processes (buraucrats) but problems solved
 
reset#3: build it and they will come
plan: launch new in-hous solutions
reality: mismatch, out of touch with business
no immediate demand, future platform language

reset#4: product thinking
plan:  whats useful for product teams in 12 months
reality: sucess, took longer nbut social proof of immediate business valu ebraouhgt patience
listen to cust requirements not their solutions

### Live migration of database fleet
clickhouse: oltp OSS DB
PVC holding metadata, data is in objstorage
problem of statefulsets: 
autoscaling requires pod eviction, resubmit pod
break first vertical sclaing: slow, puts pressure on remaining
=> make befor break (MBB):
add capacity first, then take away old one

requires multi-statefulSet:
Temporal: batch handling

### OPA Gatekeeper for FinOPS
policies as code, cost management

traditional use cases:
secret management, complance validation, network policies
finops use cases:
tagging compliance, compute optimization, namespace cost allocation, storage cost optimization

### On-premise k8s Saxo Bank:
motivation to exit cloud: compliance, reliability, cost
cluster & node creation speed
security benchmarking improvement
85% cost cut compute, storage (compare cooling etc)

issues:
cluster state after stop/start was broken
external help from ghostbusters required, PODs running on non-existing nodes
gains:
full control & visibility of control plane
etcd encryption enabled, earlier in preview mode
control over service account certificates

downside on-prem:
lack of external support

architecture:
use capi to provision clusters declaratively
replace minio with rook (due to apache license vs agpl-3.0 of minio)
crossplane for user and bucket management
bank vault seems outdated, replaced by sealedsecrets

### Canary deployments and feature flags:
canary deployments
separate deployment of new code from release functionality
challenges: deployment during maintenance window
multiple deploys per week, restart
new features, self-hosted apps

sol#1: staff, insiders +1d, early adopters +7d, stable +7d, laggards
andon cord, pulling stops assembly line, all deploys are stopped
issues: bugs found late in laggards, critical still made it to stable
#1 reason for stopping bugs was new features

sol#2: shortten time to upgrade from 15d to 5d (canary, canary +3d, stable 2d, laggards)
rotate canary, 5% of cust

gather feedback before updting everyone
trunk-based development (many small changes)

OpenFeature to create segments of users based onlocation, team, usage history, env, etc
feature flag hides changes to main, only available for staff
opt-in for early-adapters, then 33% then all others
pause rollout when issue occurs

### SIG storage:
what do we do: PVC and PV, storage classes, CSI, volumes etc
CSI drivers ar owned by SIG Cloud Providers

ephem vols:
ephemeral volumes has lifecyle of pod: emptydir, general eph vols
inject into pod: confMap, Secret, Downward API
CSI ephemeral volume can be provided by CSI drivers
image volume source represents an OCI object (container image)

hostpaht, NFS, iSCSI, FC, local

rel 1.32:
auto-remove PVC created by StatefulSet
whenDeleted

recovering from resize failure: allo user to recover
more granular reporting of resize status

Volumegroup snapshots: multi volumes snapshotted togther

SELinux relabeling with mount options
speed up container mounting
many files on volume, startup can take long
privileged PODs can no longer shared volumes (same SELinux label)

rel 1.33:
volume populators: any object as data source for a PVC, in add to snapshot or another pvc
DataSourceRef in PVC spec
DataSource only local objects, *Ref any namespace (cross NS transfer)

features in design/prototyping

### image snapshotters CERN:
CernVM filesystem
40Mio events per sec 24/7
containers allow to preserver workloads over decades
85% of container image is unused
typical cont is ~gigabytes

lazy pulling: download only layers needed when needed
fallback to legacy pulling if image is not support lazy
tar.gz snapshotter:
image in searchable tar.gz format (not standard OCI format)
=>caveat: all images need to be transformet

seakable OCI snapshotter:
no rebuilt required,
SOCI index built containing location of file in image
=> not all registries support additional artifacts (harbour does)

unpacked images on cvmFS
global read-only FS, like streaming-service for software
based on FUSE, Content addressable storage
unpack images by layers


## Day 4
### crossplane
- manages all resources
- allows to compose resources in high-level abstractions (XRDs)
- XRD is an API object w/ spec, status etc.
- controllers reconcile XRDs (e.g. S3 controller)

goal: build your own platform API
- define a schema/abstraction 
- define composite resource definition (XRD)
- use pipeline of functions to compose resources
  - reusable functions
  - templating functions

v2: focus on app composition, everything namespaced by default
example: docs.crossplane.io/v2.0-preview
1. define schema (xrd)
2. install composition functions (pytjon, helm, kcp, go everything)
3. create app
