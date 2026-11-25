<!--
Copyright 2026, Eclipse Foundation
SPDX-License-Identifier: Apache-2.0 WITH SHL-2.1
-->
# Vendored IP

The DivSqrt units in the CVFPU are a patched version of the DivSqrt units from the floating-point units of T-Head's open-source C910 and E906 RISC-V processors.

| Directory   | Upstream                                                          | Used by                     |
|-------------|-------------------------------------------------------------------|-----------------------------|
| `openc910/` | [T-head-Semi/openc910](https://github.com/T-head-Semi/openc910)  | `THMULTI` DivSqrt (`vfdsu`) |
| `opene906/` | [T-head-Semi/opene906](https://github.com/T-head-Semi/opene906)  | `TH32` DivSqrt (`fdsu`)     |

These directories are imported with [`util/vendor.py`](../util/vendor.py), the
[OpenTitan vendoring tool](https://opentitan.org/book/util/doc/vendor.html). For each unit, the
tool reads:

- `vendor/<name>.vendor.hjson`: the upstream repository, the files to exclude, and the patch
  directory.
- `vendor/<name>.lock.hjson`: the upstream commit the vendored code was imported from.
- `vendor/patches/<name>/*.patch`: the local modifications made by the CVFPU.

When it runs, `vendor.py` deletes `vendor/<name>/`, copies in the upstream sources at the
locked commit, and applies the patch files in filename order.

## Why a patch file is needed

The upstream RTL has bugs, and it is missing features that the CVFPU needs, such as FP16ALT
support. Fixes are made in the vendored files, but `vendor.py` overwrites those files with the
upstream sources. A change that exists only in `vendor/<name>/` is therefore lost the next
time the unit is re-vendored, for example to move to a newer upstream revision.

The patch files keep these local changes so that they are applied again on every import. They
also record exactly how the CVFPU's copy differs from upstream.

For this reason, and following OpenTitan's guide
[How to modify vendored code](https://opentitan.org/book/doc/contributing/hw/vendor.html#how-to-modify-vendored-code-fix-a-bug-improve-it),
**every pull request that changes `vendor/openc910/` or `vendor/opene906/` must also add or
update the matching patch in `vendor/patches/<name>/`.**

## Prerequisites

`vendor.py` needs Python 3 with the `hjson` package (`pip install hjson`), and network access
to clone the upstream repository. Run it from the repository root.

## Checking the vendored tree

On a clean checkout, re-vendoring must not change anything:

```sh
./util/vendor.py vendor/openc910.vendor.hjson
./util/vendor.py vendor/opene906.vendor.hjson
git status vendor/
```

If `git status` reports changes, either a vendored file was edited without a patch, or a patch
is wrong.

## Modifying vendored code

The example uses `openc910`. For `opene906`, replace the name everywhere.

1. **Start from a clean tree.** Run the check above.

2. **Make the change** directly in `vendor/openc910/`, and test it.

3. **Commit only the vendored files.** Give the commit a subject that describes the change;
   the patch file takes its author, date and subject from this commit.

   ```sh
   git add vendor/openc910
   git commit -m "THMULTI: <what the change does>"
   ```

4. **Export the commit as a patch.** Use the next free number in `vendor/patches/openc910/`;
   for example, `--start-number 3` when the highest existing patch is `0002-`:

   ```sh
   git format-patch -1 HEAD --relative=vendor/openc910 --no-signature --no-stat \
       --start-number 3 -o vendor/patches/openc910/ -- vendor/openc910
   ```

   Two rules apply to every patch file, and these options take care of both:

   - **Paths are relative to the upstream repository root.** `vendor.py` applies patches
     inside `vendor/openc910/`, so the patch must refer to `a/C910_RTL_FACTORY/...`, not
     `a/vendor/openc910/C910_RTL_FACTORY/...`. A patch made with plain `git diff` or
     `git format-patch` from the the CVFPU root does not apply. `--relative` removes the prefix.
   - **The number sets the application order.** Patches are applied in filename order, so a
     patch whose changes build on an earlier patch must have a higher number.

5. **Check that the patch reproduces the change.** Run the check above again: `git status
   vendor/openc910` must report nothing except the new patch file.

6. **Commit the patch file**, and open a pull request containing both the vendored change and
   the patch.

A later change to code that an existing patch already modifies gets its own new patch with
the next number; do not edit existing patch files by hand.
