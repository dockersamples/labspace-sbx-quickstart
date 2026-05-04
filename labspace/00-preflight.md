# SBX Quickstart

This lab will walk you through many of the features and capabilities of Docker Sandboxes.

> [!WARNING]
> The commands in this lab are written for MacOS and Linux environments. Windows support is coming soon.



## Step 1: Set up sbx

Every exercise depends on `sbx` running correctly on your host machine. **Do not skip this section.**

1. Verify sbx is installed by running the following command:

    ```bash
    sbx version
    ```

    Expected output: a version string like `sbx 0.21.0` or similar.

    If it is not installed, follow [these instructions](https://docs.docker.com/ai/sandboxes/get-started/) to install sbx.

2. Ensure you are logged in by running the following command:

    ```bash
    sbx login
    ```

    If you need to login, complete the instructions on the screen to do so.

## Step 2: Set up your agent

This lab works with any mainstream coding agent. Pick the provider whose API key you have — the rest of the lab will adapt.

::variableSetButton[Use OpenAI + Codex]{variables="provider=openai,agent=codex,keyEnv=OPENAI_API_KEY,secretName=openai"}

::variableSetButton[Use Anthropic + Claude]{variables="provider=anthropic,agent=claude,keyEnv=ANTHROPIC_API_KEY,secretName=anthropic"}

::variableSetButton[Use Google + Gemini]{variables="provider=gemini,agent=gemini,keyEnv=GOOGLE_API_KEY,secretName=google"}

&nbsp;

:::conditionalDisplay{variable="provider" requiredValue="openai"}
### OpenAI configuration

You'll run **Codex** inside the sandbox, authenticated to OpenAI.

1. Store your API key by running the following command and providing the secret:

    ```bash no-run-button
    sbx secret set -g openai
    ```

:::

:::conditionalDisplay{variable="provider" requiredValue="anthropic"}
### Anthropic configuration

You'll run **Claude Code** inside the sandbox, authenticated to Anthropic.

1. Store your API key by running the following command and providing the secret:

    ```bash
    sbx secret set -g anthropic
    ```

:::

:::conditionalDisplay{variable="provider" requiredValue="gemini"}
### Gemini configuration

You'll run **Gemini CLI** inside the sandbox, authenticated to Google.

1. Store your API key by running the following command and providing the secret:

    ```bash
    echo "$GOOGLE_API_KEY" | sbx secret set -g google
    ```
:::

2. Verify the secret was stored:

    ```bash no-run-button
    sbx secret ls
    ```

    Expected output: a line matching your chosen provider (`openai`, `anthropic`, or `google`) with the value masked as `****...****`.

> [!IMPORTANT]
> Secrets are stored in your OS keychain and injected at the network proxy layer. The agent never sees the raw API key. This is one of the core governance guarantees in this lab.

## Project setup

This lab is going to use some of the features of Git (namely worktrees). In order to do so, the project needs to be configured as a local git repo.

1. Initialize a repo in the current directory with `git init`:

    ```bash
    git init
    ```

2. Commit the starting project contents into the repo:

    ```bash
    git add .
    git commit -m "Initial commit"
    ```

You're all ready to go! Go ahead and move on to the next module!
