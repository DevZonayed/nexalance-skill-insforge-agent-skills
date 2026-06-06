# InsForge CLI Basics

Use this file for CLI-wide behavior: command invocation, global flags, exit codes, environment variables, and connection setup.

## Always Use npx

Run the CLI through `npx`:

```bash
npx @insforge/cli <command>
```

Do not install or call a global `insforge` binary.

## Global Options

| Flag          | Description                                                                                                                                                                                                   |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--json`      | Structured JSON output and skip value-collection prompts such as text/select prompts. Errors if any required value is missing. Combine with `-y` for destructive commands that also ask for Y/N confirmation. |
| `-y`, `--yes` | Auto-accept Y/N confirmation prompts such as delete or overwrite prompts. Does not skip value-collection prompts; use `--json` for that.                                                                      |

## Exit Codes

| Code | Meaning                                                 |
| ---- | ------------------------------------------------------- |
| 0    | Success                                                 |
| 1    | General error, including HTTP 400+ from function invoke |
| 2    | Not authenticated                                       |
| 3    | Project not linked                                      |
| 4    | Resource not found                                      |
| 5    | Permission denied                                       |

## Environment Variables

| Variable                | Description                        |
| ----------------------- | ---------------------------------- |
| `INSFORGE_ACCESS_TOKEN` | Override stored access token       |
| `INSFORGE_PROJECT_ID`   | Override linked project ID         |
| `INSFORGE_EMAIL`        | Email for non-interactive login    |
| `INSFORGE_PASSWORD`     | Password for non-interactive login |

## Connection Setup

Use the current linked project when a project is already linked. Only run setup commands when the task needs project access and the connection state is unknown or broken.

Start with the linked project check:

```bash
npx @insforge/cli current
```

- If `current` shows a linked project, use it.
- If the authenticated identity matters, or if `current` reports an authentication problem, run `npx @insforge/cli whoami`.
- If the CLI is not authenticated, run `npx @insforge/cli login`.
- If using non-interactive email login, provide `INSFORGE_EMAIL` and `INSFORGE_PASSWORD` and run `npx @insforge/cli login --email -y`.
- If no project is linked, run `npx @insforge/cli link` for an existing project, or `npx @insforge/cli create` when the user asked for a new backend.
- For non-interactive project creation, pass `--json` with required values such as `--name`, `--template`, `--org-id`, and `--region`; `-y` alone is not enough for value prompts.
- In prelinked workflows such as CI, local test projects, automation, or explicit user-provided project context, use that project context directly.

## Non-Interactive CI/CD

```bash
INSFORGE_EMAIL=$EMAIL INSFORGE_PASSWORD=$PASSWORD npx @insforge/cli login --email -y
npx @insforge/cli link --project-id $PROJECT_ID --org-id $ORG_ID -y
npx @insforge/cli db query "SELECT 1 AS ok" --json
```
