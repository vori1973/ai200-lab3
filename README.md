# AI-200 Lab 3: Azure Container Registry Tasks

Hands-on examples for building, testing, and publishing container images with Azure Container
Registry (ACR). Each part has its own source folder on the same `main` branch.

| Part | Folder | What it demonstrates |
|---|---|---|
| 3a | `a/` | Quick cloud build with `az acr build`, publishing `webapp:v2` |
| 3b | `b/` | YAML task that builds and pushes `webapp:<run-id>` |
| 3c | `c/` | Build, start nginx, smoke test, then push only on success |
| 3d | `d/` | Same smoke-test gate, publishing both run-ID and `latest` tags |

## Run an Example

Use Bash with the Azure CLI installed, sign in with `az login`, and select the subscription
containing your existing ACR. Run these commands from this repository's root, replacing the
registry name:

```bash
ACR=your-registry-name

# Quick build (Part A)
az acr build --registry "$ACR" --image webapp:v2 ./a

# YAML workflows (run the part you want)
az acr run --registry "$ACR" --file task.yaml ./b
az acr run --registry "$ACR" --file task.yaml ./c
az acr run --registry "$ACR" --file task.yaml ./d
```

ACR builds and pushes the images; no local Docker build or initial image push is required.
Save editor changes before running: these commands upload saved local files, not unsaved edits.
No GitHub push is needed for these local runs.

## Important Distinctions

- A named Git-triggered task builds the files committed to GitHub. Use a context such as
  `https://github.com/<you>/ai200-lab3.git#main:c` or `#main:d`.
- For a Dockerfile task, `--image` defines the output image. For `--file task.yaml`, omit
  `--image`; the YAML defines its build tags and push steps.
- `az acr task create` registers a named task without starting its first build. Use
  `az acr task run` or push a new commit to its watched branch afterward.
- To exercise the smoke-test gate in Part C or D, temporarily use
  `http://webserver:80/doesnotexist`, save, and run again. Expect failure before the push step;
  restore `http://webserver:80` afterward.
- Part D replaces `webapp:latest` only after a passing smoke test and successful pushes.
  Previous run-ID tags remain available. Avoid concurrent tasks publishing the same `latest` tag.
- These examples publish images to ACR only. Updating `latest` does not redeploy an existing
  Container App; Container App deployment belongs to Lab 4.

The full step-by-step guide is `docs/AI200_ACR_ACA_Labs.md` in the parent
`ai-200-playground` project.
