# <span style="font-family:Helvetica Neue; font-weight:bold">
<span style="color:#e49436">Infrastructure</span> as Code</span>

* CICD Learnings
* Runner Topology
* Artifactory
* Problems, Mitigations

---

## Runners Topology

- Aim to provide shared runners to expedite jobs
- Across both tenants and infrastructure
- Across any datacenter

+++

## Zagreb Runners - Progress

- Shared Runners deployed in Zagreb
- To complement those available in Budapest
- Testing/adoption is blocked by lack of PNB

+++

## Zagreb Runners - Progress ...

- Tighter integration with SC (v2) using terraform/ansible
- Deployable using minimal requirements (just ITOT-Jumphost)
- Good separation of concernts - config away from code
- Ready to deploy runners in other SCv2 sites

+++

## Zagreb Runners - Progress ...

- Use cases
- Facilitate the bootstrap of tenant deployments
- Designed for Non-Critical workloads, provided as best-effort
- May eventually be a fallback for runners in SC-HUBUDB1
- CAVEAT: CI Jobs must be tagged with DC Label/Identifier

+++

## Runner - Progress

- Shared runners have problems (security, debuggability)
- Pipelines should incorporate private runners for in-tenant config/deploy
- We provide an ansible role and example plays to help
- Role supports different executor types

+++

# Impediments - PNB

- Use of IPSec tunnels between Zagreb and Budapest
- Very poor bandwidth (~40kbps) to gitlab
- Downloads of docker images _very slow_
- Other problems with runner reliability, registration, git cloning
- Jobs time out even before execution begins
- Need to be aware of this in future DC deployments

## Impediment - HTTP_PROXY

- Currently not possible to port CI jobs as-is to Zagreb
- Jobs/images for Budapest jobs 'bake in' environment config (e.g. HTTP_PROXY)
- Docker images are not portable to other sites
- Env config can be managed by runners (plan to fix)
- Still requires end-users to update
- Untagged jobs? (Backup for runners in SC Budapest)

+++

# Problems

- (Non)-Portability of images prepared in SC-HUBUDB1
- SCv2 Reliability

---

# Artifactory

- The universal binary repository
- Pan-Net vision - add an important validator to the CI/CD pipeline
- Testing/Security/Pre-emptive monitoring
- Consistent deployments

+++

# Use cases

- Proxy for accessing application binaries from 3rd parties/upstream
- Binaries supported
- Can be done in a controlled fashion

+++

### Repository for VNF artefacts

- Artefact cache for VNF artefacts (VM images, CSAR Files)
- Versioned artefacts
- Taggable with PropertySets (e.g. Datacenter location, VNF)
- Publish once - deploy anywhere

# Blockers

- A story required to support users/groups in this model

+++

# Trusted container repository

- Support docker via docker repositories
- Per-team repositories
- Support for Kubernetes Helm Charts (Beta!!)

+++

# Backporting applciations

- Still on ubuntu 16.04 or 14.04 in some cases
- We do not take on upstream improvements, features, bugfixes, etc
- Enabler for internal backporting requirements
- Build on gitlab runners, publish to artifactory, deploy to end-systems

+++

# State of the -union-

- Beta has been available since end of march
- Testing of typical use-cases
- Adoption is quite low
- Problems with reachability across sites - PNB
- Unable to prove integrations and productionization

