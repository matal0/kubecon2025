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
