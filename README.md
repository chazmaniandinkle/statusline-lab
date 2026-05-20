# Statusline Lab

Interactive playground for designing [Claude Code](https://claude.ai/code) status lines. Mock a session, drag widgets between lines, load presets from popular community projects, copy the result as a bash script.

Single self-contained HTML file. No build step, no dependencies, runs offline.

**Live:** open [`index.html`](./index.html) in any browser, or visit the GitHub Pages site once enabled.

## What it does

- **Simulated session JSON** — edit the fields Claude Code passes on stdin (`session_id`, `model.display_name`, `workspace.project_dir`, `transcript_path`, git state, context %, `rate_limits.five_hour.used_percentage`, `rate_limits.seven_day.used_percentage`, cost, cache TTL, ...) and watch the preview update.
- **Drag-and-drop layout editor** — compose one or more lines from a palette of ~30 widgets. Reorder by dragging, remove by clicking ×, add lines as needed.
- **Live terminal preview** — black-background monospace preview with threshold-banded coloring (green/yellow/red) for context, block, and burn rate.
- **Preset library** — one-click load of layouts inspired by:
  - [davidamo9 gist](https://gist.github.com/davidamo9/764415aff29959de21f044dbbfd00cd9) — two-letter model + proportional bar
  - [aihero.dev](https://www.aihero.dev/creating-the-perfect-claude-code-status-line) — context-only minimalist
  - [ccusage](https://ccusage.com/guide/statusline) — burn-rate specialist
  - [Owloops/claude-powerline](https://github.com/Owloops/claude-powerline) — vim-style segments
  - [Haleclipse/CCometixLine](https://github.com/Haleclipse/CCometixLine) — Rust + Nerd Font icons
  - [rz1989s/claude-code-statusline](https://github.com/rz1989s/claude-code-statusline) — multi-line maximalist
  - [sirmalloc/ccstatusline](https://github.com/sirmalloc/ccstatusline) — kitchen-sink ecosystem
  - [Dan Does Code](https://www.dandoescode.com/blog/claude-code-custom-statusline) — worktree-aware
- **Bash codegen** — exports a portable `statusline.sh` skeleton matching your composed layout. Burn-rate / 5h-block / weekly fields are placeholders; wire `ccusage` calls to populate them (the existing community scripts above show how).

## Widget palette

The lab covers every field documented in Claude Code's `statusLine` stdin schema (extracted from the local `claude` binary):

**Identity / session:** `session`, `session_long`, `session_name`, `project`, `project_session`, `cc_version`, `output_style`.
**Model:** full name, 2-letter (`op`/`so`/`ha`), emoji.
**Workspace:** `dir` (tildified), `repo` (`owner/repo`), `repo_host`, `added_dirs`, `ws_git_worktree`.
**Git:** branch, dirty (`*`), ahead/behind.
**Context window:** `ctx_pct`, `ctx_raw`, `ctx_bar`, `ctx_bar_dots`, `ctx_full`, `ctx_input_tokens`, `ctx_output_tokens`, `cache_hit_ratio`.
**Plan limits (rate_limits.*):** `plan_5h`, `plan_5h_reset`, `plan_week`, `plan_week_reset`.
**Agent state:** `effort_level`, `thinking_enabled` (🧠), `vim_mode` (with mode-colored chrome).
**--agent flag:** `agent_name`, `agent_type`.
**PR badge (.pr.*):** `pr_number`, `pr_state` (✓ / … / ✗ / ◌).
**Worktree (.worktree.*):** `worktree`, `worktree_branch`, `worktree_orig_branch`.
**ccusage fallback (peak-relative):** `cost_session`, `cost_today`, `block_pct`, `block_proj`, `block_combined`, `block_remaining`, `weekly`, `weekly_label`, `burn_rate`, `burn_emoji`, `sparkline`, `cache_ttl`.
**Misc:** `time`, `moon`, `mcp_health`.

## Install your own statusline

Once you've designed a layout you like:

1. Click **Copy bash script** in the lab.
2. Save the output as `~/.claude/statusline.sh` and `chmod +x ~/.claude/statusline.sh`.
3. Point `~/.claude/settings.json` at it:

   ```json
   {
     "statusLine": {
       "type": "command",
       "command": "~/.claude/statusline.sh"
     }
   }
   ```

4. For ccusage-derived numbers (burn rate, cost, peak-relative comparisons), see the [ccusage statusline guide](https://ccusage.com/guide/statusline) and graft the relevant `cached_call ... ccusage blocks/weekly --json` blocks into the generated script. Refer to Anthropic's [statusline docs](https://code.claude.com/docs/en/statusline) for the full stdin schema.

### Plan-relative vs peak-relative

There are two different definitions of "5h" and "weekly" floating around:

- **Plan-relative** (`rate_limits.five_hour.used_percentage`, `rate_limits.seven_day.used_percentage`): matches the percentage Anthropic shows in the Claude.ai usage panel and the Claude Code sidebar. Available in stdin JSON starting Claude Code 2.1.80, only after the first API response of the session and only for subscribers. **Use this when you can.**
- **Peak-relative** (`ccusage blocks/weekly`): current period as a percentage of your historical peak. Different denominator, different number. Useful as a fallback or as a "how heavy is this week relative to my norm?" gauge.

The lab generates a script that prefers plan-relative and falls back to ccusage when the harness field is absent.

## Roadmap / gaps

Things the survey turned up as missing across the ecosystem — open territory:

- **MCP tool-call latency** per server, not just connect status
- **Substrate / agent identity** (who is this session bound to)
- **Multi-clock skew** (wall vs body vs mind time)
- **Gradient / animated** color transitions, not static threshold bands
- **ASCII sparklines of token burn** over the session

## Contributing

PRs welcome — especially new widget types, new preset cards from configs you've seen in the wild, and better bash codegen. The whole app lives in `index.html`; no build step.

## License

MIT — see [LICENSE](./LICENSE).
