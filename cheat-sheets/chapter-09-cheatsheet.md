# Chapter 9 Cheat Sheet: CI/CD with Git Folders & Asset Bundles

## Core Files & Structure
| Item | Purpose |
|---|---|
| `databricks.yml` | Root config file for a Databricks Asset Bundle |
| `bundle:` | Defines project name/identity |
| `variables:` | Declares reusable variables with optional defaults |
| `targets:` | Per-environment settings (dev/test/prod) |
| `resources:` | Job/pipeline/other resource definitions |

## Bundle CLI Commands
| Command | Effect |
|---|---|
| `databricks bundle init` | Scaffold a new bundle project from a template |
| `databricks bundle validate` | Check YAML syntax & resolve variables; NO deployment |
| `databricks bundle deploy -t <target>` | Deploy/update resources for a target environment |
| `databricks bundle run <resource> -t <target>` | Execute an already-deployed job/pipeline |
| `databricks bundle destroy -t <target>` | Tear down all resources for a target |
| `databricks bundle summary` | Show info about a deployed bundle |

## Target Modes
| Mode | Resource Naming | Use Case |
|---|---|---|
| `development` | Prefixed with deploying user | Isolated per-developer sandboxes |
| `production` | Exact configured name, no prefix | Shared stable environment |

## Variable Parameterization Pattern
```yaml
variables:
  catalog_name:
    default: dev_catalog

targets:
  dev:
    variables:
      catalog_name: dev_catalog
  prod:
    variables:
      catalog_name: prod_catalog

resources:
  jobs:
    my_job:
      tasks:
        - task_key: main
          notebook_task:
            notebook_path: ./notebook.py
          parameters:
            catalog: ${var.catalog_name}
```

## Git Folders vs Asset Bundles
| Aspect | Git Folders (Repos) | Asset Bundles |
|---|---|---|
| Purpose | Source control: clone/branch/pull in workspace | Declarative deploy of jobs/pipelines/config |
| Manages resources? | No | Yes |
| Environment targeting? | No | Yes, via `targets:` |
| Typical use | Development editing, checking out branches | CI/CD deployment automation |

## Branch-to-Target Promotion Pattern
```
feature/* branch  -> deploy -t dev
release branch     -> deploy -t test
main branch         -> deploy -t prod
```
CI pipeline chooses `-t` based on triggering branch — this logic lives in the pipeline, NOT in `databricks.yml`.

## Exam Gotchas
- `bundle validate` does NOT deploy anything — common wrong-answer trap.
- `mode: development` vs `mode: production` — naming prefix behavior is heavily tested.
- Git folders alone CANNOT deploy jobs/pipelines — that requires Asset Bundles.
- Variables are overridden per-target, not duplicated across separate YAML files.
