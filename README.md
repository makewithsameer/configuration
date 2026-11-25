# QuickNotify settings (read by the config server)

One folder per app. The config server reads this repo (branch `release`) **every time an app starts**,
so to change a setting: edit the file, commit, push, then restart that one app:

| Folder | App | Restart with |
|---|---|---|
| `properties_configuration/api-make-v1-notification/` | notification service | `./deploy.sh restart notification` |
| `properties_configuration/api-dev-v1-account-manager/` | account manager | `./deploy.sh restart account` |
| `properties_configuration/frontend/` | web app (`config.js`) | `./deploy.sh restart frontend` |
| `properties_configuration/api-dev-v1-gateway/` | API gateway (its SQL only) | `./deploy.sh restart gateway` |

**This repo is public: never put a secret here** (passwords, tokens, keys, app passwords).
Secrets and wiring (database, Kafka, Aerospike addresses) stay in `deploy/.env` and `deploy/config/*.env`
on the server; anything set there wins over this repo.

## SQL and tunables (in each app's `application.properties`)

Every SQL statement the backends run is in the app's `application.properties` ("SQL" section at the end), by name,
not in the Java code:

| Key | Used as |
|---|---|
| `sql.<area>.<what>=…` | plain SQL, run by code as `Sql.q("<area>.<what>")` |
| `jpa.<Entity>.<method>=…` | a repository method's query (JPQL, or native when the method says `nativeQuery = true`) |
| `jpa.<Entity>.<method>.count=…` | the count query of a paged repository method (optional) |

* The same file is **built into each jar**, so a GitHub outage never breaks the queries; the git copy wins.
* Values always go in as `?` / `:name` parameters. `{part}` markers are filled by code from a fixed set, never from user input.
* A long statement continues on the next line with ` \` at the end of the line.
* `qn.tune.*`: limits, timeouts, TTLs and batch sizes that used to be fixed in code (read at start: restart the app).
* `./deploy.sh test` fails when the code uses a statement or tunable the file lacks, or the file has one no code uses.

