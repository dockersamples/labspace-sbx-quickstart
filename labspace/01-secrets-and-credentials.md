# 🔑 Secrets and credentials

`sbx` has a built-in secrets manager that stores credentials in your OS keychain —
never in plain text on disk or inside the VM. When Claude makes an outbound request
that needs authentication, the host-side proxy intercepts it and injects the credential
automatically. Claude can make authenticated API calls but can never read, log, or
exfiltrate the raw credential.

1. Store your GitHub token **now**, before creating a sandbox. The `-g` flag makes it
global — available to all sandboxes you create:

    ```bash
    echo "$(gh auth token)" | sbx secret set -g github
    ```

    > [!IMPORTANT]
    > Global secrets must be set before a sandbox is created. They are injected at 
    > creation time and cannot be added retroactively to a running sandbox.
    > Sandbox-scoped secrets (without `-g`) can be added at any time and override the
    > global value for that sandbox.

2. Ensure the credential was added with the `sbx secret ls` command:

    ```bash
    sbx secret ls
    ```

    You should see something like: 

    ```
    SCOPE      SERVICE   SECRET
    (global)   github    gho_TK421****...****R2D2
    ```

## Supported services

| Service     | Environment variable(s)                       | API domain(s)                       |
|-------------|-----------------------------------------------|-------------------------------------|
| `anthropic` | `ANTHROPIC_API_KEY`                           | `api.anthropic.com`                 |
| `openai`    | `OPENAI_API_KEY`                              | `api.openai.com`                    |
| `github`    | `GH_TOKEN`, `GITHUB_TOKEN`                    | `api.github.com`, `github.com`      |
| `google`    | `GEMINI_API_KEY`, `GOOGLE_API_KEY`            | `generativelanguage.googleapis.com` |
| `groq`      | `GROQ_API_KEY`                                | `api.groq.com`                      |
| `mistral`   | `MISTRAL_API_KEY`                             | `api.mistral.ai`                    |
| `nebius`    | `NEBIUS_API_KEY`                              | `api.studio.nebius.ai`              |
| `xai`       | `XAI_API_KEY`                                 | `api.x.ai`                          |
| `aws`       | `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`  | AWS Bedrock endpoints               |

For services not in this list, you can write values to `/etc/sandbox-persistent.sh`
inside the sandbox via `sbx exec`. Unlike `sbx secret`, this stores the value inside
the VM where the agent can read it directly — use only for tokens where proxy injection
isn't needed.