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
  top: 48px;
  left: 50%;
  transform: translateX(-50%);
  display: block;
  width: 90%;
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
</style>


# Jenkins Out, GitHub Actions In
### How We Made the Leap
(Plone-focused CI/CD modernization)
![bg](assets/fond.png)

Lots of Plone products, one aging Jenkins box...
Time to evolve.

<div class="logo-row">
  <img src="assets/pyconfinland.png" alt="PyCon Finland">
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
    DevOps Engineer at iMio · 10+ years in Plone & open source<br>
    Automation, Docker, Kubernetes, IaC<br>
    Active Plone contributor
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

---
![bg](assets/fond3.png)
### Throwback: PloneConf 2022

![bg](assets/ploneconf2022.png)

[How we created, deployed and updated over 200 websites at iMio with no downtime.](https://www.youtube.com/watch?v=z-5xx-vKYpc)

Key difference today:
- 2015: GitHub Actions did NOT exist
- Ecosystem maturity (2022 → 2025): composite actions, Action Runner Controler

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

---
![bg](assets/fond5.png)
<img src="assets/cedric-funfacts.jpg" alt="Fun facts" class="img-fun-facts" />


---
![bg](assets/fond6.png)
## Why Migrate? (High-Level)

- Consolidate around where code lives (GitHub)
- Align with Plone community practices
- Remove plugin fragility
- Horizontal scale via Kubernetes
- Isolation per job


---
![bg](assets/fond7.png)
## Why Not GitLab CI?

We already had GitLab internally BUT:
- All Plone products already on GitHub
- Would require migration, retraining, and changes on dev local setups (& minds 🤡)
- Marketplace ecosystem (actions)
- ARC (actions-runner-controller) maturity

Decision principle: minimize friction + follow upstream culture.

---
![bg](assets/fond8.png)
## Migration Timeline

TODO : plus fancy

```mermaid
timeline
    title Migration Phases
    2021-02: Some repos using GHA tests
    2024-06: Inventory Jenkins pipelines / classify (keep / refactor / drop)
    2024-07: Build base runner Docker image + ARC PoC
    2024-07: Create central 'gha' repo (composite actions)
    2024-07: Progressive cutover (deploy stages → GHA)
    2024-08: Dual-run (Jenkins + GHA) for critical products
    2024-09: Full production deployments via GHA
    2024-09: Final failover (Jenkins disk crash - irreversible)
    2024-09: Jenkins decommission
    2025-06: Shared workflows
    2025-?: Plone in Kubernetes WIP
```

<!-- ---

## Inventory & Rationalization

We catalogued every Jenkins job:
- xx total discovered
- xx obsolete (removed)
- xx merged (duplicates)
- xx rewritten from scratch
- xx migrated mostly as-is -->

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

---
![bg](assets/fond2.png)
## Runner Docker Image

Includes:
- Python (multiple versions)
- Plone buildout deps (C libs: libxml2, libjpeg, zlib...)
- Node.js
- caching dirs structured (/cache/buildout, pip)

Versioned & scanned (Trivy) → “infrastructure you can diff.”

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

---
![bg](assets/fond4.png)
## Environment Policy

Same as Jenkins:

- Dev: every merge to `dev` auto-deploys to specific sandbox instances
- Staging: every merge to `main` auto-deploys to staging instances (copy of some prod instances)
- Prod: only annotated tag on `main` + schedule (3 AM next day)
- Rollback: git tag revert + redeploy (immutable images retained)

---
![bg](assets/fond5.png)
## Rundeck Jobs (Legacy Tie-In)

Some long-running operations still in Rundeck:
- Docker images pull
- Instances reboot
- Upgrade-steps

GHA triggers via API (signed request)

---
![bg](assets/fond6.png)
## Observability

- Lightweight Mattermost notification: short status + link (no noisy full logs).
- Actions logs (raw) in GitHub web UI
- Plone logs : rundeck & Kibana
- Container-level metrics (Prometheus + Grafana)


Dashboard WIP

---
![bg](assets/fond7.png)
## Fun Fact (Timing Was Perfect)

Physical Jenkins server died (disk failure)
BEFORE migration completed.
No data salvage possible. (but not needed)
Migration ROI validated instantly 🙂

---
![bg](assets/fond8.png)
## Current WIP / Future

- Shared reusable workflows (org-level)
- Kubernetes-native Plone (full containerization & scaling)
- Align with [plone/meta] best practices (watching upstream)
- Dashboard replacing noisy Mattermost spam

---
![bg](assets/fond9.png)
## Conclusion

Migration is an opportunity to simplify, not just port.

---
![bg](assets/fond10.png)
## Resources

TODO : compléter 

- Actions Runner Controller
    https://github.com/actions/actions-runner-controller
- IMIO GHA composite actions
    https://github.com/IMIO/gha
- zest.releaser
    https://pypi.org/project/zest.releaser/
- ArgoCD
    https://argo-cd.readthedocs.io/

---
![bg](assets/fond11.png)
## Thank You

Questions?



<!-- END -->