# Labspace for Docker Sandboxes (sbx)

An interactive lab to learn about Docker Sandboxes - the microVM-based agent environment built by Docker.

<img width="1850" height="979" alt="image" src="https://github.com/user-attachments/assets/86bab658-04de-43d4-8f68-478a1a3f0da8" />


## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [sbx](https://docs.docker.com/ai/sandboxes/)

**Special note:** this lab requires the usage of the Labspace Compose provider, which isn't yet available publicly. Stay tuned though, as it will be coming soon!

## Quick Start

```bash
docker labspace launch dockersamples/labspace-sbx-quickstart
```

Open http://localhost:3030


## Contributing

If you find something wrong or something that needs to be updated, feel free to submit a PR. If you want to make a larger change, feel free to fork the repo into your own repository.

**Important note:** If you fork it, you will need to update the GHA workflow to point to your own Hub repo.

1. Clone this repo

2. Start the Labspace in content development mode:

    ```bash
    # On Mac/Linux
    CONTENT_PATH=$PWD docker compose up --watch

    # On Windows with PowerShell
    $Env:CONTENT_PATH = (Get-Location).Path; docker compose up --watch
    ```

3. Open the Labspace at http://localhost:3030.

4. Make the necessary changes and validate they appear as you expect in the Labspace

    Be sure to check out the [docs](https://github.com/dockersamples/labspace-infra/tree/main/docs) for additional information and guidelines.

5. When done, make sure to run a `docker compose down` to completely tear everything down:

    ```bash
    # On Mac/Linux
    CONTENT_PATH=$PWD docker compose up --watch

    # On Windows with PowerShell
    $Env:CONTENT_PATH = (Get-Location).Path; docker compose up --watch
    ```
