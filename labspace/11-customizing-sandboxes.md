# 🔨 Customizing sandboxes

There are a few methods to modify and share the sandbox environment.

## Set a persistent environment variable

Variables set inside the sandbox don't survive a restart. Write them to
`/etc/sandbox-persistent.sh` to make them permanent for the sandbox's lifetime:

```bash terminal-id=host
sbx exec -d quickstart bash -c \
  "echo 'export SMTP_HOST=smtp.mailgun.org' >> /etc/sandbox-persistent.sh"
```

The file is sourced on every login shell, so your agent will see the variable in
subsequent sessions.

## Custom templates and kits

> [!NOTE]
> This section is for reference only. Building custom templates requires Docker Desktop (or another Docker daemon) installed on your host machine — it is not something you do inside the sandbox.

Docker Sandboxes offers two ways to customize a sandbox beyond the built-in defaults:

- Templates — reusable sandbox images with tools, packages, and configuration baked in. Extend a base image with a Dockerfile, or save a running sandbox as a template.
- Kits — declarative YAML artifacts that extend an agent with tools, credentials, network rules, and files at runtime, or define a new agent from scratch.

### Custom templates

Every built-in agent template (`claude-code`, `codex`, `gemini`, etc.) is a plain Docker image. You can extend any of them to pre-bake toolchains, language runtimes, config files, or any other dependencies your project needs. Claude won't have to install them at the start of every session.

1. Start `FROM` an existing sandbox template and layer your additions on top. Switch between `root` (for system packages) and `agent` (for user-level tools) as needed:

    ```dockerfile
    FROM docker/sandbox-templates:claude-code

    USER root
    RUN apt-get update && apt-get install -y protobuf-compiler

    USER agent
    RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
    ```

2. Build and push the custom template image:

    ```bash
    docker build -t my-org/my-template:v1 --push .
    ```

3. Use the template by specifying the `--template` flag when starting a sandbox:

    ```bash
    sbx run --template docker.io/my-org/my-template:v1 claude
    ```

    The image is pulled and cached locally on first use. Subsequent sandbox starts reuse the cached image. If you update the image and want the new version, run `sbx reset` to clear the cache, or use a new tag.

#### Available base images

| Template | Includes |
|----------|----------|
| `docker/sandbox-templates:claude-code` | Claude Code, standard toolchain |
| `docker/sandbox-templates:claude-code-docker` | Claude Code + Docker Engine (required for Docker Compose on Windows) |
| `docker/sandbox-templates:codex` | OpenAI Codex |
| `docker/sandbox-templates:gemini` | Gemini CLI |
| `docker/sandbox-templates:shell` | Bare Bash — no agent pre-installed |

All base images include Ubuntu, Git, GitHub CLI, Node.js, Go, Python 3, and common package managers.

### Kits

[Kits](https://docs.docker.com/ai/sandboxes/customize/kits/) provide the ability to package reusable sets of capabilities for sandboxes. These customizations include:

- Tools to install
- Environment variables to set
- Credentials to inject via the network proxy
- Domains for the network proxy to allow
- Files to add to the sandbox
- Startup commands to run

If you wanted a kit that would add a custom Claude skill to perform Docker reviews and install tools for Python linting, you'd do the following:

1. Create a directory named `custom-kit`:

    ```bash
    mkdir custom-kit
    ```

2. In that directory, create a `spec.yaml` file with the following contents:

    ```yaml save-as=custom-kit/spec.yaml
    schemaVersion: "1"
    kind: mixin
    name: docker-review
    displayName: Dockerfile review skill
    description: Ships a Claude Code skill that reviews Dockerfiles and includes python linting tooling
    commands:
      install:
        - command: "uv tool install ruff@latest"
          user: "1000"
    ```

3. All files in the `files/workspace` directory end in the sandbox. Therefore, create a file named `files/workspace/.claude/skills/docker-review/SKILL.md` with the following contents:

    ```markdown save-as=custom-kit/files/workspace/.claude/skills/docker-review/SKILL.md
    ---
    name: docker-review
    description: Review a Dockerfile for best practices. Use when the user asks to review, audit, or improve a Dockerfile.
    ---

    When reviewing a Dockerfile, check:

    1. Base image - pinned tag or digest, appropriate for the workload
    2. Layer order - dependencies copied before application source
    3. Image size - multi-stage builds, `.dockerignore`, package-manager cache flags
    4. Security - non-root `USER`, no secrets in `ARG`/`ENV`
    5. Reproducibility - pinned package versions, frontend directive where relevant    
    ```

4. Start the sandbox using the local kit:

    ```bash
    sbx run --name quickstart --kit ./custom-kit/ $$agent$$
    ```

5. Once started, validate you see the skill:

    ```bash
    ! find ./.claude -type f
    ```

    and validate Ruff is installed:

    ```bash
    ! ruff --help
    ```

