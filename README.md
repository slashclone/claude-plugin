# SlashClone for Claude Code

Find, install, save, and share agent building blocks (skills, workflows, and stacks) from [SlashClone](https://slashclone.dev) without leaving Claude Code.

## Install

In Claude Code:

```
/plugin marketplace add slashclone/claude-plugin
/plugin install slashclone@slashclone
```

Then run `/mcp` and sign in to `slashclone` to search kits from chat.

Installing and publishing kits uses the SlashClone CLI, which checks every kit's signature before it writes a file. It needs Node 22 or newer:

```
npm install -g slashclone
slashclone login
```

No Node? Use SlashClone in claude.ai or the Claude desktop app instead: https://slashclone.dev/connect

## What you get

- `/kit`: find, add, save, publish, clone, update, and audit kits.
- `/clone`: make a kit your own to change, with credit to the original.
- A skill that checks SlashClone once before you build something common, and offers once at the end to share what you made. Set `SLASHCLONE_HARVEST=off` to turn that offer off.

Nothing is installed or published without your yes.

## Help

https://slashclone.dev/help, or support@slashclone.dev.

This repository is a copy of the plugin, kept in sync automatically. Please send issues to support@slashclone.dev rather than opening pull requests here.

## License

Apache 2.0. See [LICENSE](LICENSE).
