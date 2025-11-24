# Git made simple

This page will guide you through the git basics. It will show you **all** the
commands you'll ever need to use git in Odoo (both R&D and PS Tech).

This guide will voluntarily explain things in a simple, non-technical way.

## PS Tech repositories

1. Clone the repository you want to work on.
```bash
git clone <repository_url>
```
2. Create a new branch for your work.
```bash
git checkout -b <branch_name>
```

## Odoo R&D

## Aliases

You'll type git commands a lot. To save time, you can create aliases for the
most used commands.\
For instance, typing `git co` is much faster than `git checkout`.

Add those lines to your `~/.gitconfig` file:
```bash
[alias]
    co = checkout
    st = status
    br = branch
    cp = cherry-pick
    # For this one, see the "faster pull" section below
    rh = "!f() { git reset --hard @{u}; };f"
```

## Faster pull

When you don't have any local changes, pulling can be made faster by using
`reset --hard`. This will directly set your branch to the remote state, like..
instantly!

Ever got bored waiting for git to pull the 230 new commits on your R&D branch
(e.g `19.0`)? This command will do it instantly.

```bash
git reset --hard @{u}
```

> [!WARNING]
> This can be done on any branch, including development branches, but keep in
> mint that it will discard any local changes you may have, so be careful when
> using it.

## Visual git repository hints

It is absolutely essential to have visual hints about the current git repository
state when working on code.

See my [Visual git repository hints](../shell-tools/git-bash-prompt/README.md)
detailed article.

## Overkill features

To not be overwhelmed by git, those features can be safely ignored as they don't
bring much.

### `--no-ff` for merge commits

Short answer: avoid using merge commits.

> [!TIP]
> See why not using merge commits here [Never Use Merge Commit](../../code-playbook/#never-use-merge-commit).

### `--force-with-lease` when pushing

This option is used to avoid overwriting someone else's work when pushing.\
While it is a good practice in general, it adds complexity without much value in
Odoo.

Simply use `--force` (`-f`).

- Development branch: you can safely assume no one else is working on it. No
  need to be extra careful.
- Production branch: Push forcing should never happen.
- Staging branch: Push forcing should never happen.\
  The only exception is when there is multiple staging branches, when one is
  being pushed on the production branch, other staging branches should be
  force pushed to rebase on top of the new production branch.\
  This is a very touchy operation done with care, `--force-with-lease` won't
  be very helpful here.

[!TIP]
Staging and production branches should be protected and disable push force at
all.

### Avoid specifying remote

When pushing, avoid specifying the remote name and branch name every time.\
Instead, simply set it once the first time you push with the `-u`
(`--set-upstream`) command. It will be used every time you push this branch
later.

Don't:
```bash
git push origin my-branch
# Create new commits
git push origin my-branch
# Create new commits
git push origin my-branch
```
Do:
```bash
git push -u origin my-branch
# Create new commits
git push
# Create new commits
git push
```
