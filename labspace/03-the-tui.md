# 💻 The interactive TUI dashboard

Running `sbx` with no arguments opens the TUI (Terminal User Interface). 

1. Open the TUI by running the following command outside of the sandbox:

    ```bash
    sbx
    ```

    The dashboard shows all sandboxes as cards with live CPU and memory usage.

The following table displays a few useful keyboard shortcuts:

| Key     | Action                                               |
|---------|------------------------------------------------------|
| `c`     | Create a new sandbox                                 |
| `s`     | Start or stop the selected sandbox                   |
| `Enter` | Attach to the agent session (same as `sbx run`)      |
| `x`     | Open a shell inside the sandbox (`sbx exec`)         |
| `r`     | Remove the selected sandbox                          |
| `Tab`   | Switch between the Sandboxes panel and Network panel |
| `?`     | Show all shortcuts                                   |

The **Network panel** (press `Tab`) shows a live log of every outbound connection the
sandbox makes — which hosts were reached, which were blocked. Use the arrow keys to
navigate the log and allow or block hosts directly. This is the fastest way to debug
"why can't Claude install this package?"

**Press `Ctrl-C` and then `Y`** to exit the dashboard without stopping any sandboxes.

> [!TIP]
> While you can use the keyboard to navigate around the TUI, it also has full mouse support!