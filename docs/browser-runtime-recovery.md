# Browser runtime recovery

C2C connection health, desktop browser control, and ChatGPT page rendering
are separate checks. A green `c2c doctor` does not prove browser control;
an unreadable ChatGPT reply does not prove the browser is disconnected.

## Missing runtime module

Read the installed `control-in-app-browser` skill and use its absolute
`scripts/browser-client.mjs` path. Current runtimes return an agent handle:

```js
const { setupBrowserRuntime } = await import("<installed-plugin>/scripts/browser-client.mjs");
const agent = await setupBrowserRuntime();
const iab = await agent.browsers.get("iab");
nodeRepl.write(await iab.documentation());
```

Do not pin an old cache version, copy an unrelated plugin over Browser, or
assume the runtime injects globals. Reinstall a missing or mismatched Browser
plugin through the desktop app, following its official troubleshooting
guidance. Keep storage preferences and existing compatibility entries; do
not move in-use browser profiles or inspect cookies, tokens, or session stores.

## Installation repeatedly returns to Install

Inspect application installation/synchronization errors before repeating the
installation. If the log explicitly reports that a marketplace is already
added from a different source, inspect its registered source with:

```text
codex plugin marketplace list --json
codex plugin list --marketplace <marketplace-name> --json
```

On Windows, a compatibility path, its actual target, and an extended path
such as `\\?\D:\...` can refer to the same directory but be compared as
different source identities. A manually edited source may make plugins
visible without making registration idempotent.

Only after confirming the intended source and preserving the existing
configuration, use the official CLI to remove the conflicting **marketplace
registration** and add the same verified source again:

```text
codex plugin marketplace remove <marketplace-name> --json
codex plugin marketplace add "<verified-existing-marketplace-root>" --json
codex plugin marketplace add "<verified-existing-marketplace-root>" --json
```

The repeated add should return `alreadyAdded: true`. Removing a marketplace
registration is different from `codex plugin remove`: do not uninstall plugins,
delete caches, or move data as part of this source correction. Preserve a
rollback copy outside the project if it contains machine-specific settings.

Verify the app's subsequent synchronization succeeds and that the relevant
plugins remain installed and enabled. If the running app still uses stale
configuration, reopen it using the normal UI and repeat verification.

## Verify actual control

Use the supported browser API to select the intended browser, read a harmless
page, enter test text, and click a test control. Verify the visible result and
restore any existing draft. Registration or an Installed badge alone is not
acceptance. Keep the original C2C chat and checkpoint while recovering.

For external browsers, follow the official extension troubleshooting flow.
Do not directly run native-host installers, hand-write registration, bypass
browser policy, or replace the selected browser with an unrelated control
backend. Login, CAPTCHA, and explicit consent require the user's action.

If a ChatGPT response cannot load, restore the same page and read its actual
reply before marking C2C DONE. Never count the executor's own checks as the
independent ChatGPT review.
