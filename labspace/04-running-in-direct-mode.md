# Running in direct mode

This exercise shows Claude working in **direct mode** — the default, where edits land
in your working tree. You'll have Claude run the test suite, identify failures, fix the
bugs, and confirm everything passes.

1. Reconnect to your sandbox:

    ```bash
    sbx run quickstart
    ```

2. Run the existing test suite by giving your agent the following prompt:

    ```console
    Set up the Python environment for the FastAPI backend and run the test suite.
    Report:
    - Which tests pass
    - Which tests fail, with the full error output
    - Your diagnosis of each failure

    Use pytest with verbose output: cd backend && pytest tests/ -v
    ```

    It will take about 3-4 minutes for this to all complete. 

3. Instruct your agent to fix the tests by giving it the following prompt:

    ```console
    Two tests are failing due to a pagination bug. Fix it:

    1. test_pagination_first_page_returns_results — the pagination offset is wrong
    2. test_pagination_second_page — related to the same bug

    For each fix:
    - Explain what the bug is and why it causes the failure
    - Show the diff of your change
    - Re-run the specific test to confirm it passes before moving on
    ```

    While your agent works, you can open the affected files in your editor on the host. 
    You'll see the agent's edits — the workspace mount is bidirectional and instant. No copy
    step, no polling delay.

4. Confirm these tests pass by giving your agent the following prompt:

    ```console
    Run the full test suite and confirm the two updated tests pass (or are intentionally skipped).
    ```

    Note that the `test_updated_at_changes_on_update` is still going to fail, but that'll be fixed in a moment.

> [!IMPORTANT]
> Installed packages **persist** across agent restarts for this sandbox. If you stop
> and reconnect to `quickstart`, you won't need to `pip install` again.
