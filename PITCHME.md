# <span style="font-family:Helvetica Neue; font-weight:bold">
<span style="color:#e49436">CICD Learnings</span>

* Lessons learnt from CICD
* Progress Updates to CICD Strategy/Tools
* Problems, Mitigations

---

## Runners Topology

- Aim to provide shared runners to expedite CI jobs
- Across infrastructure and tenants
- Across any datacenter

+++

## Zagreb Runners - Progress

- First attempt at providing runners in a different datacenter
- Shared Runners deployed in Zagreb
- To complement those available in Budapest

+++

### Zagreb Runners - Progress ...

- Tighter integration with SC (v2) using terraform/ansible
- Runners themselves deployed by (meta) pipelines
- Deployable using minimal requirements (just ITOT-Jumphost)
- Good separation of concerns
  - Env. config is split away from code
  - Easier to provide consistency across current/future sites.
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

+++

### Impediments - PNB

- Testing/adoption is blocked by lack of PNB
- Use of IPSec tunnels between Zagreb and Budapest
- Very poor bandwidth (~40kbps) to gitlab
- Downloads of docker images _very slow_
- Other problems with runner reliability, registration, git cloning
- Jobs time out even before execution begins
- Need to be aware of this in future DC deployments

+++

## Impediment - HTTP_PROXY

- Currently not possible to port CI jobs as-is to Zagreb
- Jobs/images for Budapest jobs 'bake in' environment config (e.g. HTTP_PROXY)
- Docker images are not portable to other sites
- Env config can be managed by runners (plan to fix)
- Still requires end-users to update
- Untagged jobs? (Backup for runners in SC Budapest)

+++

### Problems

- (Non)-Portability of images prepared in SC-HUBUDB1
- SCv2 Reliability

---

### Artifactory

picture here ...

- The universal binary repository
- Pan-Net vision - add an important validator to the CI/CD pipeline
- Testing/Security/Pre-emptive monitoring
- Consistent deployments

+++

### Use cases

- Proxy for accessing application binaries from 3rd parties/upstream
- Binaries supported
- Can be done in a controlled fashion

+++

### Repository for VNF artefacts

- Artefact cache for VNF artefacts (VM images, CSAR Files)
- Versioned artefacts
- Taggable with PropertySets (e.g. Datacenter location, VNF)
- Publish once - deploy anywhere

+++

### Blockers

- Poor adoption - blocking full integration
- Misunder
- A story required to support users/groups in this model

+++

### Trusted container repository

- Support docker via docker repositories
- Per-team repositories
- Support for Kubernetes Helm Charts (Beta!!)
- Aims to replace gitlab registry

+++

#### Application lifecycle

![Package Stack](img/pkg-stack.png)

- e.g. Most deployments on Ubuntu 16.04/14.04
- What if we need a newer (latest?) version of upstream package?
    - To take on upstream improvements/features
    - Security hotfixes, bugfixes, etc

+++

#### Application lifecycle ...

- Gitlab mirrors upstream git repo
- Gitlab triggers CI runner job to build software for target release
- Runners revision/publish artefacts (build) via CLI or API
- End-systems consume updates through repos
- The entire end-to-end process is automatic

Package signing (GPG)

+++

### Status of the rollout

- Beta has been available since end of march
- Beta adoption is quite low
- Problems with connectivity to artifactory
  - Unable to prove integrations
  - LDAP/IDP
  - Gitlab Runners

### State of the union ...

- Testing of typical use-cases is mostly complete
  - Binary repositories
  - Unstructured repositories
- Still to cover
  - Delegated administration (user/groups)
  - Unstructured repositories (Onboarding/Orchestration/LTaaS)
  - Bridge to gitlab (VCS repos - ansible, terraform, etc)

+++

## TODO

- why juju/foundation
- common bundle
- one bundle per deployment before -
- Start with branch-env
