# kubecon2025
# Table of Contents
1. [Day 1](#day 1)
2. [Day 2](#day 2)
3. [Day 3](#day 3)
4. [Day 4](#day 4)

## Day 1
## Day 2
### keynote otel
standardize data collection & analysis
perses: dashboard as code (alpha), enabling cn principles
metrics, logs, traces
otel operator:
Instrumentation CRD
- inject auto-instrumentation for java, go w/o code changes, java-options
- Java libraries, frameworks, app servers (jetty, wildfly, websphere) and JVM (openJDK Eclipse Temurin) support otel
- app Deployment:
	annotations
- prometheus backend to store data (receiver)
- otel-collector ()

### kcp: k8s-like control plane
horizontally scalable control plan for k8s-like APIs
open-source

workspace structure: virt k8s cluster
APIResourceSchema
APIExport allow to access resourcesvia permissionClaims

provider (pg) exports APIExport
kcp as API gateway extends

### DoK From DB mgmt to AI
Gabriele Bartolini
LLM: latency is important, local caching
understanding change of using local storage
pg_vector AI extension
AI: data pre- & post-processing
declarative configuration through operators (principle)
simplify operational complexity
kubernetes storage is important for PG, CSI driver
scaling storage,
trident
AI strategies:
-infrence (push out model)=large objects from object storage fetched
-training
block storage allows immutable
AI requires fresh data, realtime data, event-driven processing (supply change analysis, anomaly detetion)
orchestrating agents that reacto on events, realtime decision on hjow to react on event/which agent to spin up
controlplane: package & group resources
local SSD cheaper than memory
isolate PG nodes from the rest, w/ optimized storage
plan k8s setup for data workloads

### sidecar debate
why understand engineering tradeoffs instead of blindly jumping onto a new thing?
linkerd
sidecar: not an object until 2023, they are a pattern, container next to container
run as long as app runs, shares cgroups, volumes
example: log streaming, vault, opentelemetry
nice way to add functionalily w/ chnaging app, like a library, owned by platform team
clear operational & security model, treat them like an app
service mesh: sidecar proxy handles traffic
downside: side truck, upgrading sidecar means restarting pods, init container race condition
job termination: sync between side and app
resource usage
two types that can have sidecars: initContainers, containers
hack: container starts only after postStart hook has finished
job: k8s doesn't know that side needs to stop as well
fix:
cont can have a type: sidecar and standard
sidecars ar initcontainers, restartPolicy=Always, no longer block start of other cont (not event
side always restart when app stop
side init before app is guarantee
side terminate after app
sidecar termination is predtable now

alternatives:
1. side
2. node proxies
3. ambient 
all use L7 proxies, where to put the proxy
pod prox, node prox,
ambient: L4only proxy on node, plus l7 functionality is at a shared tunable level
sharing means multi-tenancy
contended multitenancy
L4 fairness handled by OS
but L7 fairness is much harder, preemptive multitasking
Envoy linkerd
istio ambient

### Power of init containers: reducing DB mgmt toil and yelp
horizontal scaling, upgrades, restoring clusters from backup
casssandra cluster (nosql, non-relational DB)
initcontainers run sequentially, run to completion, no readiness/liveness probe
node joins cluster:
- seed and learn cluster topology
- token ranges assigned
- data stream to new node
Change Data Capture mode, stored commit logs in CDC raw dir
consumers read commit logs pusblish to kafka
issue: CDC events will be generatd for bootstrap events when initializing new node

solution: new pod schedule to a node during scale-up
launch init container
reduce voluntary disruption
prepare for involuntary disruptions
idempodent init container (e.g. check data on PV)
start cassandra w/o CDC
stop cassandra gracefully, exit init container
start cassandra with CDC

cassandra upgrades:
two cluster status, joined: up & normal, down & normal
roling updates failed earlier, 
do one after the other: change ip, upgrades

system table changes (Cassandra: SSTable): run nodetool upgradesstables required, but no control
solution: check SSTable format in initContainer, etc.


cluster recovery from backup:
medusa copies cassandra to S3
full or diff mode
backup runs periodically per sidecar

restore: deploy new casandra cluster, timestamp pitr yaml key

takeaways for initContainers:
idempotency
minimize disruption (it's a process not an event), use PDB pod disr budget for eliminating disruption
graceful exit of init container

### github attestation of secure code
build artefact,  deploy artefact, run artefact (github scubanjs/attestation-demo-py)
build python packag, SBOM, attestation created (commit history, timespamp, build trigger)

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
