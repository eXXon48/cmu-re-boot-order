# Boot order

`cmu-re-boot-order` · Revised Store package · Mazda CMU150, firmware 74.00.324A

Reorders the service start so the Home screen, Android Auto and CarPlay come sooner: the update and resource services Android Auto needs start early, Android Auto no longer waits for Bluetooth, MMUI no longer waits for the radio and AUX services, navigation starts after CarPlay, and diagnostics, the display link, USB music, DAB and AUX start after the Home screen. The device manager and Android Auto run on the second CPU core, which has spare time during boot.

## Why

The stock service order is not built for a fast Home screen or a fast phone projection: Android Auto waits for Bluetooth, MMUI waits for the radio and AUX services, navigation starts before CarPlay, and diagnostics, the display link, USB music, DAB and AUX all start in the busiest seconds of the boot. This package moves what Android Auto and CarPlay need forward and what nobody looks at during boot back, and puts the device manager and Android Auto on the second CPU core, which has spare time then.

## What it changes

| File | How | Why |
|---|---|---|
| `/jci/sm/sm.conf` | xml-node | service start reordered for a sooner Home screen, Android Auto and CarPlay |
| `/jci/sm/sm_WCP.conf` | xml-node | service start reordered for a sooner Home screen, Android Auto and CarPlay |

`xml-node` edits address single nodes by XPath; `3way` edits are small diffs against the stock file. The Store merges them with every other installed package, refuses a real clash, and takes them back out on uninstall. No stock file is replaced as a whole.

## Install and uninstall

Install from the Revised Store app on the unit, then restart. Uninstall the same way: after the restart the unit behaves as stock again.

Safety: every change is recorded in the Store's vanilla index, so the boot-loop tripwire reverts it on its own if the unit fails to boot several times in a row. No package edits the boot script above the tripwire line.

## Requires

- `cmu-re-store (>= 0.13.4)`

## Part of

- `cmu-re-speedup-boot`

## Develop

```sh
sh test/run.sh                                  # manifest and payload checks
sh tools/mkpkg.sh <version> <rvp-pack> out.rvp   # build the .rvp
```

Releases: a `dev/vN` tag on `develop` gives a dev prerelease, a `vN` tag on `main` a release; CI packs the `.rvp` and attaches it to the GitHub release.
