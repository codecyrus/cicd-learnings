# <span style="font-family:Helvetica Neue; font-weight:bold">
<span style="color:#e49436">Infrastructure</span> as Code</span>

* CICD Learnings
* Runner Topology
* Artifactory 
* Problems, Mitigations

---

## Runners Topology

What?

+++

# Runner topology

- Shared Runners Beta in Zagreb
- Use cases
- Tenant Bootstrap
- Non-Critical

+++

# Future

- Open up once PNB permits Zagreb <-> Budapest
- Untagged jobs?
- Ready to deploy runners in other SCv2 sites

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

# Repository for VNF artefacts

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

