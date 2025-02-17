---
sidebar_label: CLI
---

# Nango CLI

:::caution
The CLI is now deprecated in favor of the new Dashboard that ships with all Nango instance. Please use the UI moving forward.

If your use case is not supported with the UI (e.g. programmatic creation of integrations/provider configs) please reach out to us on the [Slack community](https://www.nango.dev/slack). We have a workaround for you.
:::

## Setting up the CLI

The Nango CLI makes use of two environment variables to connect to your Nango instance:

```bash
export NANGO_SECRET_KEY=<SECRET-KEY>
export NANGO_HOSTPORT=<HOST-AND-PORT-OF-NANGO-SERVER>
```

Please refer to the [quickstart](quickstart.md) for the appropriate values for your use case (Nango Cloud, localhost or self-hosted).

We recommend setting these environment variables in your `.bashrc`/`.zshrc` or similar file so they get set for every terminal session.

## CLI Features

For an overview of the CLI commands and what they do run:

```bash
npx nango
```

The CLI also comes bundled with detailed help for each command.

## Debugging CLI issues

The Nango CLI leverages npx, a tool that ships automatically with npm. npx automatically installs the Nango CLI on first run and keeps a cache of it, updating it whenever it detects a new version.

If you run into issues with the CLI the best first step is to clear the npx cache and see if this fixes the problem:

```bash
rm -r ~/.npm/_npx
```

Another common issue is trying to run the Nango CLI from a terminal that is inside a clone of the Nango repo. This will lead to issues, make sure that your working directory is outside of the Nango repository before calling `npx nango`.
