# 26-Week Study Schedule — The Realistic Stack

Companion to `stack-learning-guide.md`. Breaks the 6-month plan (from `career-notes.md`) into weeks with specific daily exercises.

**Assumes:** ~10–12 focused hours/week part-time (≈1.5–2 h on weekdays, 2–4 h on the weekend).
**Rhythm:** Mon–Fri = new material + drills; Sat–Sun = build the deliverable. Every week ends with a **Deliverable** (a file or repo) and a **Checkpoint** (self-test).
**Rule:** every Sunday you ship something. If a week slips, drop the *Stretch*, never the deliverable.

## Setup once (before Week 1)
- Install: WSL2 (Ubuntu) **or** a cloud VM; a code editor (VS Code); Git; Docker Desktop later; Python 3.11+.
- Create a GitHub account and a `learning/` folder of repos.
- Start a running `journal.md` — one paragraph per day on what you did and what confused you.

---

# Phase 1 — Foundations (Weeks 1–4)

## Week 1 — Linux orientation: filesystem, files, permissions
- **Mon:** Set up Ubuntu (WSL2/VM). Run `ls -la /`, `man ls`, `man hier`. In your journal, list the purpose of `/etc`, `/var`, `/home`, `/usr`, `/tmp`.
- **Tue:** File ops: `mkdir -p`, `touch`, `cp -r`, `mv`, `rm -r`, `ln -s`, `ln`. Build a sandbox tree and inspect it with `tree` and `find`.
- **Wed:** Permissions lab: create files, apply `chmod 600 644 755 754`, set `umask`, `chown` with `sudo` (in VM). Decode 5 given permission strings.
- **Thu:** View/search: `cat`, `less`, `head -n 20`, `tail -f`, `wc -l`, `grep -rin "error" /var/log 2>/dev/null`. Find the 3 most repeated strings in a log.
- **Fri:** `find` by name/size/mtime/type and `-exec`; `locate`. List files modified in the last 24 h; delete files >10 MB in a sandbox.
- **Weekend:** OverTheWire **Bandit 0–5**. Write `linux-cheatsheet.md` (every command used, one line each).
- **Deliverable:** cheatsheet + Bandit progress log. **Checkpoint:** explain `chmod 754` and FHS without notes.

## Week 2 — Text processing, pipes, processes, services
- **Mon:** Pipes & redirection: `|`, `>`, `>>`, `2>`, `2>&1`, `tee`. Chain `cut | sort | uniq -c | sort -nr`.
- **Tue:** `sed` (substitute, delete, line ranges), `tr`, `xargs`. Clean a messy CSV (strip blanks, change delimiter).
- **Wed:** `awk`: `-F`, `$1..$NF`, patterns, `BEGIN/END`, sum/average a column from a log or CSV.
- **Thu:** Processes & environment: `ps aux`, `top`/`htop`, `kill`, `nice`, `PATH`, `export`, `.bashrc`, aliases.
- **Fri:** Services & cron: `systemctl start/status/enable`, `journalctl -u <svc> -n 50`, `crontab -e`. Schedule a script to run every minute and log output.
- **Weekend:** Log-analysis drill on an nginx/Apache log — **top 10 IPs, top 10 URLs, count of 5xx** using only shell tools. Bandit 6–12.
- **Deliverable:** `log-report.sh` + a report file. **Checkpoint:** explain a 4-stage pipeline command aloud.

## Week 3 — Bash scripting + Git basics
- **Mon:** Variables, quoting (`"$x"` vs `'$x'`), args `$1 $@ $#`, exit codes, `readonly`.
- **Tue:** Conditionals `[[ ]]`, file tests (`-f -d -e`), `for`/`while`/`until`, `case`.
- **Wed:** Functions, arrays/associative arrays, `set -euo pipefail`, `trap`, run `shellcheck`.
- **Thu:** Build `backup.sh`: tar a directory with a timestamp, log start/end, return proper exit codes, handle a missing dir.
- **Fri:** Git start: `git init`, `add`, `commit`, `status`, `log --oneline`, `diff`, `.gitignore`. Make 5 clean commits.
- **Weekend:** Push cheatsheet + scripts to a GitHub repo with a real README. Bandit 13–20.
- **Deliverable:** first GitHub repo. **Checkpoint:** repo has ≥6 commits and a README that explains how to run each script.

## Week 4 — Git collaboration + Linux troubleshooting + Phase 1 capstone
- **Mon:** Branching: `branch`, `switch -c`, `merge`; create and resolve one merge conflict.
- **Tue:** Remotes: `clone`, `fetch`, `pull`, `push`, `-u`; open a feature branch + Pull Request.
- **Wed:** Rewriting history on a throwaway repo: `rebase -i` (squash/reword), `cherry-pick`, `revert`, `reset --soft/--mixed/--hard`.
- **Thu:** Troubleshooting scenarios: disk full (`df -h`, `du -sh *`), high load (`top`, `ps`), service down (`systemctl`, `journalctl`), SSH key auth.
- **Fri:** Write `troubleshoot.md` — a symptom → commands → fix playbook from Thursday.
- **Weekend (Phase 1 capstone):** Provision a VM, harden SSH to keys-only, run a service under `systemd`, add a nightly cron backup job.
- **Deliverable:** configured VM + `troubleshoot.md`. **Checkpoint:** can SSH with keys, restart a service, and explain the backup job.

---

# Phase 2 — SQL + PostgreSQL (Weeks 5–10)

> Non-negotiable weekly habit from here on: **2 h of SQL problems** every week (DataLemur / StrataScratch / LeetCode).

## Week 5 — SQL basics
- **Mon:** Install Postgres (Docker is easiest). `psql` intro (`\l`, `\c`, `\d`, `\dt`). Create a DB, a table, `INSERT` rows.
- **Tue:** `SELECT`, `WHERE`, `ORDER BY`, `LIMIT`, `DISTINCT`, `IN`, `BETWEEN`, `LIKE/ILIKE`, `IS NULL` (and why `= NULL` fails).
- **Wed:** Aggregates + `GROUP BY` + `HAVING`; `COUNT(*)` vs `COUNT(col)` vs `COUNT(DISTINCT)`.
- **Thu:** 5 easy filtering/aggregation problems (DataLemur).
- **Fri:** Dates: `date_trunc`, `extract`, `interval`, `age`, `to_char`.
- **Weekend:** Load a sample dataset (Chinook or a CSV you like). Write 10 basic queries.
- **Deliverable:** `queries-basics.sql`. **Checkpoint:** 10/10 problems solved unaided.

## Week 6 — Joins + subqueries
- **Mon:** `INNER`, `LEFT`, `RIGHT`, `FULL` joins; self-joins (employees ↔ managers).
- **Tue:** `CROSS` join; anti-join (`LEFT ... WHERE ... IS NULL`); `EXISTS`/`NOT EXISTS` vs `IN`.
- **Wed:** Subqueries: scalar and correlated; rewrite a correlated subquery as a join.
- **Thu:** 6 join problems.
- **Fri:** Chinook exercises: top genre per country, customers with no orders, etc.
- **Weekend:** Design `customers/orders/order_items/products`; write 10 join queries.
- **Deliverable:** `schema.sql` + `queries-joins.sql`. **Checkpoint:** explain row counts when a join is `LEFT` vs `INNER`.

## Week 7 — CTEs + window functions
- **Mon:** CTEs (`WITH`), multiple CTEs for readability; recursive CTE for an org chart.
- **Tue:** `ROW_NUMBER`, `RANK`, `DENSE_RANK`; the difference and when each is right.
- **Wed:** `LAG`/`LEAD`; running totals; moving averages; `PARTITION BY` + frames (`ROWS BETWEEN`).
- **Thu:** 6 problems: Nth per group, running total, month-over-month change.
- **Fri:** Gaps-and-islands intro; consecutive-logins problem.
- **Weekend:** 10 window-function problems, solutions saved.
- **Deliverable:** `window-functions.sql`. **Checkpoint:** compute a running total and a per-group rank from memory.

## Week 8 — PostgreSQL specifics
- **Mon:** Types (`numeric` not float for money, `timestamptz`, `jsonb`, arrays, `uuid`), constraints, identity columns.
- **Tue:** `psql` deeper: `\copy`, `\timing`, `\x`. Bulk-load ~1M rows.
- **Wed:** Indexes: B-tree, partial, composite (leftmost-prefix), covering (`INCLUDE`).
- **Thu:** `EXPLAIN (ANALYZE, BUFFERS)`; scan & join types; compare plans with/without an index.
- **Fri:** Transactions, isolation levels, MVCC; reproduce a deadlock on purpose, then fix it.
- **Weekend:** Indexing lab — create indexes, measure before/after, write the plan deltas.
- **Deliverable:** `indexing-lab.md`. **Checkpoint:** read a plan and name the bottleneck.

## Week 9 — Performance tuning
- **Mon:** Enable `pg_stat_statements`; set `log_min_duration_statement`; find the slowest queries.
- **Tue:** Index strategy; why an index is ignored (type mismatch, function on the column → expression index).
- **Wed:** Query rewriting: push predicates, replace correlated subqueries, eliminate `SELECT *`, avoid N+1.
- **Thu:** `VACUUM`/`ANALYZE`/autovacuum; table bloat.
- **Fri:** Materialized views + refresh strategy; partitioning intro for time-series.
- **Weekend (milestone):** Take a slow analytic query and reduce runtime ≥80%; document plans and reasons.
- **Deliverable:** `tuning-report.md`. **Checkpoint:** present the before/after and justify each change.

## Week 10 — DB operations + Phase 2 capstone
- **Mon:** Backup/restore: `pg_dump`, `pg_restore` (custom format), PITR concept, WAL.
- **Tue:** Replication basics; stand up a read replica in Docker.
- **Wed:** Roles/privileges, `GRANT`/`REVOKE`, `pg_hba.conf`, RLS awareness, `pgbouncer`.
- **Thu:** Model a star schema with foreign keys for a small domain.
- **Fri:** Review weak spots; redo 5 missed problems.
- **Weekend (Phase 2 capstone):** Build a normalized schema and 20 analytic queries (≥5 with window functions, ≥3 tuned). Document it.
- **Deliverable:** repo with `schema.sql`, `analytics.sql`, tuning notes. **Checkpoint:** schema is 3NF for OLTP or star for analytics — and you can say why.

---

# Phase 3 — Python for data (Weeks 11–14)

## Week 11 — Python fundamentals
- **Mon:** `venv`, `pip`, run a script; types, operators, f-strings.
- **Tue:** Control flow, loops, `enumerate`/`zip`, comprehensions, functions.
- **Wed:** `list`/`dict`/`set`/`tuple`; files, `pathlib`, `csv`, `json`.
- **Thu:** Errors/exceptions, modules, typing hints, dataclasses.
- **Fri:** `pytest` + `logging` basics; write 3 unit tests.
- **Weekend:** Build a CLI tool that reads a CSV, summarizes it, and writes JSON.
- **Deliverable:** `csv-summary/` repo. **Checkpoint:** run it on a new CSV without edits.

## Week 12 — pandas
- **Mon:** `read_csv`/`read_sql`; DataFrame/Series; filtering, indexing, sorting.
- **Tue:** `groupby`/`agg`, `merge`/`join`, `pivot_table`.
- **Wed:** Missing data, dtypes, `apply`/`map`, datetime accessor.
- **Thu:** Load 1M rows; memory/perf; chunked reads.
- **Fri:** Explore a real dataset; write 5 insights.
- **Weekend:** Analysis notebook + a short written summary.
- **Deliverable:** `analysis/` notebook. **Checkpoint:** explain an insight that required a merge + groupby.

## Week 13 — DB access + APIs
- **Mon:** `psycopg2`/`SQLAlchemy`: connect, parameterized queries (never f-string SQL), create tables.
- **Tue:** `requests`/`httpx`: auth, pagination, retries, rate limiting.
- **Wed:** JSON ↔ DataFrame; write Parquet with `pyarrow`.
- **Thu:** Write `extract()` from a public API (e.g., a weather/data API).
- **Fri:** `transform()` + idempotent `load()` (upserts) into Postgres.
- **Weekend:** ETL v1 end-to-end.
- **Deliverable:** `etl/` repo. **Checkpoint:** re-running the job doesn't duplicate rows.

## Week 14 — ETL hardening + orchestration intro + Phase 3 capstone
- **Mon:** Config via env/`.env`, logging, retries, error handling.
- **Tue:** `pytest` tests for the transform step.
- **Wed:** Idempotency review; schedule with cron.
- **Thu:** Airflow intro (DAG, tasks, schedule) or Prefect/Dagster quickstart.
- **Fri:** Docs + README; architecture sketch.
- **Weekend (Phase 3 capstone):** Scheduled ETL: source → pandas → Postgres, tested, logged, documented.
- **Deliverable:** capstone repo. **Checkpoint:** explain extract → transform → load and where it can fail.

---

# Phase 4 — AWS (Weeks 15–18)

## Week 15 — AWS core, IAM, S3
- **Mon:** Account + MFA; create an IAM user/role with least privilege; `aws configure`.
- **Tue:** S3: buckets, bucket policies, versioning, lifecycle, presigned URLs.
- **Wed:** CLI: `aws s3 ls/cp/sync`; boto3 from Python.
- **Thu:** Storage classes + cost basics; Billing/Budgets.
- **Fri:** Write a least-privilege IAM policy for read-only S3 access.
- **Weekend:** Upload/download a dataset with CLI and Python.
- **Deliverable:** `s3-notes.md` + script. **Checkpoint:** explain why access keys should never be committed.

## Week 16 — EC2 + VPC + networking
- **Mon:** Launch EC2 (AMI, key pair, security group, user data); SSH in.
- **Tue:** VPC, subnets, route tables, IGW/NAT; SG vs NACL.
- **Wed:** Create an RDS Postgres; connect from EC2 with `psql`.
- **Thu:** CloudWatch metrics, logs, an alarm.
- **Fri:** Instance types, cost, scaling basics.
- **Weekend:** App on EC2 talking to RDS.
- **Deliverable:** deployment notes + diagram. **Checkpoint:** explain how traffic reaches EC2 and why RDS is in a private subnet.

## Week 17 — IaC (Terraform) + serverless
- **Mon:** Terraform: providers, resources, `init/plan/apply/destroy`, state.
- **Tue:** Provision S3 + a security group via Terraform.
- **Wed:** Provision RDS via Terraform; variables/outputs.
- **Thu:** Lambda + IAM execution role + S3 event trigger.
- **Fri:** API Gateway + Lambda.
- **Weekend:** Reproducible infra: `terraform apply` builds it, `destroy` tears it down.
- **Deliverable:** `infra/` repo. **Checkpoint:** rebuild the environment from code alone.

## Week 18 — Data services + Phase 4 capstone
- **Mon:** Glue catalog + Athena query over S3.
- **Tue:** Redshift overview; when it beats RDS.
- **Wed:** Kinesis streaming + Step Functions awareness.
- **Thu:** Azure/GCP concept mapping (read the equivalents).
- **Fri:** Cloud cert practice questions (CCP/SAA sampling).
- **Weekend (Phase 4 capstone):** Terraform-provisioned S3 + RDS + Lambda; load data, query from Python, add a CloudWatch alarm.
- **Deliverable:** repo + architecture diagram. **Checkpoint:** whiteboard the whole flow.

---

# Phase 5 — dbt (Weeks 19–22)

## Week 19 — dbt setup + models
- **Mon:** Install `dbt-core` + `dbt-postgres`; `dbt init`; `profiles.yml`; `dbt debug`.
- **Tue:** First models; `ref()`; `dbt run`; materializations (view vs table).
- **Wed:** Structure `models/staging`, `intermediate`, `marts`; `sources.yml`.
- **Thu:** Tests: `not_null`, `unique`, `relationships`, `accepted_values`; `dbt test`.
- **Fri:** `dbt docs generate` + `serve`; read lineage.
- **Weekend:** Turn the Week-13 ETL output into staging models.
- **Deliverable:** dbt project v1. **Checkpoint:** every model passes tests and has a description.

## Week 20 — Transformations + modeling
- **Mon:** Staging: rename, cast, clean one table per source.
- **Tue:** Intermediate: joins and business logic.
- **Wed:** Marts: `dim_`/`fct_`, choose the grain, star schema.
- **Thu:** Surrogate keys with `dbt_utils.generate_surrogate_key`.
- **Fri:** `dbt build`; selectors (`+model`, `tag:`).
- **Weekend:** Full staging → marts pipeline.
- **Deliverable:** documented DAG + tested marts. **Checkpoint:** state the grain of each fact table.

## Week 21 — Advanced dbt
- **Mon:** Incremental models (`is_incremental()`, `unique_key`).
- **Tue:** Snapshots for SCD-2.
- **Wed:** Macros + Jinja; packages (`dbt_utils`, `dbt_expectations`).
- **Thu:** Seeds, vars, dev/prod targets.
- **Fri:** Exposures, docs polish, one custom generic test.
- **Weekend:** Add an incremental fact table + an SCD snapshot.
- **Deliverable:** complete `models/`. **Checkpoint:** explain what `is_incremental()` changes at runtime.

## Week 22 — CI + Phase 5 capstone
- **Mon:** GitHub Actions to run `dbt build` on push.
- **Tue:** dbt Cloud vs Core overview.
- **Wed:** Serve marts to BI (Metabase/Superset quickstart).
- **Thu:** Data-quality checks + alerting.
- **Fri:** Phase review; finalize docs.
- **Weekend (Phase 5 capstone):** End-to-end source → dbt (staging/marts/incremental/snapshot), tested, documented, CI-green.
- **Deliverable:** capstone repo. **Checkpoint:** the CI badge is green and `dbt docs` shows the full lineage.

---

# Phase 6 — Docker/K8s + capstone (Weeks 23–26)

## Week 23 — Docker
- **Mon:** Install Docker; images vs containers; `run/ps/logs/exec/stop/rm`.
- **Tue:** Write a Dockerfile for the Python ETL app; build and run it.
- **Wed:** Volumes/bind mounts so Postgres data persists.
- **Thu:** Networks, env vars, port mapping; container-to-container DNS.
- **Fri:** Best practices: `.dockerignore`, multistage build, smaller image.
- **Weekend:** Dockerize the ETL app.
- **Deliverable:** `Dockerfile` + notes. **Checkpoint:** explain image layers and why cache order matters.

## Week 24 — Compose + registries
- **Mon:** `docker-compose.yml` for app + Postgres.
- **Tue:** Add a dbt service; profiles inside the network.
- **Wed:** `up -d`/`down`/`logs`; healthchecks; `depends_on`.
- **Thu:** Push the image to a registry (Docker Hub/ECR).
- **Fri:** Image security: non-root user, scan basics.
- **Weekend:** One-command full stack via Compose.
- **Deliverable:** `docker-compose.yml`. **Checkpoint:** `docker compose up` brings up the whole stack from clean.

## Week 25 — Kubernetes basics
- **Mon:** Concepts: cluster/node/pod/deployment/service; install `kind`/`minikube`.
- **Tue:** `kubectl get/describe/logs/exec/apply/delete`.
- **Wed:** Deployment + Service; `port-forward`; `scale`.
- **Thu:** ConfigMaps/Secrets; liveness/readiness probes; resource limits.
- **Fri:** Volumes/PVC; Helm awareness.
- **Weekend:** Deploy the app to `kind` and expose it.
- **Deliverable:** manifests. **Checkpoint:** explain Pod vs Deployment vs Service.

## Week 26 — Capstone polish + interview prep
- **Mon:** Finalize capstone: architecture + README + diagram (+ tests/CI if missing).
- **Tue:** Timed SQL drill (6 problems, 45 min).
- **Wed:** Linux troubleshooting mock aloud; narrate an `EXPLAIN ANALYZE`.
- **Thu:** Write 4 STAR stories from your support background.
- **Fri:** Rehearse a 5-minute portfolio walkthrough; prep questions to ask.
- **Weekend:** Apply to 5 target roles; record feedback to guide the next cycle.
- **Deliverable:** polished portfolio + 5 applications sent. **Checkpoint:** you can explain every project end-to-end.

---

## Progress tracker

| Phase | Weeks | Deliverable shipped? |
|---|---|---|
| 1 Foundations | 1–4 | ☐ |
| 2 SQL/Postgres | 5–10 | ☐ |
| 3 Python | 11–14 | ☐ |
| 4 AWS | 15–18 | ☐ |
| 5 dbt | 19–22 | ☐ |
| 6 Docker/K8s + capstone | 23–26 | ☐ |

## Weekly cadence reminder
- **Mon–Fri:** ~1.5–2 h — 30 min review/drill, 60–90 min new material, 15 min journal.
- **Weekend:** 2–4 h — build the week's deliverable.
- **Every week:** ≥2 h SQL practice (Weeks 5+).
- **Every Sunday:** commit and push. No deliverable = the week isn't done.

*See `stack-learning-guide.md` for the detailed topic lists behind each week, and `career-notes.md` for the target roles and the "fewer interruptions" rationale.*
