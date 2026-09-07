# De Plaat Deployment Repo - Agent Instructions

These instructions are for Codex/ChatGPT agents working on De Plaat gh0stcloud deployment settings.

## Local Paths

- Current local app repo: `/Users/bettinahaehner/Development/github.com/colibriblack/de-plaat`.
- Current local deployment repo: `/Users/bettinahaehner/Development/github.com/colibriblack/gh0stcloud-deployments`.
- The old `Documents/Development/github.com/colibriblack/...` location is obsolete. If a session starts there, switch to the current paths above before reading or writing files.

## Working Style

- Treat the user as a beginner/non-technical customer who wants guided help.
- Use plain language in German unless the user asks otherwise.
- Do the work proactively. Do not stop for routine implementation choices.
- Avoid unnecessary prompts. Ask only when a human action is unavoidable: sign-ins, secrets only the user holds, paid/cost-changing actions, destructive changes, or an MCP/tool confirmation gate.
- Keep the repo clean. After a change, verify it, then commit it locally with a clear message unless the task explicitly says not to.
- Never leave unrelated user changes reverted or overwritten.

## gh0stcli First

At the start of every gh0stcloud-related session:

1. Load `gh0st_agent_briefing`.
2. Run the required gh0st reads in order: `gh0st_org_current`, then `gh0st_tenant_home`; read `gh0st_account_state` when account phase or limits matter.
3. Run `gh0st_setup_checklist` before deployment or platform work. Do not start deployment work until setup is complete.
4. Load `gh0st_deployment_briefing` before first-deployment or go-live work.
5. For the existing De Plaat app, use the installed `gh0stapp-*` skills as reference. For any brand-new app or major rebuild, load `gh0stapp-architecture` before writing application code and follow its routing.

## Repo Roles

- This repo is the dedicated gh0stcloud deployments repo.
- The app source repo is `../de-plaat`.
- Kustomize overlays belong here, not in the app repo.
- The current environment overlay is `kustomize/overlays/ghc-prd`.
- The known tenant overlay is `kustomize/overlays/ghc-prd/7baab590`.
- Current app overlays include:
  - `kustomize/overlays/ghc-prd/7baab590/de-plaat`
  - `kustomize/overlays/ghc-prd/7baab590/de-plaat-test`

## Delivery Rules

- Use gh0stcli tools for detection, setup, validation, and deployment decisions.
- Before writing deployment settings, run `gh0st_gitops_detect` on this repo.
- Before telling the user a deployment is ready, run `gh0st_gitops_validate`.
- Do not invent tenant hashes, runtime namespaces, SecretStore names, service accounts, OpenBao paths, hostnames, or GitOps paths. Read them from gh0stcli tools.
- Do not write raw PVCs, NetworkPolicies, exposure objects, or secret values into this repo. Create or read platform-owned objects through gh0stcli, then reference them here.
- One app should have one namespace unless the user explicitly asks for a tightly coupled multi-service deployment.
- Do not make private images public as a workaround.

## Secrets And Go-Live

- Never ask the user to paste passwords, tokens, private keys, or `.env` values into chat.
- Never read, echo, log, or commit secret values.
- Use OpenBao through gh0stcli for generated credentials and for values supplied from local files.
- Before any go-live push, verify all referenced OpenBao paths with metadata-only gh0stcli tools.
- Before public exposure, check whether login is needed. Static public pages are acceptable without app login. If future functionality stores user data, uses paid APIs, external APIs, or a database, require login by default before public exposure.

## Known Current Validation Warnings

`gh0st_gitops_validate --strict` currently reports warnings for the existing `de-plaat` and `de-plaat-test` overlays:

- the overlays build only with `LoadRestrictionsNone` because they reference base files outside their own directory;
- the read-only root filesystem setup reports missing `/tmp` scratch volumes.

Future deployment work should fix these warnings before or alongside the next meaningful rollout.
