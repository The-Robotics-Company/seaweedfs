# TRC patches

`trc` (the default branch) is an upstream release tag plus the patches below. It changes only through pull requests: the organization's base ruleset forbids force-pushes to default branches. `master` mirrors upstream and is never changed by hand. Each patch is also on a `fix/<name>` branch cut from upstream `master`, for its upstream PR.

Base: upstream `4.55`.

| Patch | Branch | Upstream PR | Status |
|---|---|---|---|
| filer: rename a directory that holds a hard link on SQL stores | `fix/hardlink-dir-rename` | [#13001](https://github.com/seaweedfs/seaweedfs/pull/13001) | carried |

When a patch is merged upstream, drop it at the next rebase onto a release that contains it and mark it merged here.

## Releases

Tags are `<upstream version>-trc.<n>`, for example `4.55-trc.1`. Pushing one runs `.github/workflows/trc-release.yml`, which builds `weed` as the large-disk Linux amd64 releases from upstream do and publishes, each with its `.sha256`:

| Asset | Build tags | Use |
| --- | --- | --- |
| `linux_amd64_large_disk.tar.gz` | `5BytesOffset` | mounts |
| `linux_amd64_full_large_disk.tar.gz` | `5BytesOffset,elastic,gocdk,rclone,sqlite,tarantool,tikv,ydb` | servers: only this build carries the rclone tier backend |

Each archive holds the `weed` binary at its root, like the upstream archives. The build sets `version.COMMIT` to the tag, so `weed version` prints it, for example `version 8000GB 4.55 4.55-trc.1 linux amd64`.

## Moving to a new upstream release

`.github/workflows/trc-upstream-sync.yml` fast-forwards `master` weekly and opens an issue when upstream publishes a release whose tag is not in this repo yet. Then:

1. `git fetch upstream tag <ver>` and `git push origin <ver>`.
2. On a branch from `trc`, `git merge <ver>`; for each patch upstream merged, keep upstream's version in conflicts, and update the base and table above.
3. Open a pull request into `trc`, merge it, and tag the merge commit `<ver>-trc.1`.
