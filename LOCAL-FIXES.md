# Local Fixes

This fork runs upstream [ring-mqtt](https://github.com/tsightler/ring-mqtt) with a few
changes of its own. The `local-fixes` branch holds them as commits on top of the upstream
commit it was cut from. Every day `.github/workflows/local-fixes.yml` takes upstream's
latest `dev`, re-applies those commits and publishes `ghcr.io/drawmonster/ring-mqtt:local`.

## Changes

- **Reorder buffer settings for daemon sessions.** ringstream holds a video sequence gap
  open while it waits for Ring to resend the missing packet. In daemon mode, the mode
  ring-mqtt uses, that wait always started at 50 ms. Ring's resends were measured at
  80-120 ms, so early losses were given up before the repair arrived, and each one froze
  the stream until the next keyframe. `RING_REORDER_WAIT`, `RING_REORDER_MAX` and
  `RING_REORDER_DEPTH` now set it (for example `250ms` and `1s`). Unset keeps upstream's
  defaults.

## When a change stops applying

The daily run fails, posts to Discord and publishes nothing, so the server keeps its last
good image. Rebase `local-fixes` onto upstream by hand, set `BRANCH_BASE` in the workflow
to the new base commit, and push.
