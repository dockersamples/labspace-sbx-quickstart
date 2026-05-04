# 🔒 Network policies

Every sandbox routes outbound HTTP/HTTPS through a host-side proxy that enforces
access rules you define. There are three built-in postures:

| Policy      | Description                                                                          |
|-------------|--------------------------------------------------------------------------------------|
| Open        | All traffic allowed — no restrictions                                                |
| Balanced    | Default deny, with a broad allow-list covering AI APIs, npm, pip, GitHub, registries |
| Locked Down | Everything blocked; you explicitly allow what you need                               |

## Inspect current rules

To view the existing rules, use the `sbx policy ls` command:

```bash terminal-id=host
sbx policy ls
```

You will see a long list of allowed websites. 

## See what the sandbox is actually hitting

To view a list of the hosts that were allowed or blocked by the proxy, use the `sbx policy log` command:

```bash terminal-id=host
sbx policy log quickstart
```

You should see output similar to the following:

```console no-copy-button no-run-button
Blocked requests:
SANDBOX      TYPE      HOST                       PROXY         RULE                                                                                                  REASON                                  LAST SEEN         COUNT
quickstart   network   telemetry.nextjs.org:443   transparent   no applicable policies for op(action=net:connect:tcp, resource=net:domain:telemetry.nextjs.org:443)   No matching allow rule (default deny)   16:11:06 30-Apr   2

Allowed requests:
SANDBOX      TYPE      HOST                                                                               PROXY            RULE             REASON   LAST SEEN         COUNT
quickstart   network   api.anthropic.com:443                                                              forward          domain-allowed            16:18:46 30-Apr   11
quickstart   network   registry.yarnpkg.com:443                                                           transparent      domain-allowed            16:11:04 30-Apr   1
...
```

Adding the `--json` flag will output the log details in a machine-readable JSON format.

## Allow additional hosts

As you can see, the list of allowed hosts is small to begin with. However, it is easy to modify the list.

1. In your agent terminal, ask it to fetch the contents of example.com.

    ```bash
    ! curl example.com
    ```

    You should see output suggesting it was blocked:

    ```console no-copy-button no-run-button
    Blocked by network policy: domain example.com:80                                                                                  
       detail: no matching allow rule — blocked by default deny policy
    ```

2. Use the `sbx policy allow network` command to allow communication with example.com:

    ```bash terminal-id=host
    sbx policy allow network example.com
    ```

3. Try the request to get example.com again:

    ```bash
    ! curl example.com
    ```

    It should work now!

> [!TIP]
> You can allow multiple hosts in a single command or even use wildcards
>
> ```bash no-copy-button no-run-button
> # Allow multiple at once
> sbx policy allow network "smtp.mailgun.org,api.sendgrid.com"
>
> # Allow all npm and PyPI (useful if Claude can't install packages)
> sbx policy allow network "*.npmjs.org,*.pypi.org,files.pythonhosted.org"
> ```

## Block a host

To specifically block a host, use the `sbx policy deny` command:

```bash terminal-id=host
sbx policy deny network ads.example.com
```

## Removing custom policies

Each policy statement you define manually is stored as a separate policy statement. To remove them, use the `sbx policy rm` command:

1. Remove the allow rule for example.com with the following command:

    ```bash terminal-id=host
    sbx policy rm network --resource=example.com
    ```

2. Remove the deny rule for ads.example.com with the following command:

    ```bash terminal-id=host
    sbx policy rm network --resource=ads.example.com
    ```


## Reaching host services from inside the sandbox

If you have a service running on your host machine (e.g. a local Ollama instance or a database), you can't reach it via `localhost` from inside the sandbox, as it resolves to the VM itself. 

Use `host.docker.internal` instead, and add a policy rule if needed:

```bash
sbx policy allow network localhost:11434
```

Then inside the sandbox, point your client at `host.docker.internal:11434`.
