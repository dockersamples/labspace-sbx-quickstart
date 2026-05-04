# 🐳 Docker inside the sandbox

Each sandbox has its own private Docker daemon. Your agent can run `docker compose up`,
build images, and start containers — none of which appear in your host's `docker ps`.

1. Back in the default terminal, if necessary, reconnect to your sandbox:

    ```bash
    sbx run quickstart
    ```

2. Give your agent the following prompt:

    ```console
    Start the full application stack using Docker Compose.
    Once everything is healthy:
    1. Confirm the backend API is responding at http://localhost:8000/health
    2. Create a test user via the API
    3. Create a project and two issues for that user
    4. Report the final state (user, project, issues) as JSON

    Use the API docs at http://localhost:8000/docs if helpful.
    Make sure all servers bind to 0.0.0.0 so I can reach them via port forwarding.
    ```

    You will see your agent run `docker compose up --build -d`, wait for the `db` healthcheck to pass,
    then hit the API endpoints with `curl` or `httpx`.

3. From inside the agent's interface, you can see the running containers in the sandbox:

    ```bash
    ! docker ps
    ```

    Running that should give you output similar to the following:

    ```console no-copy-button no-run-button
      ⎿  CONTAINER ID   IMAGE                COMMAND                  CREATED              STATUS                        PORTS                                          NAMES                                                                                                 
          a74c644a575f   fix-bugs-frontend    "docker-entrypoint.s…"   About a minute ago   Up About a minute             0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp   fix-bugs-frontend-1                                                                 
          046609163f75   fix-bugs-backend     "uvicorn app.main:ap…"   About a minute ago   Up About a minute             0.0.0.0:8000->8000/tcp, [::]:8000->8000/tcp   fix-bugs-backend-1
          f8c2988853b3   postgres:16-alpine   "docker-entrypoint.s…"   About a minute ago   Up About a minute (healthy)   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp   fix-bugs-db-1
     ```

The containers your agent runs live entirely inside the sandbox. When you `sbx rm` the
sandbox, all images, containers, and Postgres data are deleted automatically.
