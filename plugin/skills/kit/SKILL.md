---
name: kit
description: SlashClone commands. /kit find <need>, /kit add @owner/kit, /kit stack @owner/stack, /kit save, /kit publish, /kit clone @owner/kit, /kit update, /kit audit, /kit build, /kit breakdown [folder].
argument-hint: "[find|add|stack|save|publish|clone|update|audit|build|breakdown] [args]"
disable-model-invocation: true
---

# /kit

Run the SlashClone subcommand the user asked for: `$ARGUMENTS`

## The CLI

Every file-writing step uses the SlashClone CLI, which verifies signatures and writes Claude Code permission rules. Find it once, in this order:

1. `echo "$SLASHCLONE_CLI"`: if set (for example `node /path/to/slashclone/packages/cli/src/bin.ts` for development), use it.
2. `command -v slashclone`: if found, use `slashclone` (installed with `npm install -g slashclone`).
3. Otherwise, stop and tell the user to install it in their terminal: `npm install -g slashclone` (Node 22 or newer). Do not run `npx slashclone` yourself: a global install pins one reviewed version instead of fetching code on every run.

- If the CLI says "Not logged in", ask the user to run `slashclone login` in their terminal (it opens a browser), then continue.

Below, `kb` means the CLI you found.

## Subcommands

**find `<need>`**: Call the `kit_search` tool from the slashclone MCP server with the need. Show the top 3 as a short list: name, one-line description, installs, whether it is tested, and scan status. If the MCP server is not connected, tell the user to run `/mcp` and authenticate slashclone.

**add `@owner/kit[@version]`**:

1. Run `kb add <ref>`. Without approval it prints what the kit is, how it was verified, its scan and test status, and everything it is allowed to touch, then exits without installing.
2. Show the user that summary in your own words, especially the "This kit:" permissions, and ask "Install it?".
3. Only after a clear yes, run `kb add <ref> --yes`.
4. If the output says the update "asks for new access", ask again specifically about the new lines. Only after a clear yes, run it with `--approve-new-permissions`.
5. If the output has a "Set up its keys" list, walk the user through it: for each key not marked `[set]`, say what it is for and give its "Get it" link. Tell them to set it in their shell profile or their agent's env settings and restart Claude Code. Never ask for a value, never accept one pasted into the chat (if they paste one anyway, tell them to revoke it and make a new one), and never read or print a value. To check one, you may run `test -n "$NAME" && echo set || echo "not set"`, which prints only whether it is set.

**stack `@owner/stack`**: Installs every kit in a stack, in setup order, with one approval.

1. Run `kb stack add <ref>`. Without approval it prints the install order, any hooks first, then each kit's verification, scan, and permissions, and exits without installing.
2. Summarize it for the user (the order, any hooks, and what each kit can touch) and ask "Install all of them?".
3. Only after a clear yes, run `kb stack add <ref> --yes`.
4. Show the env var checklist it prints. It only says whether each name is set; never ask the user to paste a value into the chat. Point them to their shell profile instead.

To create a stack, help the user write `stack.json` (slug, title, description, visibility, `items` in setup order as `{ "kit": "@owner/kit", "version": "^1.0.0" }`, and `env` as `{ "name": "API_TOKEN", "description": "..." }`), then run `kb stack create`.

**save**: Package the skill or workflow you just built together as a kit.

1. Make a folder for it (ask where if unclear) with `SKILL.md` (Agent Skills frontmatter: `name` in lowercase-hyphen form, and a `description` saying what it does and when to use it) and any scripts it needs.
2. Write `kit.json`: name, owner (the user's handle; get it from `kb whoami`), version `0.1.0`, type, description, category, license (ask; MIT is the default), compat `["claude-code"]`, and permissions that declare every MCP tool, shell command, network domain, file path (relative), and env var name it uses. Never put secret values anywhere; use env var names.
3. Run `kb validate <folder>` and fix every failing finding.
4. Ask whether it should be public, unlisted, or private (a new kit is private unless you pass a flag), then run `kb save <folder> --public` (or `--unlisted`, `--private`).

**publish `[folder]`**: Ask who should see it: public (everyone, the usual choice for sharing), unlisted (anyone with the link), or private (only the user). After a clear answer, run `kb publish <folder> --public` (or `--unlisted`, `--private`). Without a flag a new kit stays private. Tell the user what the last line says: the live link, or that only they can see it. If the scan blocks it, show the findings and offer to fix them.

**clone `@owner/kit[@version]` `[--as <name>]`** (`remix` and `fork` are aliases): Clone a kit (make it the user's own to change, with credit to the original, and install it). Run `kb clone <ref>` first: it previews the original kit and exits without creating anything. Confirm with the user, then run it again with `--yes`, which creates the clone and installs it. To edit it in the browser instead, open the kit page and choose Clone.

**update**: Run `kb update --check` and show what is new. For each update the user wants, follow the same approval rules as add (`--yes`, and `--approve-new-permissions` only after explicit approval of the new access).

**audit**: Run `kb audit` and summarize what each installed kit can touch and whether any were modified locally.

**build**: Help the user write `build.json` (slug, entry_type, title, what_it_does, inputs, outputs, category, kits as `@owner/kit`), then run `kb build`.

**breakdown `[folder]`**: Turn a project into its reusable parts. The folder defaults to the current repository.

1. Read the project: its README, CLAUDE.md, `.claude/` (commands, agents, hooks, settings), skills, scripts with clear inputs and outputs, workflow files (n8n, GitHub Actions), and docs that read as checklists or playbooks. Skim; do not read secrets files (`.env*`, keys, credentials).
2. List the parts someone else could reuse, up to ten, one line each: a lowercase-hyphen name, what it does, its kit type (skill, command, subagent, hook, claude-md, n8n workflow), the files it would include, and what it would need to touch. Group parts that work together and suggest a stack for them.
3. Check every part before you list it, and drop or rewrite any that would expose the user or their employer:
   - no secrets, tokens, keys, or env var values (names only);
   - no internal hostnames, URLs, account, project, customer, or database ids;
   - no personal data, names of colleagues or clients, or private business details (prices, plans, roadmaps, contracts);
   - nothing from files the user does not own or has not said they may share.
     Write each part as general guidance that works for anyone, not a copy of this project's files.
4. Ask which parts to package. For each one they pick, follow **save** above (it validates and scans), asking whether it should be public, unlisted, or private.
5. If the project is a whole app others might rebuild together, offer to sketch it as a blueprint (its parts as work packages) and point them to `https://slashclone.dev/blueprints` to create it. Create nothing without their yes.

Anything else: show the list of subcommands above.
