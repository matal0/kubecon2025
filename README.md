# kubecon2025
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
