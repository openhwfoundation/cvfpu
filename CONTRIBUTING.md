# Contributing

Thank you for considering contributing to CVFPU!
We very much appreciate contributions such as submitting bug reports and feature requests,
writing code which can be included in the project or improvements to the documentation.

## Contributor Agreement
Most Contributors are [members](https://www.openhwgroup.org/membership/) of the
OpenHW Group and participate in one or more [Technical Task Groups](https://www.openhwgroup.org/working-groups/).
Membership is strongly encouraged, but not required.  Contributors must be
covered by the terms of the [Eclipse Contributor Agreement](https://www.eclipse.org/legal/ECA.php)
(for individuals) **or** the [Eclipse Member Committer and Contributor Agreement](https://www.eclipse.org/legal/committer_process/EclipseMemberCommitterAgreement.pdf)
(for employees of Member companies). The ECA/MCCA provides a legal
framework for a Contributor's technical contributions to the OpenHW Group,
including provisions for grant of copyright license and a Developer
Certificate of Origin on contributions merged into OpenHW Group repositories.
<br><br>
All pull-requests to OpenHW Group git repositories should be signed-off using the
`--signoff` (or `-s`) option to the git commit command (see below), although this is no longer strictly necessary.

In most cases a contribution should be made on a _develop_ branch.<br>

## The Mechanics
1. [Fork](https://help.github.com/articles/fork-a-repo/) the [cvfpu](https://github.com/openhwfoundation/cvfpu) repository
2. Clone repository: `git clone https://github.com/[your_github_username]/cvfpu`
3. Checkout the correct branch reflecting the nature of your contribution.  Nearly all contributions should target a core's _dev_ branch.  Hotfixes can target _master_.
4. Create your feature branch: `git checkout -b <my_branch>.`<br> Please uniquify your branch name.  See the [Git Cheats](https://github.com/openhwfoundation/core-v-verif/blob/master/GitCheats.md) for a useful nominclature.
6. Commit your changes: `git commit -m 'Add some feature' --signoff`
7. Push feature branch: `git push origin <my_branch>`
8. Submit a [pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork).
9. If known, it is advisable to select one or more appropriate reviewers for your PR.

## Repository Structure

This repository loosely follows the [GitFlow Branching Model](https://nvie.com/posts/a-successful-git-branching-model/).

Some points about the workflow:
- `master` branch will be used for versioned releases of the project
- `develop` branch is the base for all development going on
- New features / bugfixes shall create a *feature branch* off `develop` and merge back into it when the feature / fix is complete.
- GitHub pull requests should be used to contribute your work by opening a PR against `develop`.
- Commits should reflect some meaningful step of work and will likely be squashed and rebased before merging if it's for a single feature only.

