# Branch mode and parallel agents

## Direct mode vs. branch mode

Everything in the previous section ran in **direct mode** — your agent edited your working tree and
you could see the changes immediately. That's great for interactive work.

**Branch mode** gives your agent its own Git worktree and branch, isolated from your main
working tree. You keep working normally; the agent works on its branch; you review the
diff and merge when you're happy. Use it when you want a clean diff to review before
anything lands, or when running multiple agents simultaneously.

### Single branch: working in isolation

1. If you are currently in the sandbox **press `ctrl-c` twice to exit**

2. Start a new agent session for a new branch of work by adding the `--branch` flag:

    ```bash
    sbx run quickstart --branch=fix-bugs
    ```

    > **Tip**: You don't have to name the branch yourself. `--branch auto` lets `sbx` generate a name for you — handy when you just want isolation without thinking about branch names.

    `sbx` creates a worktree under `.sbx/quickstart-worktrees/fix-bugs` in your repo root.

3. Give your agent the following prompt to fix the last broken test:

    ```console
    One test is still failing after the direct-mode fix. The updated_at field in backend/app/models.py
    never changes on update. Fix the bug and commit with a descriptive message.
    ```

4. Monitor **from a second terminal** without interrupting the session with the `git worktree list` command:

    ```bash terminal-id=host
    git worktree list
    ```

    You should see a new worktree in the project's `.sbx` directory.

### Parallel agents

Because each branch-mode run creates its own worktree, you can run multiple agents
simultaneously with no conflicts. The key is giving each agent its task up front via
a prompt file — this lets both fire off without any interactive back-and-forth.

DevBoard has two unimplemented features that make ideal parallel tasks:

- **Search** — `GET /issues/search?q=` returns 501; the query logic needs to be written
- **Notifications** — `send_status_change_notification()` is a no-op stub; it needs to actually send emails on issue status changes

The `prompts/` directory in this repo includes a ready-made prompt file for each.

1. Start one agent in a new terminal, giving it the prompt to implement search (the specification is provided in the `prompts` directory):

    ```bash terminal-id=parallel-agent-1
    sbx run quickstart --branch=add-search -- "$(cat prompts/implement-search.txt)"
    ```

    > **Note**: the `"$(cat ...)"` must be quoted or the prompt won't be passed correctly
    > to the sandbox.

2. Launch a second agent in another terminal to implement notifications, giving it its specification:

    ```bash terminal-id=parallel-agent-2
    sbx run quickstart --branch=add-notif -- "$(cat prompts/implement-notifications.txt)"
    ```


Both commands run in the same `quickstart` sandbox — no new sandbox is created.
Each gets its own isolated Git worktree under `.sbx/quickstart-worktrees/`, so they
read and write completely separate copies of the code with no conflicts. You'll still
see only one entry in `sbx ls`.

Each agent reads its prompt and gets to work independently. You can watch each agent's progress in its own terminal.
