# QuickNotify settings (read by the config server)

One folder per app. The config server reads this repo (branch `release`) **every time an app starts**,
so to change a setting: edit the file, commit, push, then restart that one app:

| Folder | App | Restart with |
|---|---|---|
| `properties_configuration/api-make-v1-notification/` | notification service | `./deploy.sh restart notification` |
| `properties_configuration/api-dev-v1-account-manager/` | account manager | `./deploy.sh restart account` |
| `properties_configuration/frontend/` | web app (`config.js`) | `./deploy.sh restart frontend` |

**This repo is public: never put a secret here** (passwords, tokens, keys, app passwords).
Secrets and wiring (database, Kafka, Aerospike addresses) stay in `deploy/.env` and `deploy/config/*.env`
on the server; anything set there wins over this repo.
