---
name: clone
description: "/clone, the command the logo names. /clone @owner/kit makes a kit yours to change, with credit, and installs it. /clone <app or idea> finds the blueprint to rebuild an app together, or offers to start one. /clone on its own lists what you cloned."
argument-hint: "[@owner/kit | app or idea]"
disable-model-invocation: true
---

# /clone

Clone is SlashClone's one verb: make something yours to change, with credit to the original. Run what the user asked for: `$ARGUMENTS`

## The CLI

Find it the same way `/kit` does: `$SLASHCLONE_CLI` if set, else `slashclone` if `command -v slashclone` finds it. Otherwise stop and tell the user to run `npm install -g slashclone` in their terminal (Node 22 or newer). Below, `kb` means the CLI you found. If it says "Not logged in", ask the user to run `slashclone login` in their terminal, then continue.

## `/clone @owner/kit[@version]`: clone a kit

1. Run `kb clone <ref>`. Without approval it previews the original kit, how it was verified, and everything it can touch, then exits without creating anything.
2. Show that summary in your own words and ask: "Clone it into your account and install it?" If they want a different name, use `--as <name>`.
3. Ask one optional question: "In one line, what will be better in your clone?" Pass the answer as `--note "<their words>"`. It shows on the clone's page next to what changed from the original. Skip it if they would rather not say yet.
4. Only after a clear yes, run `kb clone <ref> --yes` with any `--as` and `--note`. It creates the clone as a draft with credit and installs it.
5. Tell them where it was installed, that they can change it now, and that `/kit publish` shares it. Its page will show what changed from the original and their note.

## `/clone <app or idea>`: rebuild an app together

Blueprints split an app into parts that people claim and build (https://slashclone.dev/blueprints).

1. Read the public list: `curl -fsS "${SLASHCLONE_API_URL:-https://slashclone.dev}/api/v1/blueprints"`. It is read-only and needs no sign-in. Pick the blueprints whose title or description match what the user named.
2. If one fits, show its title, what it rebuilds, and its open parts, and give its page: `https://slashclone.dev/@<owner>/-/blueprints/<slug>`. Claiming a part happens there, signed in.
3. If none fits, say so and offer to start one: open `https://slashclone.dev/blueprints` to create it, or help them write the parts list first. Never create anything without their yes.
4. Also run the `kit_search` tool from the slashclone MCP server with the idea, in case a single kit already does it, and mention the best match in one line.

## `/clone` on its own: what you cloned

1. Run `kb whoami` for their handle, then `kb audit`.
2. List the installed kits that `kb audit` marks `cloned from @owner/kit@version` (on the line under the kit), with the original's name. Older CLIs do not print that line: then read `forked_from` in the kit's `kit.json` in its install folder.
3. For each, give its page (`https://slashclone.dev/@<handle>/<kit>`), where "What changed from the original" shows the diff. Mention `kb update --check` if the original has a newer version.

## Rules

- Never create, install, or publish without the user's explicit yes.
- Never ask for or print secret values. Kits name the env vars they need; people set their own.
- Say clone, not remix or fork, in everything you write. The CLI still accepts `remix` and `fork` as aliases.
