# Debugging sandboxes

The `sbx exec` command opens a shell (or runs a one-off command) inside a running sandbox.

1. Start a new shell inside the quickstart sandbox with the following command in a host terminal:

    ```bash terminal-id=host
    sbx exec -it quickstart bash
    ```

2. From inside this new shell, you can inspect the environment your agent is working in:

    ```bash no-copy-button no-run-button
    docker ps                                          # what containers are running?
    pip list | grep fastapi                            # what's installed?
    curl -s http://localhost:8000/health | python3 -m json.tool   # is the API up?
    ```

3. Type `exit` to leave. The agent session keeps running.

## Run a one-off command without opening a shell

You can also specify a single command to run for one-off commands:

```bash terminal-id=host
sbx exec -it quickstart bash -c "cd backend && pytest tests/ -v --tb=short"
```
