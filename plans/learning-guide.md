# The Realistic Stack — Detailed Learning Guide

**Stack:** PostgreSQL / SQL tuning · Python (or Bash) · Git · one cloud (AWS used throughout) · dbt · basic Docker / Kubernetes.
**Prerequisite layer:** Linux command line.
**Goal:** reach a *job-ready* data-platform skill set, biased toward fewer-interruption, research-leaning roles (analytics engineer, data engineer, bioinformatics / clinical data).

---

## 0. How to use this document

- It is organized as **layers**. Each layer has: *why it matters → what to learn in detail → hands-on drills → milestone project → interview questions → resources*.
- Study in the order in §8. Do not skip Linux/Git/SQL foundations — everything else assumes them.
- For every layer, build something. Skills without a repo don't count in interviews.
- Provide time to **repetition**: SQL especially rewards daily practice.

---

## 1. The stack at a glance

| Layer | Tool | Why it's there | Rough effort |
|---|---|---|---|
| Foundation | Linux CLI + Bash | Every server, cron job, log, and pipeline runs here | 4 weeks |
| Foundation | Git | Version everything; collaborate | ongoing |
| Core | SQL on PostgreSQL | The universal data language | 6-8 weeks |
| Core | Python | Automate, transform, orchestrate | 4 weeks |
| Platform | One cloud (AWS) | Where data lives and runs | 4 weeks |
| Transform | dbt | Turn SQL into tested, versioned models | 3-4 weeks |
| Runtime | Docker + K8s basics | Package and run services | 3 weeks |

---

## 2. Foundations

### 2a. Linux command line (prerequisite)

**Why:** SQL and Python run *on* Linux. Logs, cron, services, cloud servers, and HPC clusters are all Linux. Support work has already given you exposure; make it deliberate.

**What to learn in detail**
- **Filesystem & navigation:** `pwd`, `ls -la`, `cd`, `tree`, `find` (by name, size, mtime, `-exec`), `locate`.
- **Permissions & ownership:** `rwx` model, octal vs symbolic, `chmod`, `chown`, `umask`, setuid/setgid sticky bits, symlinks vs hard links.
- **Text processing (the big one):** `cat`, `less`, `head`, `tail -f`, `grep` (regex, `-r`, `-v`, `-o`, `-E`), `cut`, `sort`, `uniq -c`, `tr`, `wc`, `sed` (substitute, delete, ranges), `awk` (fields `$1`, patterns, `BEGIN/END`, aggregation), `xargs`, pipes `|`, redirection `> >> 2> &>`.
- **Processes & services:** `ps aux`, `top`/`htop`, `kill`/`kill -9`, `nice`, `nohup`, `systemctl` (start/stop/enable/status), `journalctl -u`, `cron`/`crontab -e`.
- **Users & groups:** `whoami`, `id`, `sudo`, `/etc/passwd`, `/etc/group`, `useradd`.
- **Networking:** `curl` (`-i`, `-X`, `-d`, `-H`), `wget`, `ss -tulpn`, `ping`, `dig`/`nslookup`, `nc`, ports, `hosts` file.
- **SSH & transfer:** key generation (`ssh-keygen`), `ssh`, `ssh config`, `scp`, `rsync -avz`, port forwarding.
- **Environment:** `PATH`, env vars, `export`, `.bashrc`/`.zshrc`, aliases.
- **Packages:** `apt`, `yum`/`dnf`, `dpkg`, `brew` (mac).
- **Disks & archives:** `df -h`, `du -sh`, `mount`, `tar` (`czf`/`xzf`), `gzip`, `zip`.
- **Logs:** structure of `/var/log`, `journalctl`, log rotation (`logrotate`).

**Hands-on drills**
1. Bandit (OverTheWire) levels 0→20.
2. Parse an Apache/Nginx log: top 10 IPs, top URLs, 5xx counts — using only shell tools.
3. Write a cron job that archives old files nightly and logs the result.

**Milestone project:** Provision an Ubuntu VM (AWS free tier or WSL2), harden SSH to keys-only, run a service under `systemd`, and script a log-rotation job.

**Interview questions**
- How do you find files modified in the last 24h? (`find . -mtime -1`)
- A disk is full — how do you find what's using space?
- Explain `chmod 754`. What does each digit mean?
- How do you tail a log and filter only errors live? (`tail -f x.log | grep -i error`)

**Resources:** *The Linux Command Line* (William Shotts); OverTheWire Bandit; `man` pages; explainshell.com.

---

### 2b. Bash scripting

**Why:** Glue for automation (backups, ETL kick-offs, cron). You'll use it alongside Python.

**What to learn in detail**
- Shebang `#!/usr/bin/env bash`; `chmod +x`.
- Variables, quoting (`"$var"`), `readonly`, command substitution `$(...)`.
- Conditionals `if/elif/else`, `test`/`[ ]`/`[[ ]]`, string & numeric comparisons, file tests (`-f`, `-d`, `-e`).
- Loops `for`, `while`, `until`; `case`.
- Functions, arguments (`$1`, `$@`, `$#`), return codes.
- Safety: `set -euo pipefail`; trap on `ERR`/`EXIT`.
- Arrays and associative arrays.
- Text pipelines combined with `awk`/`sed`.
- Debugging with `set -x`; `shellcheck`.

**Drill:** script that takes a directory, compresses files older than N days, uploads to S3, and logs results with timestamps and exit codes.

**Resources:** *Bash Pitfalls*; shellcheck.net; Google Shell Style Guide.

---

### 2c. Git

**Why:** Non-negotiable. Everything (code, SQL models, IaC) is versioned.

**What to learn in detail**
- Concepts: working tree, staging, commit, branch, HEAD, remote.
- Daily: `status`, `add`, `commit`, `diff`, `log --oneline --graph`, `restore`.
- Branching: `branch`, `checkout -b`/`switch -c`, `merge`, fast-forward vs merge commit.
- Remotes: `clone`, `remote`, `fetch`, `pull`, `push`, `-u`.
- Rewriting safely: `rebase`, interactive rebase (`squash`, `reword`), `cherry-pick`, `revert`, `reset --soft/--mixed/--hard`.
- Conflict resolution.
- `.gitignore`, `.gitattributes`, tags/releases, `stash`.
- Pull requests / code review workflow.
- Commit hygiene: small, focused, imperative messages.

**Milestone:** Every project below lives on GitHub with a real README (what/why/how to run) and a clear history.

**Resources:** *Pro Git* (free); Learn Git Branching (interactive); Conventional Commits.

---

## 3. SQL & PostgreSQL (the core)

This is the single highest-ROI layer. Interviewers test it directly.

### 3a. SQL fundamentals

**What to learn in detail**
- **Querying:** `SELECT`, `WHERE`, `ORDER BY`, `LIMIT/OFFSET`, `DISTINCT`.
- **Filtering:** `AND/OR/NOT`, `IN`, `BETWEEN`, `LIKE`/`ILIKE`, `IS NULL` (and why `= NULL` fails).
- **Joins:** `INNER`, `LEFT`, `RIGHT`, `FULL`, `CROSS`, self-joins; anti-joins via `LEFT ... IS NULL`; join keys and cardinality.
- **Aggregation:** `GROUP BY`, `HAVING`, `COUNT/SUM/AVG/MIN/MAX`, `COUNT(*)` vs `COUNT(col)`, `COUNT(DISTINCT ...)`, `FILTER`.
- **Subqueries:** scalar, correlated, `EXISTS`/`NOT EXISTS`, `IN` vs `EXISTS`.
- **CTEs:** `WITH`, multiple CTEs, readability, **recursive CTEs** (org charts, graphs).
- **Window functions:** `ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE`; `LAG`/`LEAD`; running totals and moving averages; `PARTITION BY` + `ORDER BY` + frame (`ROWS BETWEEN`).
- **Set operations:** `UNION`, `UNION ALL`, `INTERSECT`, `EXCEPT`.
- **Conditional logic:** `CASE`, `COALESCE`, `NULLIF`, `GREATEST/LEAST`.
- **Dates:** `date_trunc`, `date_part`/`extract`, `interval`, `age`, `to_char`, time zones.

**Hands-on drills (do these weekly)**
- LeetCode SQL / StrataScratch / DataLemur: 50+ problems.
- Classic shapes: second-highest salary, Nth highest, consecutive logins, running total, dedupe keeping latest, gaps-and-islands, YoY growth, cohort retention.

---

### 3b. PostgreSQL specifics

**What to learn in detail**
- **Data types:** `integer/bigint`, `numeric` (money, never float), `text`/`varchar`, `boolean`, `date/timestamp/timestamptz`, `uuid`, `json` vs `jsonb`, arrays, enums, ranges, `inet`.
- **Objects:** databases, schemas, tables, views, materialized views, sequences/identity, domains.
- **psql CLI:** `\l`, `\c`, `\d`, `\dt`, `\d+`, `\x` (expanded), `\copy`, `\e`, `\timing`, `\i`.
- **Constraints:** `PRIMARY KEY`, `UNIQUE`, `NOT NULL`, `CHECK`, `FOREIGN KEY` + `ON DELETE/UPDATE` actions.
- **Indexes:** B-tree (default), hash, GIN/GiST (full-text, arrays, jsonb), **partial**, **composite** (leftmost-prefix rule), **covering** (`INCLUDE`), unique.
- **EXPLAIN:** `EXPLAIN`, `EXPLAIN (ANALYZE, BUFFERS)`; scan types (Seq Scan, Index Scan, Index Only Scan, Bitmap); join algorithms (Nested Loop, Hash Join, Merge Join); cost/rows/actual; why estimates go wrong.
- **Transactions & concurrency:** `BEGIN/COMMIT/ROLLBACK`, `SAVEPOINT`, isolation levels (Read Committed, Repeatable Read, Serializable), MVCC, locking (`SELECT ... FOR UPDATE`), deadlocks.
- **Maintenance:** `VACUUM`, `VACUUM FULL`, `ANALYZE`, autovacuum, table bloat, `REINDEX`.
- **Statistics & planner:** `pg_stats`, `default_statistics_target`.
- **Backup/restore:** `pg_dump`/`pg_restore`, custom format, logical vs physical, WAL, PITR, `pg_basebackup`.
- **Replication:** streaming vs logical, read replicas, failover concepts.
- **Connection management:** `pgbouncer`, max_connections, prepared statements.
- **Security:** roles, `GRANT/REVOKE`, row-level security, `pg_hba.conf`.
- **Extensions:** `pg_stat_statements`, `pgcrypto`, `uuid-ossp`, `postgis` (awareness).
- **Partitioning:** declarative range/list/hash partitioning.

**Hands-on drills**
1. Load a 1–5M row CSV with `\copy`, add indexes, compare `EXPLAIN ANALYZE` before/after.
2. Reproduce a deadlock on purpose; then fix it.
3. Set up a read replica (Docker) and route reads to it.

---

### 3c. Performance tuning

**What to learn in detail**
- Read a plan and spot the bottleneck: Seq Scan on a big table, bad row estimates, nested loops over many rows, sorts spilling to disk.
- Index strategy: which column(s), order, partial vs composite, when an index *won't* be used (low selectivity, type mismatch, function on column → need expression index).
- Keep statistics fresh (`ANALYZE`); understand selectivity and cardinality.
- Rewrite queries: avoid `SELECT *`, push predicates, replace correlated subqueries, avoid functions on indexed columns in `WHERE`, break big joins.
- Find slow queries: `pg_stat_statements`, `log_min_duration_statement`, `auto_explain`.
- Lock contention & deadlocks: `pg_locks`, `pg_stat_activity`, `pg_blocking_pids`.
- Materialized views and caching for expensive aggregates; refresh strategies.
- Partitioning for huge time-series tables.

**Milestone project:** Given a slow analytic query on a large table, reduce its runtime by ≥80% and document *why* (plan before/after, indexes added, query rewritten).

**Interview questions**
- Difference between `WHERE` and `HAVING`?
- Explain an index and when it hurts.
- How would you find and fix a slow query?
- `UNION` vs `UNION ALL`? `DELETE` vs `TRUNCATE`?
- What is a window function and give a use case?
- Explain MVCC / how Postgres handles concurrent writes.

**Resources:** PostgreSQL official docs; *Use The Index, Luke!* (use-the-index-luke.com); pgexercises.com; *SQL Performance Explained*; DataLemur/StrataScratch.

---

## 4. Python for data (choose Python over Bash)

**Why:** Transformation, APIs, orchestration, and analysis. Python is the lingua franca of data engineering.

### 4a. Python fundamentals
- Types, variables, operators, f-strings.
- Control flow, loops, `range`, `enumerate`, `zip`.
- Functions, default/keyword args, `*args/**kwargs`, lambdas.
- Data structures: `list`, `tuple`, `dict`, `set`; comprehensions.
- Files: `open`, context managers, `pathlib`, `csv`, `json`.
- Errors: `try/except/finally`, custom exceptions.
- Modules & packages; `venv`; `pip`; `requirements.txt` / `pyproject.toml`.
- Typing hints, dataclasses; virtual environments; project layout.
- Testing with `pytest`; logging; debugging.

### 4b. Data libraries
- **pandas:** `read_csv/read_sql`, `DataFrame`/`Series`, filtering, `groupby`, `merge`/`join`, `pivot_table`, `apply`, `dt` accessors, missing data, dtypes, memory.
- **polars** (awareness): faster columnar engine, lazy execution.
- **DB access:** `psycopg2`/`psycopg` or **SQLAlchemy** (engine, sessions, parameterized queries — never string-format SQL).
- **HTTP/APIs:** `requests`/`httpx`, auth, pagination, retries, rate limits.
- **Serialization:** JSON, CSV, Parquet (`pyarrow`), Avro (awareness).
- **Notebooks:** Jupyter for exploration (not production).

### 4c. Automation & orchestration (intro)
- Write idempotent ETL scripts: extract → validate → transform → load.
- Scheduling with `cron`; then a taste of **Airflow** (DAGs, operators, backfills) or Prefect/Dagster.
- Config via env vars/`.env`; secrets handling.
- Retries, logging, data-quality checks.

**Milestone project:** An ETL job that pulls an API (or CSV), cleans it in pandas, loads it into Postgres, is scheduled, tested with pytest, and logs each run.

**Resources:** *Python for Data Analysis* (Wes McKinney, free online); Automate the Boring Stuff; pandas user guide; Real Python.

---

## 5. One cloud — AWS (with Azure/GCP mapping)

**Why:** Data and apps live in the cloud. Learn one deeply; concepts transfer.

### 5a. Core AWS
- **Accounts & access:** root vs IAM user, roles, policies, `least privilege`, MFA, access keys, `aws configure`.
- **Compute:** EC2 (instances, AMIs, key pairs, security groups, user data, Elastic IP), auto-scaling basics.
- **Storage:** S3 (buckets, keys, prefixes, lifecycle, versioning, policies, presigned URLs), EBS, EFS.
- **Networking:** VPC, subnets (public/private), route tables, internet gateway, NAT gateway, security groups vs NACLs, basic DNS (Route 53).
- **Managed DB:** RDS/Aurora PostgreSQL (instance classes, backups, parameter groups, Multi-AZ, read replicas).
- **Serverless:** Lambda, API Gateway, S3 event triggers; IAM execution roles.
- **IaC:** CloudFormation **or** Terraform (resources, state, plan/apply) — pick Terraform for portability.
- **CLI & SDK:** `aws s3`, `aws ec2`, `aws rds`, boto3 from Python.
- **Monitoring & cost:** CloudWatch (metrics, logs, alarms), billing, budgets.
- **Security hygiene:** never commit keys, use roles, encrypt at rest (KMS), IAM policies.

### 5b. Data services (awareness → pick as needed)
- **Athena** (query S3 with SQL), **Glue** (catalog + ETL), **Redshift** (warehouse), **Kinesis** (streaming), **EMR** (Spark/Hadoop), **Step Functions** (orchestration).

### 5c. Other clouds (map the concepts)
- **Azure:** ADLS Gen2 (≈S3), Azure SQL/Synapse (≈RDS/Redshift), Data Factory (≈Glue), AKS (≈EKS), Entra ID (≈IAM).
- **GCP:** GCS (≈S3), Cloud SQL/BigQuery (≈RDS/Redshift-BigQuery), Dataflow/Dataform (≈Glue/dbt), GKE (≈EKS), IAM.

**Milestone project:** Provision (via Terraform) an S3 bucket + RDS Postgres + one EC2/Lambda, load data, run a query from Python, and wire a CloudWatch alarm — all reproducible from code.

**Cert (optional but valued):** AWS Certified Cloud Practitioner (entry) → Solutions Architect Associate or Data Engineer Associate.

---

## 6. dbt (analytics engineering)

**Why:** The modern way to build warehouses: SQL models with versioning, tests, docs, lineage. Directly targets the "fewer interruptions, research-leaning" role.

**What to learn in detail**
- **Concepts:** dbt = "T in ELT". Models, the DAG, `ref()`/`source()` for dependencies, compilation to SQL.
- **Setup:** install `dbt-core` + adapter (e.g., `dbt-postgres`), `dbt init`, `profiles.yml`, `dbt_project.yml`, targets (dev/prod).
- **Project structure:** `models/staging`, `models/intermediate`, `models/marts`; `seeds/`, `tests/`, `macros/`, `snapshots/`, `analyses/`.
- **Commands:** `dbt run`, `dbt test`, `dbt build`, `dbt docs generate/serve`, `dbt seed`, `dbt snapshot`, `dbt compile`, `dbt debug`, selectors (`+model`, `model+`, `tag:`).
- **Materializations:** view, table, **incremental** (`is_incremental()`, unique_key, merge/delete+insert), ephemeral.
- **Sources:** `sources.yml`, freshness, `source()`.
- **Testing:** built-in (`not_null`, `unique`, `relationships`, `accepted_values`) + custom singular/generic tests.
- **Docs & lineage:** descriptions, `dbt docs`, exposure, DAG visualization.
- **Modeling:** staging (rename/cast/clean) → intermediate (joins/business logic) → marts (`dim_`/`fct_`), star schema / Kimball basics, grain, slowly changing dimensions (snapshots).
- **Macros & Jinja:** `{% %}`, `{{ }}`, loops, `dbt_utils`, `generate_surrogate_key`.
- **Packages:** `packages.yml`, `dbt_utils`, `dbt_expectations`.
- **Marts for BI:** expose tables for Metabase/Superset/Power BI.

**Milestone project:** Take the ETL output from §4, build staging → marts models with tests and docs, an incremental fact table, and a snapshot for an SCD-2 dimension. Serve `dbt docs`.

**Resources:** dbt Learn (free courses); dbt docs; *The Analytics Engineering Guide*; *Fundamentals of Data Engineering* (Reis & Housley).

---

## 7. Docker & Kubernetes basics

**Why:** Package services (apps, Postgres, pipelines) so they run anywhere; K8s is how modern platforms deploy.

### 7a. Docker
- **Concepts:** image vs container; layers; registry.
- **Dockerfile:** `FROM`, `WORKDIR`, `COPY`, `RUN`, `CMD` vs `ENTRYPOINT`, `ENV`, `ARG`, `EXPOSE`, multistage builds.
- **CLI:** `build`, `run` (`-d -p -v -e --name`), `ps`, `logs -f`, `exec -it`, `stop`, `rm`, `images`, `rmi`, `pull`, `push`.
- **Data:** named volumes, bind mounts; persistence for databases.
- **Networking:** bridge networks, port mapping, service-name DNS.
- **Compose:** `docker-compose.yml` for app + Postgres + dbt; `up -d`, `down`, `logs`.
- **Best practices:** small base images (slim/alpine), `.dockerignore`, non-root user, cache-friendly layer order.

### 7b. Kubernetes (basics)
- **Model:** cluster, node, control plane, pod, ReplicaSet, Deployment, Service (ClusterIP/NodePort/LoadBalancer), Ingress.
- **kubectl:** `get pods/svc/deploy`, `describe`, `logs`, `exec`, `apply -f`, `delete`, `port-forward`, `scale`.
- **Config:** ConfigMaps, Secrets, resource requests/limits, liveness/readiness probes.
- **Storage:** PersistentVolume / PersistentVolumeClaim.
- **Namespaces**, labels/selectors, rolling updates.
- **Helm** (awareness): charts, values, `helm install/upgrade`.
- **Managed:** EKS/GKE/AKS; local: `kind`/`minikube`.

**Milestone project:** Dockerize an app + Postgres with Compose; then deploy a Deployment+Service to a local `kind` cluster and expose it.

---

## 8. Phased 6-month plan (part-time)

Assumes ~10–12 focused hours/week. Compress if full-time.

| Phase | Weeks | Focus | Deliverable |
|---|---|---|---|
| 1 | 1–4 | Linux, Bash, Git | Bought into CLI; Bandit done; Git habit |
| 2 | 5–10 | SQL + PostgreSQL + tuning | 50 SQL problems; tuned-query milestone |
| 3 | 11–14 | Python for data | ETL project on GitHub |
| 4 | 15–18 | AWS core (+ IaC) | Terraform + RDS + S3 project |
| 5 | 19–22 | dbt | Warehouse with tests + docs |
| 6 | 23–26 | Docker/K8s + capstone | Capstone deployed; portfolio polished |

**Weekly cadence:** 2 hrs SQL practice (non-negotiable), 4 hrs current layer's project, 2 hrs reading/docs, 1 hr review/notes, 1 hr flash. Ship something every week.

---

## 9. Capstone portfolio projects (pick 2–3)

1. **Support-log analytics warehouse** *(leverages your background)* — ingest logs (Linux/shell) → Python ETL → Postgres → dbt models (top errors, MTTR, volume trends) → simple dashboard. Demonstrates the whole stack.
2. **Cloud data platform** — Terraform-provisioned S3 + RDS + Lambda, dbt transforms, scheduled, with tests and CI (GitHub Actions running `dbt build`).
3. **Domain dataset pipeline** *(track-specific)* — clinical/health (FHIR/OMOP) or bioinformatics (FASTQ→VCF) ETL into a warehouse with dbt marts.

Each repo needs: clear README (problem, architecture diagram, how to run), tests, and a short "what I learned" section.

---

## 10. Track add-ons (choose based on target role)

- **Analytics engineer:** dimensional modeling (Kimball), grain, star schema, SCDs; BI tool (Metabase/Superset/Power BI); metrics layer; more dbt depth (incremental, snapshots, exposures).
- **Data engineer:** Airflow/Dagster, Spark (PySpark), Kafka/streaming, Parquet/Delta/Iceberg, cloud data services (Glue/Redshift/Athena), data contracts, CI/CD.
- **Bioinformatics / research:** molecular biology basics (central dogma), file formats (FASTA/FASTQ, SAM/BAM, VCF), tools (samtools, bcftools, BWA), R/Bioconductor, HPC + SLURM, workflow managers (Nextflow/Snakemake). *Linux + SQL are core here.*
- **Clinical / health data:** FHIR/HL7 v2, EHR data models (Epic/Cerner), OMOP CDM, REDCap, HIPAA/de-identification, quality reporting.

---

## 11. Study resources

- **Linux/Bash:** *The Linux Command Line*; OverTheWire Bandit; explainshell.com; shellcheck.
- **Git:** *Pro Git*; Learn Git Branching.
- **SQL:** *SQL Performance Explained*; use-the-index-luke.com; pgexercises.com; DataLemur; StrataScratch; LeetCode SQL.
- **PostgreSQL:** official docs (read the tutorials + the "Performance Tips" chapter); *PostgreSQL 14 Administration Cookbook*.
- **Python:** *Python for Data Analysis* (free); pandas user guide; Automate the Boring Stuff.
- **Cloud:** AWS Skill Builder + docs; Stephane Maarek (Udemy) courses; A Cloud Guru.
- **dbt:** dbt Learn (free); dbt docs; *Fundamentals of Data Engineering*.
- **Docker/K8s:** Docker docs; *Docker Deep Dive*; Kubernetes docs; *Kubernetes Up & Running*.
- **Career/big picture:** *Fundamentals of Data Engineering*; *Designing Data-Intensive Applications* (advanced, later).

---

## 12. Interview preparation

- **SQL rounds:** window functions, joins, aggregation, date math, dedupe, gaps-and-islands, "explain this plan." Practice out loud and under time.
- **Data modeling / system design:** design a warehouse for X; star vs normalized; batch vs streaming; incremental loads; idempotency; late-arriving data.
- **Linux troubleshooting scenarios:** "server is slow", "disk full", "service won't start", "high load average" — walk through diagnosis.
- **Cloud fundamentals:** IAM least privilege, S3 permissions, RDS vs self-managed, cost control, VPC basics.
- **dbt:** model layering, testing strategy, incremental logic, docs/lineage.
- **Behavioral:** have STAR stories from support work (a hard incident, a process you improved, cross-team collaboration).
- **Portfolio walkthrough:** be ready to whiteboard your capstone's architecture and defend trade-offs.

---

## 13. "Job-ready" checklist

- [ ] Comfortable on the Linux CLI without looking things up; can troubleshoot a host.
- [ ] Write and debug Bash automation safely (`set -euo pipefail`).
- [ ] Git daily; can branch, rebase, resolve conflicts, open a clean PR.
- [ ] Solve medium SQL problems with window functions and CTEs unaided.
- [ ] Read `EXPLAIN ANALYZE` and tune a query with indexes.
- [ ] Build a tested Python ETL that loads Postgres.
- [ ] Provision cloud infra (Terraform) with RDS + S3; use the CLI/SDK.
- [ ] Ship a dbt project with staging/marts, tests, docs, an incremental model.
- [ ] Containerize a multi-service app with Compose; deploy a pod to K8s.
- [ ] 2–3 portfolio repos with READMEs, diagrams, and tests.
- [ ] Can talk through every item above confidently in an interview.

---

*Companion file: `career-notes.md` (career paths and the "fewer interruptions" rationale).*
