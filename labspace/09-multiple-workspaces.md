# Multiple workspaces

You can mount additional directories into a sandbox alongside the primary workspace.

Use cases might include:

- A shared `libs/` repo the agent can reference
- `docs/` alongside `app/`
- Frontend and backend as separate repos both mounted at once
- Sharing common skills used across all projects

> [!IMPORTANT]
> Workspaces are configured at creation time — you can't add mounts to an existing sandbox. That means we need to recreate `quickstart`.

1. Delete the current sandbox:

    ```bash terminal-id=host
    sbx stop quickstart
    sbx rm quickstart
    ```

2. Recreate it with both workspaces mounted:

    ```bash
    sbx run --name=quickstart $$agent$$ ./backend ./frontend:ro
    ```

    - `./backend` — primary workspace (read/write); agent starts here
    - `./frontend:ro` — mounted read-only; agent can read but not write

    Both appear inside the sandbox at their exact host paths, so relative paths in error
    messages match what you see locally.

3. Run the following command to prove the mount paths and that the frontend is mounted read-only:

    ```console
    ! mount | grep project
    ```

    You should see output similar to the following:

    ```console no-copy-button no-run-button
      ⎿  bind-c5bc2cc3304b8481 on /Users/moby/.labspace/project/backend type virtiofs (rw,relatime)                                 
          bind-53446ec621e39053 on /Users/moby/.labspace/project/frontend type virtiofs (ro,relatime)
     ```

### Cross-repo exercise

With both workspaces mounted, give Claude the following prompt:

```console
The frontend's api.ts sends search requests to GET /issues/search, but the
backend currently returns 501 for that endpoint. (Assume you've already
implemented the backend fix in a previous session.)

Review the frontend search flow in the frontend/src directory and make sure
the error handling is user-friendly when the endpoint isn't available yet.
The frontend is read-only — propose the change but don't apply it.
```

This pattern is common in monorepo setups where you want Claude to understand the full
picture but only write to specific parts.