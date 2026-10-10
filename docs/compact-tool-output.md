# Compact tool output (ChessHeist fork)

**Purpose:** reduce ChatGPT conversation payload per CatDesk call **without reducing the number of calls**.

Select **Compact** in CatDesk's existing `Show Detail` settings (after installing a build containing this branch). This does not alter execution, polling cadence, command outputs or browser actions.

For large text/JSON MCP results (above approximately 1.5 KB serialized), CatDesk first saves the **entire original MCP result** in a locally private JSON file under `%LOCALAPPDATA%\CatDesk\tool-output\<uuid>.json`. It then returns a small, structured status, bounded message/command excerpts, progress cursor/exit status where available, and the `fullOutputFile` path. Other clients may read that file locally via commands if they need exact source context. Small results remain unchanged. Image/audio/resource MCP results and screenshots are never rewritten.

If full-result logging cannot succeed, compaction **does not discard** the original response. The path is local only; never upload these logs to GitHub or publish them externally, because tool output can contain sensitive data.

Limitations: Compact controls MCP result payload size, not ChatGPT's own conversation-storage behavior, and does not itself guarantee lower RAM usage. A `read` response may contain only filenames/metadata in compact form: obtain file excerpts or read the local full-result log when actual contents are required. Logs are not automatically rotated yet; archive/purge them under an explicit local retention policy. Existing `Expanded`, `Collapsed` and `Disable` modes retain their old semantics.

Verification: `cargo test --locked compact_output_tests`; GitHub Actions workflow `compact-output-ci.yml`. On Windows without MSVC `link.exe`, compilation requires the hosted Windows CI runner or a properly installed Visual Studio C++ toolchain. Do not replace a running CatDesk executable before CI/build verification.
