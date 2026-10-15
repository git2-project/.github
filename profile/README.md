# git2 ⚡

> **Your company's Git server, on your own machines.**  
> Open source · Self-hosted · One command to install · One bucket to back up and restore everything.

---

## 🎯 Three Promises
1. **One command to install:** `git2ctl install` brings up the entire stack from zero to running.
2. **One bucket for everything:** Encrypted nightly snapshots of the database, repos, `git2.yaml`, and secrets into any S3/GCS/R2/local bucket.
3. **Friendly for everyone:** **Dash** plane for developers (repos, MRs, pipelines) and **Admin** plane for IT (SSO/LDAP, runners, backups, audit logs).

---

## 🗺️ Lanes, Repositories & Owners

Projemiz 2 Plane ve 3 Engine olmak üzere **5 paralel kulvarda (lane)** geliştirilmektedir. Ayrıca servisler arası OpenAPI ve Protobuf sözleşmeleri `contracts` reposunda tutulur.

| Lane / Repo | Services & Components | Scope & Responsibilities | Owner |
| :--- | :--- | :--- | :--- |
| 🖥️ **`git2-dash`** *(Plane)* | `dash-web`, `dash-api` | Repos, code browsing, merge requests, code review, issues, search | `@owner-dash` |
| 🛡️ **`git2-admin`** *(Plane)* | `admin-web`, `admin-api`, `auth-svc` | Users, teams, SSO & LDAP, instance settings, runner management, audit log | `@owner-admin` |
| 🌿 **`git2-git`** *(Engine)* | `git-svc` | Repo storage on disk, push/clone (HTTPS/SSH), diff, merge, branch protection, Git LFS | `@owner-git` |
| 🚀 **`git2-ci`** *(Engine)* | `ci-svc`, `runner` | `.git2-ci.yml` spec, pipeline scheduling, job queue, container execution, logs, artifacts | `@owner-ci` |
| 📦 **`git2-platform`** *(Engine)* | `gateway`, `backup-svc`, `git2ctl` | TLS & routing, Docker Compose / Helm deploy, encrypted backup & restore, upgrades | `@owner-plat` |
| 📜 **`contracts`** *(Shared)* | `OpenAPI`, `gRPC proto` | Shared API & service contracts (Changes require PR approval from both affected owners) | `@everyone` |

---

## 🛠️ Tech Stack & Architecture

* **Services:** Go, communicating via **gRPC** (`OpenAPI` for public APIs)
* **Web UI:** React (compiled to static files, served via `gateway` — *no Node in prod*)
* **Git Backend:** `git` + `go-git`
* **State & Event Bus:** **PostgreSQL** (All data + transactional **PG Outbox** for events like `repo.pushed` and `pipeline.finished` — *no Redis, no Kafka/NATS*)
* **Object Storage:** `gocloud blob` supporting AWS S3, GCS, Azure Blob, Cloudflare R2, SeaweedFS, Garage, Ceph, or local disk (*no MinIO*)
* **Backup Crypto:** `age` encryption
* **Deployment & Telemetry:** Docker Compose (all-in-one) / Helm (multi-node), OpenTelemetry

---

## 📅 9-Week Roadmap & Milestones

| Milestone | Week | Target Demo | Key Deliverables |
| :--- | :---: | :--- | :--- |
| **M0 · Foundation** | **W1** | **Sign in** *(Dash & Admin, one login)* | Dash/Admin login shells, `auth-svc` tokens, empty repo API, `.git2-ci.yml` v0 spec, `docker compose up`, `contracts/` v0 |
| **M1 · First Push** | **W3** | **Push & Browse** *(`git push`, see code)* | Repo & code views, users/teams, HTTP push/clone, runner v0, `gateway` + `git2ctl` |
| **M2 · First Merge** | **W5** | **Review & Merge** *(MRs + SSO)* | MR & review UI, SSO/LDAP, diff & merge engine, job queue, backup v0 |
| **M3 · Green Pipeline** | **W7** | **Green Pipeline** *(Jobs in containers)* | Pipeline views, runners/settings UI, branch protection & LFS, logs & artifacts, bucket drivers |
| **M4 · Restore** | **W8** | **Backup & Restore** *(New server, same git2)* | UI polish, audit log, SSH support, CI caching, full instance restore |
| **DEMO DAY** | **W9** | **Live End-to-End Demo** | Full integration, bug bash, live demo. **Stretch Goal:** *git2 hosts git2!* |

---

## 🤖 Working Rhythm & Claude Rules
* **One `CLAUDE.md` per repo/service:** Defines scope, build/test commands, and conventions.
* **Strict Boundaries:** Everyone (and Claude) works inside their own lane. `contracts/` changes by PR only, approved by both affected owners.
* **Quality Gate:** Every PR ships with tests (against mocks) and requires **1 human review** before merge.
* **Sync Cadence:**
  * **Daily:** Short async update in chat.
  * **Every Friday:** Integration check (`main` runs end-to-end).
  * **Each Milestone:** Live demo to the team.
