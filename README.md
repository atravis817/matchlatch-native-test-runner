# MATCHLATCH isolated native test runner

WO-ML-018T Stage A prepares static infrastructure only. **Do not run this workflow
until separate Founder Stage-B authorization names its exact control commit.**
All six native PostgreSQL race scenarios remain NOT RUN at publication.

This public control repository contains only this README and
`.github/workflows/native-authority.yml`. It is separate from the application and
must remain excluded from Vercel and every deployment integration. No app source,
credentials, customers, merchant catalog data, secrets, variables or environments
belong here. No application branch or tag moves are required.

## Fixed inputs and dependency provenance

| Component | Fixed revision / SHA-256 |
| --- | --- |
| Public application repository | `https://github.com/atravis817/matchlatch.git` |
| Application commit | `163c87a4d81dd2d61572571a71ea73d7113f76ae` |
| Application tree | `fdbb6d1cdf83289d4fed49cd55bebf27266ca106` |
| Node.js Linux x64 archive | `v24.22.0`; `cc2f601cee82d9a5a233302fe307abc600727035fd7d2c3a3571755989e95008` |
| PostgreSQL source archive | `17.11`; `dd27f2b3c59e73ed14aa3324901242bf69a032a6347805f274e6260322d42979` |
| GitHub Actions dependencies | None: no `uses` steps, checkout action, setup action or third-party action |

Checksums were read from the official publisher manifests on 2026-10-15:

- https://nodejs.org/dist/v24.22.0/SHASUMS256.txt
- https://ftp.postgresql.org/pub/source/v17.11/postgresql-17.11.tar.bz2.sha256

The workflow downloads those exact archives from the same publishers and checks
the embedded hashes before extraction. It never chooses latest or retries a
failed download. No npm/pip/apt installation, Docker image or external package
repository is used. PostgreSQL builds as the ordinary runner user into an owned
temporary prefix; no sudo, system service or default distro cluster is used.
`configure --without-readline --without-zlib --without-icu` avoids those optional
libraries. This is a native server, not PGlite or a hosted database.

**Remaining mutability:** the standard `ubuntu-24.04` image, kernel, compiler,
linker, libc and preinstalled utilities are controlled by GitHub. This is not a
bit-reproducible system image. Required existing tools: Git, curl/TLS certificates,
GNU make, GCC, Bison, Flex, Perl, Bash, Python3, tar, xz, bzip2, sha256sum, timeout
and coreutils. Image/tool versions are logged; missing tools or unsupported
configuration fail closed. No software is installed during Stage A. The build's
time/compatibility and archive bytes have not been runtime-verified by Stage A.

## Permissions, isolation and cost

Only `workflow_dispatch` exists. One standard GitHub-hosted Ubuntu24.04 x64 job,
15-minute total limit; fixed concurrency group, `cancel-in-progress: false`.
No push, PR, schedule, repository_dispatch, workflow_run, reusable trigger,
automatic retry, cache, artifact upload, package publication or deployment.
`permissions: {}` removes configurable GITHUB_TOKEN permissions; no token is
passed to downloads/Git/tests, no secrets expression, OIDC or environment exists.
Git source retrieval is anonymous into a new detached scratch checkout; hooks
and external Git configuration are disabled. Full object fsck, exact commit/tree,
237-blob count, no symlinks/submodules and clean source are required.

The Founder/CTPO supplied owner-observed settings evidence in
https://github.com/atravis817/matchlatch/pull/9#issuecomment-6194943190 : Actions
enabled, read-only default token, no secrets/variables/environments/webhooks,
Vercel restricted to the application repository, GitHub Free billing. Public
visibility and write access were separately checked through the GitHub connector.
The connector cannot independently read the protected settings endpoints; owner
evidence is distinguished from API inspection. Broad allowed-actions policy and
unchecked full-SHA-policy enforcement were not changed; this workflow uses no
actions at all. No new account grants or billing changes are necessary.

GitHub documents free standard hosted compute for public repositories and
excludes ordinary logs/summary from artifact storage billing:
https://docs.github.com/en/billing/concepts/product-billing/github-actions .
This design uses no chargeable artifact/cache/custom-image storage or paid runner.
**Stop if public/standard/$0 eligibility changes or becomes uncertain.** Do not
add a payment method, upgrade, change a budget or substitute infrastructure.
Current $0 evidence does not authorize any future charge.

Public job logs disclose synthetic test strings, source/workflow identifiers,
backend PIDs, lock evidence, versions and temporary paths. They persist after VM
disposal. No full environment dumps or credentials are printed; downloads/builds
and fixtures can still emit diagnostic text. Effective repository log retention
duration was not supplied in the owner evidence: record and explicitly accept it
before Stage B. No job logs exist from Stage A. Do not upload evidence as artifacts
to work around retention. Authorized reviewers may download ordinary logs and
record sanitized results in application PR9; deleting VM files does not delete
GitHub logs. No claim of zero outbound network access is made.

## Separate Stage-B approval packet (not granted)

Before requesting approval, CTPO must review the exact published control commit,
both files and hashes, publisher checksums, static review evidence, current
owner-verified isolation and effective public-log retention. Stop on discrepancies.
Then the Founder may authorize **one dispatch / one attempt only**, against main
at that reviewed control SHA, for the fixed application commit/tree above.
No automatic rerun: `GITHUB_RUN_ATTEMPT` must be1. Actor must be `atravis817`.
The approval URL and typed confirmation are auditable human attestations; the
workflow does not fetch or authenticate the approval comment. They do not replace
the actual separate Founder decision. Write access/manual dispatch remains a
human governance boundary, not a cryptographic one-run authorization mechanism.

Proposed authorization scope: one standard runner for at most15minutes, PG17.11
source compile/install in its temporary directory, Node24.22.0 extraction there,
one fresh local cluster/database with max10connections, synthetic fixtures,
exactly one invocation of the existing six-scenario launcher. No source repair,
second attempt, app ref movement, live database/provider access or deployment.
Only source/tool retrieval requires network; tests use127.0.0.1. No network
firewall is configured. A failure means report and stop, not patch/retry.

After approval, the authorized operator uses the manual workflow on reviewed main
with these inputs (do not do this during Stage A):

- `approval_url`: exact separate Founder Stage-B comment URL on application PR9.
- `reviewed_control_sha`: full approved control SHA, equal to the run's GITHUB_SHA.
- `safety_confirmation`: `STAGE_B_APPROVED_ZERO_COST_SYNTHETIC_ONLY`.

Runtime preflight checks actor, repository, event, main, attempt1, hosted Linux
x64, Ubuntu24.04, nonroot user and input bindings before allocation. Billing,
integration inventory and retention cannot be proved by a typed input: the
operator must verify them before dispatch. The prepared workflow cannot run the
tests without provisioning the approved future ephemeral runner, so Stage-A
static validation is not a successful execution claim.

## Tests, evidence and cleanup

The fixed launcher invokes `scripts/verify-shopify-authority-races.mjs` once via:

```sh
MATCHLATCH_NATIVE_LOCAL_APPROVED=1 timeout --kill-after=5s 105s bash scripts/run-shopify-authority-native-local.sh
```

The application harness has its own90-second deadline and independent persistent
psql processes. It applies four SQL fixtures only in the new disposable database:
sync, acceptance, privilege correction, authority. It verifies distinct backend
PIDs; simultaneous claims with one winner; revoke before claim; revoke between
claim/commit; commit before revoke; expiry after claim. No Shopify/Supabase calls
or production/customer data are allowed. The local launcher uses mode700 storage,
loopback-only PostgreSQL, a random port, one database and synthetic roles/data.
Local trust authentication is confined to this exclusive disposable VM.

Capture run URL/id/attempt, control/source/tree pins and fsck result, runner/tool
versions, archive checksum checks, timestamps/elapsed duration, exit code, all six
PASS lines, final `Native races: 6 PASS / 0 FAIL / 0 SKIP`, backend PID/advisory
lock wait evidence and cleanup result. Expiry has a real delay in the existing
harness; wrapper elapsed time is total duration, not a new per-scenario assertion.
Stage A does not execute any of these operations or claim any scenario passed.

Setup limit10minutes; scenario step2minutes; always-run cleanup1minute, within the
job15-minute cap. No compiler/test suite from dependencies is run (`make`, not
`make check`). Build/install logs and final160lines per evidence file are streamed
by cleanup; native launcher output is streamed in full. On ordinary failure the
cleanup step still executes. It verifies owned paths/UID/executable/PGDATA before
stopping any remaining owned postmaster, verifies stopped status/process absence,
checks port closure when its PID file remains, and removes only this run's owned
`/tmp/ml018t-<run>-<attempt>` tree. The short path avoids PostgreSQL Unix-socket
length limits. The original launcher is unchanged and only stops its cluster;
the wrapper supplies independent removal/stop verification.

If stop/ownership checks fail, cleanup fails rather than deleting uncertain data.
Hard cancellation, job timeout or VM loss can prevent cleanup; provider VM
disposal is the final isolation boundary, not evidence a trap succeeded. Preserve
the actual cleanup outcome in the report. The retained workflow remains manual;
every later execution needs separate authorization. Logs follow their effective
GitHub retention policy, independently of local cleanup.

Coordination: https://github.com/atravis817/matchlatch/pull/9 and
https://github.com/atravis817/matchlatch/issues/7 . Stage-A publication does not
authorize Stage B, security sign-off, hosted authority SQL, catalog import,
activation, scheduling, commerce or any application release.
