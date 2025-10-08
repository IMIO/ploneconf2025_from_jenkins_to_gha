---
title: "Jenkins Out, GitHub Actions In: How We Made the Leap"
subtitle: "From a Fragile Physical Box to Cloud-Native, Consistent CI/CD"
conference: "PyCon Finland 2025"
author: "Benoît Suttor & Rémi Dubois - iMio"
marp: true
paginate: true
footer: "PloneConf & PyCon Finland 2025 • iMio • Jenkins Out → GitHub Actions In"

---
<style>
@import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@300..700&display=swap');
</style>
<style>
section {
 font-family: "Quicksand", sans-serif;
}
.img-text-row-bio {
  display: flex;
  align-items: flex-start;
  gap: 24px;
}
.img-text-row-bio img {
  width: 128px;
  height: 128px;
  border-radius: 16px;
  object-fit: cover;
}
.img-text-row-bio .text {
  flex: 1;
}
.github-row {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-top: 8px;
}
.github-row img {
  width: 32px;
  height: 32px;
  vertical-align: middle;
}
.github-row .username {
  font-size: 1.1em;
  font-weight: 500;
}
.img-text-row-imio {
  display: flex;
  align-items: flex-start;
  gap: 24px;
}
.img-text-row-imio img {
  width:35%;
  height:35%;
  border-radius: 16px;
}
.img-text-row-imio .text {
  flex: 1;
}
.img-fun-facts {
  position: absolute;
  top: 24px;
  left: 50%;
  transform: translateX(-50%);
  display: block;
  width: 65%;
  max-width: 90vw;
  height: auto;
  margin: 0 auto;
  border-radius: 18px;
  box-shadow: 0 4px 24px rgba(0,0,0,0.08);
}
.logo-row {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 40px;
  margin-top: 32px;
  margin-bottom: 24px;
}
.logo-row img {
  height: 128px;
  width: 128px;
  object-fit: contain;
  filter: drop-shadow(0 4px 16px rgba(0,0,0,0.10));
  background: white;
  border-radius: 12px;
  padding: 4px 6px;
  box-sizing: border-box;
}

.logo-row img.pycon {
    padding:24px;
}

.mw50 {
    width: 50%;
}
</style>


# Jenkins Out, GitHub Actions In
### How We Made the Leap
(Plone-focused CI/CD modernization)
![bg](assets/fond.png)

Lots of Plone products, one aging Jenkins box...
Time to evolve.

<div class="logo-row">
  <img class="pycon" src="assets/pyconfinland.png" alt="PyCon Finland">
  <img src="assets/ploneconf2025.png" alt="PloneConf 2025">
  <img src="assets/imio_logo.svg" alt="iMio">
</div>

<!-- _footer: "" -->

---

## Who ?

![bg](assets/fond7.png)

### Benoît

<div class="img-text-row-bio">
  <img src="assets/benoit.jpg" alt="Benoît">
  <div class="text">
    DevOps Engineer at iMio · 15 years in Plone & open source<br>
    Automation, Docker, Kubernetes, IaC<br>
    Plone contributor, Plone foundation member
  </div>
</div>
<div class="github-row">
    <img src="assets/github.png" alt="GitHub" />
    <span class="username">bsuttor</span>
</div>


### Rémi

<div class="img-text-row-bio">
  <img src="assets/remi.jpg" alt="Rémi">
  <div class="text">
    16 years in municipal IT · SmartWeb @ iMio since 2022 <br>DevOps since 2024
    <br>Open-source & learning mindset
  </div>
</div>
<div class="github-row">
    <img src="assets/github.png" alt="GitHub" />
    <span class="username">remdub</span>
</div>

<!-- Benoît & Rémi-->

---

![bg](assets/fond.png)
## iMio
<div class="img-text-row-imio">
  <img src="assets/imio-garcon.png" alt="iMio garçon">
  <div class="text">
    <ul>
    <li>Provides IT services to ~400 local authorities</li>
    <li>Municipalities, CPAS, provinces, police zones, rescue zones</li>
    <li>11 different applications → 1200+ instances</li>
    <li>Our mission: Mutualize IT solutions and support digitalization</li>
    </ul>
  </div>
</div>


<!-- Rémi -->
---
![bg](assets/fond4.png)
## Agenda (25 min)

1. Context & Legacy Pain (3')
2. Why Migrate? (4')
3. Strategy & Process (5')
4. Technical Architecture (5')
5. Demo: Deployment Flow (4')
6. Tips, Fun Fact & Failures (2')
7. Future / WIP (2')

Q&A spills into hallway 🙂

<!-- Benoît -->
---
![bg](assets/fond3.png)
### Throwback: PloneConf 2022

![bg](assets/ploneconf2022.png)

<div class="mw50">
<a href="https://www.youtube.com/watch?v=z-5xx-vKYpc">How we created, deployed and updated over 200 websites at iMio with no downtime.</a>


Key difference today:
- 2015: GitHub Actions did NOT exist
- Ecosystem maturity (2022 → 2025): composite actions, Action Runner Controler
</div>

<!-- Benoît -->

---
![bg](assets/fond4.png)

## The Legacy Setup (Reality Check)

- Single physical server (Ubuntu 14.x)
- Jenkins + a lot of plugins
- Groovy pipelines of... varying quality
- Hard to upgrade (plugin dependency hell)
- Credential sprawl
- Snowflake state (deployed with iac, but not maintained anymore)

Risk ↑ / Confidence ↓ / Bus factor = 1.5
<!-- Benoît -->
---
![bg](assets/fond5.png)
<img src="assets/cedric-funfacts.jpg" alt="Fun facts" class="img-fun-facts" />

<!-- Rémi -->
---
![bg](assets/fond6.png)
## Why Migrate? (High-Level)

- Consolidate around where code lives (GitHub)
- Align with Plone community practices
- Remove plugin fragility
- Horizontal scale via Kubernetes
- Isolation per job

<!-- Rémi -->
---
![bg](assets/fond7.png)
## Why Not GitLab CI?

We already had GitLab internally BUT:
- All Plone products already on GitHub
- Would require migration, retraining, and changes on dev local setups (& minds 🤡)
- Marketplace ecosystem (actions)
- ARC (actions-runner-controller) maturity

Decision principle: minimize friction + follow upstream culture.

<!-- Rémi -->

---
![bg](assets/fond8.png)
## Timeline

<img src="assets/MigrationPhases.png" alt="Migration phases" class="img-migration-phases" />
<!-- Benoît
https://www.mermaidchart.com/app/projects/ad5d26cc-69a9-4a3b-b625-7a64ef6c03e8/diagrams/7eb0a929-db20-4674-9341-dfd996eb8eaa/version/v0.1/edit

timeline
    title Migration Phases
    2021-02: Some repos using GHA tests
    2024-06: Inventory Jenkins pipelines / classify (keep / refactor / drop)
    2024-07: Build base runner Docker image + ARC PoC
           : Create central 'gha' repo (composite actions)
           : Progressive cutover (deploy stages → GHA)
    2024-08: Dual-run (Jenkins + GHA) for critical products
    2024-09: Full production deployments via GHA
           : Final failover
           : Jenkins decommission
    2025-06: Shared workflows
-->

---
![bg](assets/fond9.png)
## Talking to Teams

Questions we asked:

1. Does the actual workflow still suits your needs ?
2. How would you improve it ?


Qu'est-ce qui trigger quoi ? Est-ce toujours utile ?
Comment sont lancés les tests ? (buildout & co)
Quand et quels tags pour construire les images ?
etc
TODO


---
![bg](assets/fond10.png)
## Design Goals

- One mental model per repo (tests → build → deploy)
- Explicit environments: dev / staging / prod
- Idempotent deploy steps
- Reusable composite actions (gha repo)
- Deterministic runner image (pin dependencies)
- Observability

---
![bg](assets/fond11.png)
## The 'gha' Repository (Building Blocks)

TODO

Reusable composite actions (examples):
- setup-plone-environment
- run-plone-tests
- build-plone-image
- deploy-helm-release
- notify-mattermost (rate-limited)
- tag-and-publish (integrates zest.releaser conventions)

Link: https://github.com/IMIO/gha

Encapsulate complexity → keep workflows thin.

<!-- Rémi -->

---
![bg](assets/fond.png)
## Runner Strategy

Self-hosted via ARC:
https://github.com/actions/actions-runner-controller

Why:
- Auto-scaling ephemeral runners (security + cleanliness)
- Resource quotas per namespace
- Fast spin-up (prebaked image)
- Same network (reach internal services / servers)
<!-- Rémi -->
---
![bg](assets/fond2.png)
## Runner Docker Image

Includes:
- Python (multiple versions)
- Plone buildout deps (C libs: libxml2, libjpeg, zlib...)
- Node.js
- caching dirs structured (/cache/buildout, pip)

Versioned & scanned (Trivy) → “infrastructure you can diff.”
<!-- Benoît -->
---
![bg](assets/fond3.png)
## Branch / Deploy Flow (Visual)

### TODO (demo ?)
```mermaid
flowchart LR
    A[Dev Commit (feature/*)] -->|PR| B[Pull Request]
    B -->|Merge| C[dev branch]
    C -->|CI + Auto Deploy| D[Dev Sandbox]
    C -->|PR or Fast-Forward| E[main]
    E -->|CI + Immediate Deploy| F[Staging]
    E -->|Tag (zest.releaser)| G[(Tag vX.Y.Z)]
    G -->|Scheduled Deploy 03:00| H[Production]
    H -->|Post Deploy Checks| I[Notify + Metrics]
```

Consistent rules → reduces cognitive load.

<!-- Benoît -->

---
![bg](assets/fond4.png)
## Environment Policy

Same as Jenkins:

- Staging: every merge to `main` auto-deploys to staging instances (copy of some prod instances)
- Prod: only annotated tag on `main` + schedule (3 AM next day)
- Rollback: git tag revert + redeploy (immutable images retained) (! Database)

<!-- Benoît -->

---
![bg](assets/fond5.png)
## Rundeck Jobs (Legacy Tie-In)

Some long-running operations still in Rundeck:
- Docker images pull
- Instances reboot
- Upgrade-steps

GHA triggers via API (signed request)

<!-- Rémi -->

---
![bg](assets/fond6.png)
## Observability

- Lightweight Mattermost notification: short status + link (no noisy full logs).
- Actions logs (raw) in GitHub web UI
- Plone logs : rundeck & Kibana
- Container-level metrics (Prometheus + Grafana)

Dashboard WIP

<!-- Rémi -->

---
![bg](assets/fond7.png)
## Fun Fact (Timing Was Perfect)

Physical Jenkins server died (disk failure)
BEFORE migration completed.
No data salvage possible. (but not needed)
Migration ROI validated instantly 🙂

<!-- Benoît -->

---
![bg](assets/fond8.png)
## Current WIP / Future

- Shared reusable workflows (org-level)
- Kubernetes-native Plone (full containerization & scaling)
- Align with [plone/meta] best practices (watching upstream)
- Dashboard replacing noisy Mattermost spam

<!-- Benoît & Rémi-->

---
![bg](assets/fond9.png)
## Conclusion

Migration is an opportunity to simplify, not just port.

<!-- Benoît & Rémi -->


---
![bg](assets/fond10.png)
## Thank you


- Actions Runner Controller: https://github.com/actions/actions-runner-controller
- IMIO GHA composite actions: https://github.com/IMIO/gha
- zest.releaser: https://pypi.org/project/zest.releaser/
- ArgoCD: https://argo-cd.readthedocs.io/
- Our slides: https://imio.github.io/ploneconf2025_from_jenkins_to_gha/

## Questions ?



<!-- END -->