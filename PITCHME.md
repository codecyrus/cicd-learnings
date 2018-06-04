# <span style="font-family:Helvetica Neue; font-weight:bold">
<span style="color:#e49436">CICD Learnings</span>

* Lessons learnt from CICD
* Progress Updates to CICD Strategy/Tools
* Problems, Mitigations

---

## Runners Topology

Picture here

- Aim to provide a gitlab runner architecture
  - Across datacenters
  - Across infrastructure and tenants
  - Decentralized / self-service configuration

+++

## Zagreb Runners - Progress

- Demand for runners local to datacenters
  - Runner consistency required across DCs
- Shared Runners deployed in Zagreb
  - complements those available in Budapest
  - Some differences
- Allow routing/pinning of CI jobs to datacenters
  - CI Jobs just need tags (e.g. SCv2-HRZAGT1, SC-HUBUDB1)

+++

### Zagreb Runners - Progress ...

- Full end-to-end pipeline with terraform/ansible on SCv2
  - Better integration with Openstack (Over SCv1)
  - Runners themselves deployed by (meta) pipelines
  - Deployable using minimal requirements (just itot-jumphost)
  - Better separation of concerns
    - Env. config is split away from code
    - Forces consistency across runners in current/future sites.
- Ready to deploy runners in other (SCv2) sites

+++

## Runners - Use cases ..

- Designed for Non-Critical workloads, provided as best-effort
  - Facilitate the bootstrap of OS tenant deployments
  - Fallback for CI jobs run in Budapest (Caveats apply!)
  - Not a substitute for a real IaaS/PaaS
  - Services should be deployed to tenants
- CI Jobs will start to require tags with DC Label/Identifier
  - Requires update to .gitlab-ci.yml
  - Affects docker images (many/most tied to Budapest)

Forced to if zag is a fall back

+++

## Runners - Multi-stage CI Jobs

- Shared runners have problems
  - Runner outside env (SGs required to allow SSH, etc in)
  - Security of secrets (different jobs run as the same user)
  - (arguably) harder to debug than (re)using an accessible runner
- Our recommendations
  - Setup Multi-stage pipelines
  - Incorporate dedicated/private runners for in-tenant config/deploy
  - Easier for branch-to-env routing of CI jobs
  - We provide an ansible role and examples, documentation
  - `ansible-roles/ansible-gitlab-runner/`

+++

### Impediments - PNB

- Testing/adoption was blocked by lack of PNB
  - IPSec tunnels, poor bandwidth/latency to gitlab, etc
  - Problems with runner registration, preparation, git cloning
  - Need to ensure all DC rollouts are on PNB!!
- Possible improvement with bootstrapping
  - Runner needed to run runner deployment
  - We have a non-optimal hack using the itot-jumphost
  - Could deploy a runner as part of DC rollout (??)

+++

## Impediment - HTTP_PROXY

- SCv1 design requires use of a http_proxy to access remote content
  - Currently not possible to port these CI jobs as-is to Zagreb
  - Jobs/images for Budapest jobs 'bake in' environment config (e.g. HTTP_PROXY)
  - Env config can (should) be managed by runners (plan to fix)
  - Or we defer the need for HTTP_PROXY
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

### Repository for custom artefacts

- Repository infrastructure for VNF artefacts
  - VM images
  - CSAR Files
  - Test Artefacts
- Versioned artefacts
    - Metadata PropertySets (e.g. Datacenter location, VNF)
    - Rich query language
- Publish once - deploy anywhere
  - Aim to provide access across all datacenters
    - datacenter-local caching proxies or JFrog Enterprise+

+++

### Binary distribution + access

- Access vendor distribution repos/channels
  - Access of 3rd parties/upstream binaries
  - Point of control for binaries entering the system
- Binaries supported for most popular distributions
  - .deb (Debian, Ubuntu, etc)
  - .rpm (RHEL, Centos)
  - Containers (Docker/OCI, Helm chars)
  - Code (PyPi, Golang, Ruby Gems, PHP, npm, Maven/Gradle, etc)
  - opkg/ipkg
  - DevOps (Puppet, Chef) - No ansible/terraform :|

+++

#### Build infrastructure / Application lifecycle

![Package Stack](img/pkg-stack.png)

- e.g. Most deployments on Ubuntu 16.04/14.04
- What if we need a newer (latest?) version of upstream package?
  - To take on upstream improvements/features
  - Security hotfixes, bugfixes, etc
- Conversely, What if we need older versions no longer available?

+++

#### Build infrastructure

![Package Build Pipeline](img/pkg-ci-pipeline.png)

+++

#### Build infrastructure

- Gitlab + Runners + Artifactory
  - Gitlab mirrors upstream git repo
  - Gitlab triggers CI runner job to build software for target release
  - Runners revision/publish artefacts (build) via CLI or API
  - End-systems consume updates through repos
  - end-to-end automatic process

+++

### Trusted container repository

- Support for docker repositories
  - Will eventually replace the gitlab registry
- Opens possibility for image inspection (JFrog XRay/Enterprise+)
- Support for Kubernetes Helm Charts (Beta!)
- Odd requirement on DNS

+++

### Roadmap

- Q1-Q2
  - Expose out-of-box repos
  - Beta
- Q3
  - Further requirements gathering
  - Replication
  - Build Integration

### Rollout status

- Slow difficult process
- Beta has been available since end of march
  - Adoption is low
- Problems with connectivity to artifactory
  - Unable to prove integrations
  - LDAP/IDP
  - Gitlab Runners

### Rollout status

- Testing of typical use-cases is mostly complete
- Still to cover
  - VNF repositories (Onboarding/Orchestration/LTaaS)
  - Delegated administration (user/groups)
  - Bridge to gitlab (VCS repos - ansible, terraform, etc)
  - Repository Replication

+++

## TODO

![Branch To Environment Mapping](img/branch-env-mapping.png)

- why juju/foundation
- common bundle
- one bundle per deployment before -
- Start with branch-env


