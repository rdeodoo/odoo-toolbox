# Shell Tools

## Visual git repository hints

You **should** have a tool that turns your bash prompt when you enter a `git`
repository. You should because it will make your life a thousand time easier. It
will save you an absurd amount of time.

This is how it looks like with my configuration, even though there are a lot of
different tools and configs to achieve this:

![Git Bash Prompt](git-bash-prompt.png)

You can find the configuration there: TODO link to gitbashprompt folder

## Auto fetch

That's also a life saving "tool". It will save your the burden of having to
think about regularly fetching to see if you are up to date.

Do you remember wasting time investigating something to then realize it was just
because you were 580 commits behind on the community repository?\
That won't ever happen again.

How it works is that every time you'll press `Enter` in your shell in a `git`
repository folder, it will automatically `fetch` behind the scenes if it did not
fetch in the last 5 minutes.

In my case, this is coming from the `Git Bash Prompt` tool mentioned above.

## Reverse search

Using `CTRL+R`, you'll be prompted to add a term to look for in your past shell
history.\
You can dynamically type letters and the search will be done on the spot. The
further you type, the further results will be trimmed down.

You can then press `CTRL+R` repeatedly to loop through the results.

Press `Enter` once you found what you needed to execute that command.

You can use `CTRL+S` to loop back in the results, in case you went too fast and
went through the line you were looking for.

> [!TIP]
> `CTRL+S` will most likely not do anything by default. You need to execute
> `stty -ixon` for it to work. If it's the case, add it to your `~/.bashrc`
> config.

## Partial history search

Same spirit as `CTRL+R` reverse search. You would type anything in your shell
like `./odoo-bin` and then press `PgUp` or `PgDn` to loop through your previous
commands that starts with `./odoo-bin`. It's convenient to very quickly go back
to a command you are not exactly sure what is was but you now the start of it.

> [!TIP]
> You can mix this with the `CTRL+K` shortcut to quickly remove part of a
> command and start looping around other matches.
> See TODO link to keyboard navigation

### Config:

If not enabled by default, you can add this in your `~/.inputrc`:
```
# ---------------------------------------------------------
# pgup/pgdn
"\e[6~": history-search-forward
"\e[5~": history-search-backward
#----------------------------------------------------------
```

> [!TIP]
> If you modify a shell config file like `~/.inputrc`, `~/.bashrc`,
> `~/.bash_aliases` etc, you'll need to "reload" the configuration by doing
> `source ~/.inputrc` and/or `source ~/.bashrc` or it won't work.

## Bash Aliases

A few useful custom commands I made that can be useful. It should be added in
your `~/.bash_aliases` file.

### `odoo-bin`

To easily start Odoo with or without enterprises, themes etc:

```bash
# Odoo Community
alias oc="~/Documents/git_sources/odoo/odoo-bin --addons-path=~/Documents/git_sources/odoo/addons"
# Odoo Enterprise
alias oe="~/Documents/git_sources/odoo/odoo-bin --addons-path=~/Documents/git_sources/enterprise,~/Documents/git_sources/odoo/addons"
# Odoo Themes
alias ot="~/Documents/git_sources/odoo/odoo-bin --addons-path=~/Documents/git_sources/odoo/addons,~/Documents/git_sources/design-themes"
# Odoo Enterprise + Themes
alias oet="~/Documents/git_sources/odoo/odoo-bin --addons-path=~/Documents/git_sources/enterprise,~/Documents/git_sources/odoo/addons,~/Documents/git_sources/design-themes"
```

You can then just do this to start a DB, using odoo-bin arguments as you would normally:
```bash
oe -d my_db -i some_module --dev=reload
```

### tig

If you are using `tig`, I'd suggest adding this alias so it avoids the `tig`
command to load thousands of commits, which takes forever while you usually just
want the first few ones:

```bash
alias tig="tig --max-count=150"
```

### Copy Database

This method copies a database into a new one (very quickly). It takes care of
copying the filestore as well. You can optionally start Odoo directly after the
copy.

This is especially useful in 2 scenario where I use this command as a
"savepoint", sort of a rollback in time before I break or do something that I
can't revert but I will have to revert.
- Upgrading: you did set up a database in a specific version that you will need
  to upgrade (to test something), and you know you will have to do it multiple
  time because
- Testing code: you need to test your code, but once you test it you can't
  easily retest it as it depends of something you have to recreate.\
  For instance, you have a custo where you select 5 orders and generate a single
  invoice where you merge some lines, splits others, and do other stuff outside
  the invoice itself.\
  The result is not correct, you adapt the code and need to retest it. You now
  need to reconfigure 5 SO, which is taking 15 minutes as it needs to be done in
  a very specific way. And you can't simply cancel the invoice and regenerate it
  for some reason.

  In this case, you just setup your 5 SO, and before clicking on "Create
  Invoice", you run `cpodoo mydb mydbB` and you now have a backup of your DB
  that you can copy the other way around at any time to regenerate the invoice
  in the exact same condition in 5 seconds.

  ```shell
  # setup your DB, like creating the 5 SO you need
  ./odoo-bin -d mydb ..
  # copy the DB
  cpodoo mydb mydbB
  # restart your DB and generate the invoice
  ./odoo-bin -d mydb ..
  # you adapted the code be cause the invoice was wrong
  # now bootstrap again a DB based on the back you made
  cpodoo mydbB mydb
  # retry generating the invoice
  ./odoo-bin -d mydb ..

  # you can repeat this infinitely:
  cpodoo mydbB mydb
  ./odoo-bin -d mydb ..
  ```

```
cpodoo() {
    # Remove $2 then copy $1 to $2
    rm -r ~/.local/share/Odoo/filestore/$2;
    cp -R ~/.local/share/Odoo/filestore/$1 ~/.local/share/Odoo/filestore/$2;
    psql -d $1 -c "SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE pid <> pg_backend_pid() AND datname = '$2' ;" && command dropdb $2 --if-exists && createdb -T $1 $2;
    # add `&& ot -d $2` at the end of the previous line to start Odoo after it was copied
}
```

> [!WARNING]
> If you have an open `psql` connection in one of your shell, don't forget to
> exit it, or you won't be able to copy the DB.

## Git Aliases

You can add shortcuts to the `git` command, so you don't have to repeatedly
write the same long command again and again.

You can do that by adding this in the `~/.gitconfig` file:
```py
[alias]
        co = checkout
        st = status
        br = branch
        cp = cherry-pick
        rh = "!f() { git reset --hard @{u}; };f"
```

The `rh` (reset --hard) command is very convenient to instantly get a branch up
to date instead of doing `git pull` which takes forever. `rh` will get your
branch up to date in half a second, even if you are thousands of commits late.\
Just know that it's a `--hard` reset, so it will "erase" all your pending diff
or commit you have added locally.\
This is especially useful for your community and enterprise repositories where
you most likely always just want to update your branch and ignore what you have
locally.

## Terminator