---
title: "Jenkins Out, GitHub Actions In: How We Made the Leap"
subtitle: "From a Fragile Physical Box to Cloud-Native, Consistent CI/CD"
conference: "PyCon Finland 2025"
author: "Benoît Suttor & Rémi Dubois - iMio"
marp: true
paginate: true
footer: "PloneConf & PyCon Finland 2025 • iMio • Jenkins Out → GitHub Actions In"
theme: imio
---
<!--
_footer: ""
_paginate: skip
_class: center
-->
![bg](assets/fond8.png)

<div>

# Jenkins Out, GitHub Actions In
### How We Made the Leap

(Plone-focused CI/CD modernization)
Lots of Plone products, one aging Jenkins box...
Time to evolve.

</div>

<div class="logo-row">
  <img class="pycon" src="assets/pyconfinland.png" alt="PyCon Finland">
  <img src="assets/ploneconf2025.png" alt="PloneConf 2025">
  <img src="assets/imio_logo.svg" alt="iMio">
</div>



---

## Who ?

![bg](assets/fond10.png)

### Benoît

<div class="img-text-row-bio">
  <img src="assets/benoit.jpg" alt="Benoît">
  <div class="text">
    DevOps Engineer at iMio · 15 years in Plone & open source<br>
    Automation, Docker, Kubernetes, IaC<br>
    Plone contributor, Plone foundation member
    <div class="github-row">
      <img src="assets/github.png" alt="GitHub" />
    <span class="username">bsuttor</span>
</div>
  </div>
</div>



### Rémi

<div class="img-text-row-bio">
  <img src="assets/remi.jpg" alt="Rémi">
  <div class="text">
    16 years in municipal IT · SmartWeb @ iMio since 2022 <br>DevOps since 2024
    <br>Open-source & learning mindset
    <div class="github-row">
    <img src="assets/github.png" alt="GitHub" />
    <span class="username">remdub</span>
</div>
  </div>
</div>

<!-- Benoît & Rémi-->

---

![bg](assets/fond5.png)
<div class="img-text-row-imio">
  <img src="assets/imio-garcon.png" alt="iMio garçon" />
  <div class="text">
    <h2>What is iMio?</h2>
    <ul>
      <li>Public company in Belgium</li>
      <li>Provides IT services to ~400 local authorities</li>
      <ul>
        <li>Municipalities, Public Centre for Social Welfare, provinces, police zones, rescue zones</li>
      </ul>
      <li>11 different applications → 1200+ instances</li>
      <li>Python is in our ADN</li>
      <ul>
        <li>Plone, Odoo and Django projects</li>
      </ul>
      <li>Our missions</li>
      <ul>
        <li>Mutualise IT solutions</li>
        <li>support digitalisation</li>
      </ul>
    </ul>
  </div>
</div>


<!-- Rémi -->
---
![bg](assets/fond8.png)
## Agenda (25 min)

<div class="img-text-row-imio">
  <div class="text">
    <ol>
      <li>Context & Legacy Pain (3')</li>
      <li>Why Migrate? (4')</li>
      <li>Strategy & Process (5')</li>
      <li>Technical Architecture (5')</li>
      <li>Demo: Deployment Flow (4')</li>
      <li>Tips, Fun Fact & Failures (2')</li>
      <li>Future / WIP (2')</li>
    </ol>
  </div>
  <img src="assets/imio-calendar.png" alt="iMio calendar" />
</div>

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
![bg](assets/fond2.png)

<div class="img-text-row-imio">
  <img class="wh45" src="assets/imio-server.png" alt="iMio server" />
  <div class="text">
    <h2>The Legacy Setup</h2>
    <ul>
      <li>Single physical server (Ubuntu 14.x)</li>
      <li>Jenkins + a lot of plugins</li>
      <li>Groovy pipelines of... varying quality</li>
      <li>Hard to upgrade (plugin dependency hell)</li>
      <li>Credential sprawl</li>
      <li>Snowflake state (deployed with iac, but not maintained anymore)</li>
    </ul>
    <h3>Risk ↑ / Confidence ↓ / Bus factor = 1.5</h3>
  </div>
</div>

<!-- Benoît -->

---
<!--
_class: center
_footer: ""
-->
![bg](assets/fond5.png)
<img src="assets/cedric-funfacts.jpg" alt="Fun facts" class="img-fun-facts" />

<!-- Rémi -->
---
![bg](assets/fond5.png)
## Why Migrate? (High-Level)

<div class="img-text-row-imio">
  <div class="text">
    <ul>
      <li>Jenkins server was on its last legs</li>
      <li>Consolidate around where code lives (GitHub)</li>
      <li>Align with Plone community practices</li>
      <li>Remove plugin fragility</li>
      <li>Horizontal scale via Kubernetes</li>
      <li>Isolation per job</li>
    </ul>
  </div>
  <img src="assets/imio-interrogation.png" alt="iMio interrogation"/>
</div>

<!-- Rémi -->
---
![bg](assets/fond7.png)

<div class="img-text-row-imio">
  <img src="assets/imio-exclamation.png" alt="iMio interrogation"/>
  <div class="text">
    <h2>Why Not GitLab CI?</h2>
    <ul>
    <li>We already had GitLab internally BUT</li>
    <li>All Plone products already on GitHub</li>
    <li>Would require migration, retraining, and changes on dev local setups (& minds 🤡)</li>
    <li>Marketplace ecosystem (actions)</li>
    <li>ARC (actions-runner-controller) maturity</li>
    <li>Decision principle: minimise friction + follow upstream culture.</li>
    </ul>
  </div>
</div>


<!-- Rémi -->

---
![bg](assets/fond8.png)
## Timeline

<img src="assets/MigrationPhases.png" alt="Migration phases" class="img-migration-phases" />
<!-- Benoît
https://www.mermaidchart.com/app/projects/ad5d26cc-69a9-4a3b-b625-7a64ef6c03e8/diagrams/7eb0a929-db20-4674-9341-dfd996eb8eaa/version/v0.1/edit
<!--
timeline
    title Migration Phases
    2021-02: Some repos using GHA tests
    2024-06: Inventory Jenkins pipelines / classify (keep / refactor / drop)
    2024-07: Build base runner Docker image + ARC PoC
           : Create central 'gha' repo (composite actions)
           : Progressive cutover (deploy stages → GHA)
    2024-08: Dual-run (Jenkins + GHA) for critical products
    2024-09: Full production deployments via GHA
           : Jenkins decommission
    2025-06: Shared workflows
-->


---
![bg](assets/fond10.png)
## Talking to teams

<div class="img-text-row-imio">
  <div class="text">
    <h4>Questions we asked :</h4>
    <ul>
      <li>Does the actual workflow still suits your needs?</li>
      <ul>
            <li>What triggers the CI/CD ?</li>
      </ul>
      <li>How would you improve it?</li>
      <li>Should we keep explicit environments: staging / prod?</li>
      <li>Which metrics do you need?</li>
    </ul>
  </div>
  <img class="wh40" src="assets/imio-meeting.png" alt="iMio meeting"/>
</div>

<!-- Benoît
TODO : vérifier formulations phrases
-->


---
![bg](assets/fond4.png)
## The 'gha' Repository
![bg](assets/github-gha.png)


Reusable composite actions (examples):
- Build and push a docker image
- Run plone tests
- Build deb package
- Release Helm Chat
- Notify on mattermost


Link: https://github.com/IMIO/gha

### Encapsulate complexity
### → Keep workflows thin

<!-- Rémi
keep workflows thin and easy to understand
-->

---
![bg](assets/fond8.png)

<div class="img-text-row-imio">
  <img class="wh50"src="assets/argocd.png" alt="argocd"/>
  <div class="text">
    <h2>Runner Strategy</h2>
    <ul>
    <li>Self-hosted via ARC</li>
    <ul>
      <li>Auto-scaling ephemeral runners (security + cleanliness)</li>
      <li>Resource quotas per namespace</li>
      <li>Fast spin-up (prebaked image)</li>
      <li>Same network (reach internal services / servers)</li>
    </ul>
    </ul>
  </div>
</div>

https://github.com/actions/actions-runner-controller

<!-- Rémi -->
---
![bg](assets/fond2.png)
## Runner Docker Image


<div class="img-text-row-imio">
  <div class="text">
    <h4>Includes</h4>
    <a href="https://github.com/IMIO/docker-bases/tree/master/actions-runner" class="little-link">https://github.com/IMIO/docker-bases/tree/master/actions-runner</a>
    <ul>
      <li>Python (multiple versions)</li>
      <li>Plone buildout deps (C libs: libxml2, libjpeg, zlib...)</li>
      <li>caching dirs structured (/cache/buildout, pip)</li>
      <li>Versioned & scanned (Trivy)</li>
    </ul>
  </div>
  <img src="assets/imio-container.png" alt="imio-container"/>
</div>



<!-- Benoît -->
---
![bg](assets/fond3.png)
<div class="img-release-process">
  <div>
    <h3>Branch / Deploy Flow</h3>
  </div>
  <div>
    <img src="assets/ReleasesProcess.png" alt="release-process">
  </div>
</div>
<!-- Benoît
Démo ?
Consistent rules → reduces cognitive load.
 -->

---
![bg](assets/fond3.png)
## DEMO

<div class="img-text-row-imio">
  <div class="text">
    <video width="640" height="480" controls>
      <source src="assets/vidtest.mp4" type="video/mp4">
      Your browser does not support the video tag.
    </video>
  </div>
  <img src="assets/imio-demo.png" alt="imio-demo"/>
</div>

<!-- Benoît & Rémi
Same as Jenkins:

- Staging: every merge to `main` auto-deploys to staging instances (copy of some prod instances)
- Prod: only annotated tag on `main` + schedule (3 AM next day)
- Rollback: git tag revert + redeploy (immutable images retained) (! Database)

-->


---
![bg](assets/fond5.png)
## Rundeck Jobs (Legacy Tie-In)

Some long-running operations still in Rundeck:
- Docker images pull
- Instances reboot
- Upgrade-steps

GHA triggers via Rest API

<!-- Rémi
  faire le lien avec la démo
-->

---
![bg](assets/fond4.png)
## Observability

<div class="img-text-row-imio">
  <div class="text">
    <h4>Includes</h4>
    <ul>
    <li>Lightweight Mattermost notification: short status + link (no noisy full logs).</li>
    <li>Actions logs (raw) in GitHub web UI</li>
    <li>Plone logs : rundeck</li>
    <li>Container-level metrics (Prometheus + Grafana)</li>
    </ul>
  </div>
  <img src="assets/imio-explorateur.png" alt="imio-container"/>
</div>


<!-- Rémi -->

---
<!--
_footer: ""
-->
![bg](assets/fond7.png)

<div class="img-text-row-imio">
  <img src="assets/imio-server-onfire.png" alt="imio-onfire"/>
  <div class="text">
    <h2>Fun Fact (Timing Was Perfect)</h2>
    <ul>
    <li>Physical Jenkins server died (disk failure)</li>
    <li><b>BEFORE</b> migration completed.</li>
    <li>No data salvage possible. (but not needed)</li>
    <li>Migration ROI validated instantly 🙂</li>
    </ul>
  </div>
</div>


<!-- Benoît -->

---
![bg](assets/fond8.png)
## Current WIP / Future

- Shared reusable workflows (org-level)
- Kubernetes-native Plone (full containerization & scaling)
- Align with [plone/meta] best practices
- Dashboard replacing noisy Mattermost spam

<!-- Benoît & Rémi-->

---
<!--
_class: center
_footer: ""
-->
![bg](assets/fond6.png)


<div class="img-text-row-imio">
  <img class="wh40" src="assets/imio-ripjenkins.png" alt="imio-ripjenkins"/>
  <div class="text">
    <span class="big-text">
      Farewell Jenkins, you won’t be missed.
    </span>
  </div>
</div>



</div>
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