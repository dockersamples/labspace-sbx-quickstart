# ☎️ Port forwarding with `sbx ports`

Sandboxes are network-isolated — your browser can't reach a server inside one by
default. `sbx ports` punches a hole from a host port to a sandbox port.

## Forward the API

1. With the Docker Compose stack running from the previous section, run the following in another terminal:

    ```bash terminal-id=host
    sbx ports quickstart --publish 8000:8000
    ```

2. Open [http://localhost:8000/docs](http://localhost:8000/docs) to see the live Swagger UI running inside the sandbox.

## Forward the frontend

1. Publish port 3000 from the sandbox to port 3001 on the host to access the frontend:

    ```bash terminal-id=host
    sbx ports quickstart --publish 3001:3000
    ```

2. Open [http://localhost:3001](http://localhost:3001) in your web browser to see the Web front-end.


## Additional commands

1. To view the active ports being published by the sandbox, you can use the `sbx ports` command:

    ```bash terminal-id=host
    sbx ports quickstart
    ```

    Running that will give output similar to the following:

    ```console no-copy-button no-run-button
    HOST IP     HOST PORT   SANDBOX PORT   PROTOCOL
    127.0.0.1   3001        3000           tcp
    127.0.0.1   8000        8000           tcp
    ```

2. To stop port publishing, use the `sbx ports` command with the `--unpublish` flag:

    ```bash terminal-id=host
    sbx ports quickstart --unpublish 8000:8000
    ```

> [!IMPORTANT]
> Services inside the sandbox must bind to `0.0.0.0`, not `127.0.0.1`.
> Most dev servers default to `127.0.0.1` — that's why the prompt
> explicitly asks the agent to bind to `0.0.0.0`.

> [!IMPORTANT]
> Published ports don't survive a sandbox stop/restart. Re-run `sbx ports`
> after restarting.