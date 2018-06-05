# <span style="font-family:Helvetica Neue; font-weight:bold">
<span style="color:#e49436">CICD Learnings</span>

* Lessons learnt from CICD
* Progress Updates to CICD Strategy/Tools
* Problems, Mitigations
* Your contribution - ideas, improvements

---

## CI/CD

![CICD Pipeline](img/cicd_pipeline.png)

+++

- Continuous Integration is the practice of integrating code into a shared repository and building/testing each change automatically
- Continuous Delivery adds that the software can be released to production at any time, often by automatically pushing changes to a staging system.
- Continuous Deployment goes further and pushes changes to production automatically.

+++

![Video](https://raw.githubusercontent.com/shalomb/cicd-learnings/master/img/cicd-in-practice.mp4)

---

## Gitlab

- Version: 10.7 (One month behind Gitlab)

![right fit](img/gl_stats.png)

+++

## One year ago

![inline fill](img/gitlab-statistics-2017.png)

+++

## Gitlab CI/CD Statistics

![right](img/gl_records.jpg)

+++

- Total Pipelines: 34937
- Total Jobs: 117579
- Pipelines / day: ~90
- Jobs / day: ~300
- Shared runners: 4
- Private runners: 10

+++

## Gitlab CI/CD
- .gitlab-ci.yml file to define the stages and jobs
- Gitlab Runner is used to run the jobs and send the results back to GitLab

+++

![right fit](img/ci-cd-architecture_2x.png)

+++

## gitlab-ci.yml example

```yaml
image: docker:latest

stages:
  - test
  - build

variables:
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

before_script:
  - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY

test_build:
  stage: test
  tags:
    - docker
  script:
    - docker build -t $IMAGE_TAG -t $CI_REGISTRY_IMAGE:latest .

build:
  stage: build
  tags:
    - docker
  script:
    - docker build -t $IMAGE_TAG -t $CI_REGISTRY_IMAGE:latest .
    - docker push $IMAGE_TAG
    - docker push $CI_REGISTRY_IMAGE:latest
  only:
    - master
```

+++

![right fit](img/pipeline.png)

---

## Runners Topology

- Push for more IaC
- Aim to provide a gitlab runner architecture
  - Across datacenters
  - Across infrastructure and tenants
  - Decentralized / self-service configuration

+++

## Zagreb Runners - Progress

- Demand for shared DC-local runners
  - Runner consistency required across DCs
- Shared Runners deployed in Zagreb
  - Complements those available in Budapest
  - Some differences
- Allow routing/pinning of CI jobs to datacenters
  - CI Jobs just need tags (e.g. SCv2-HRZAGT1, SC-HUBUDB1)

+++

### Zagreb Runners - Progress ...

- Full deployment with terraform/ansible on SCv2
  - Runners themselves deployed by (meta) pipelines
  - Deployable using minimal requirements (just itot-jumphost)
  - Better separation of concerns
    - Env. config is split away from code
    - Forces consistency across runners in current/future sites.
- Ready to deploy runners in other (SCv2) sites

+++

## Runners - Use cases ..

- Designed for Non-Critical workloads - best-effort
  - Facilitate the bootstrap of OS tenant deployments
  - Fallback for CI jobs run in Budapest (Caveats apply!)
  - Not a substitute for a real IaaS/PaaS
  - Services should be deployed to tenants
- Tags are DC Label/Identifier
  - e.g. IC-HRZAGT1, IC-HRBUDB1, SC-HUBUDB1
  - Requires update to .gitlab-ci.yml
  - Affects docker images (many/most tied to Budapest)

## Runners - Multi-stage CI Jobs

- Shared runners have problems
  - Runner outside env (SGs required to allow SSH, etc in)
  - Security of secrets (different jobs run as the same user)
  - (arguably) harder to debug than using an accessible runner
- Our recommendations
  - Setup Multi-stage pipelines (shared + private)
  - Incorporate dedicated/private runners for in-tenant config/deploy
  - Easier for branch-to-env routing of CI jobs
  - We provide an ansible role and examples, documentation
  - `ansible-roles/ansible-gitlab-runner/`

+++

### Impediments - PNB

- Testing/adoption impeded by lack of OAM/PNB
  - IPSec tunnels, poor bandwidth/latency to gitlab, etc
  - Problems with runner registration, preparation, git cloning
  - Need to ensure all DC rollouts are on PNB!!
- Possible improvement with bootstrapping
  - Runner needed to run runner deployment
  - We have a non-optimal hack using the itot-jumphost
  - Could deploy a runner as part of DC rollout (??)

+++

## Problems - Env. Vars.

- SCv1 forces use of HTTP_PROXY
  - Vars "baked-in" to Dockerfiles, .gitlab-ci.yml
  - Currently not possible to port these CI jobs
  - Env config can (should) be managed by runners (plan to fix)
- Untagged jobs? (Backup for runners in SC Budapest)

---

## Beryllium CI/CD

- Better IaC
- No push to `master` branch
- MR approvers to do code review
- Deployment with Foundation and Juju
- Common bundle for all the deployments
- Common pipeline for all the deployments
- Differences between environments applied with `juju overlay` option

+++

## CI/CD Improvements

- Improved automation/integration with NetBox
- Automatically import information from NB into `.yaml` files

+++

## CI/CD Improvements - Config Mgmt

- Common bundle for all deployments
- Bundle configuration merged at runtime
- Follows DRY ("Don't Repeat Yourself") principles
- Avoids having to make changes to multiple repos

+++

## Foundation

The Good:

- Automates deployment from MAAS
- Simplified yaml config files
- Faster deployment

The bad:
- Not idempotent, therefore not ready for CI/CD

The ugly:
- Not opensource

+++

## Branching

![Be-Branch-Env-Mapping](img/branch-env-mapping.png)

---

### Artifactory

- The universal binary repository manager
  - Complements gitlab (binaries vs source code)
  - Internal + External binaries (artefacts)
- Optimized pipelines
  - Code + binary reuse
  - Consistent deployments
  - QA
- Validated pipelines
  - DevSecOps

+++

### Binary distribution + access

![Repo List](img/jf-rt-repo-types.png)

- Access vendor distribution repos/channels
  - Access of 3rd parties/upstream binaries
  - Point of control for binaries entering the system

+++

### Repository for custom artefacts

- Repository infrastructure for VNF artefacts
  - VM images, Software Packages, etc
  - CSAR Files
  - Test Artefacts
- Versioned artefacts
  - Metadata PropertySets (e.g. Datacenter location, VNF, En)
  - Rich query language
- Publish once - deploy anywhere
  - Aim to provide access across datacenters

+++

#### Artefacts for Application lifecycle

![Package Stack](img/pkg-stack.png)

+++

#### Artefacts for Application lifecycle

- Deployment Packages, versions and dependencies fixed
  - Can be inflexible
- What if we are forced
  - To take on upstream improvements/features
  - Security hotfixes, bugfixes, etc |
  - Patches, Tweaks |

+++

### Trusted container repository

- Support for docker repositories
  - Will eventually replace the gitlab registry
- Opens possibility for image inspection (JFrog XRay/Enterprise+)
- Support for Kubernetes Helm Charts (Beta!)
- Odd requirement on DNS - FQDN per repo

+++

#### Build infrastructure

![Package Build Pipeline](img/pkg-ci-pipeline.png)

+++

#### Artifact delivery infrastrcture

![Artifact Delivery Infrastructure](img/jf-artifact-delivery-infrastructure.png)

+++

### Roadmap

- Q1-Q2
  - Expose out-of-box functionality
  - App. Orch. Use cases
  - Beta announced in march
- Q3
  - Continued beta
  - Expose more use-cases/integrations
  - IC local artefact access
  - LDAP/IDP - Authentication + Authorization
  - Build Integration

### Roadmap - other

- DevSecOps
  - XRay
- Delegated administration
  - Self-service
- Improved HA + Repo Replication
  - DNS LB
- Gitlab Bridge - VCS (ansible, terraform, etc)

---

### Thank You!
