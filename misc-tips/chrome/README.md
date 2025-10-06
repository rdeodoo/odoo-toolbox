# Chrome tips

## Site search shortcuts

### Explanation

There is a very convenient way in chrome to speed up your daily PR / tasks /
commit / runbot navigation.

It allows you to type a keyword in your URL bar, like `task`, then press the
TAB key, then type the shortcut expected value, like `4544330`, then press
ENTER.\
This will redirect you to `https://www.odoo.com/odoo/project.task/4544330`
without having to go to Odoo.com, find the task, or click on a task then change
the ID.

You can do that for a lot of website you access to daily based on a number or a
search term.

### Setup

Go to settings > search engines page, or directly access
`chrome://settings/searchEngines`.\
Scroll to the `Site search` section and click on `Add`. It will prompt you for a
name, a shortcut and a URL with placeholders.

**Name**: It has no purpose other than helping you figure what it is about when
accessing the settings. Just type the same as what you type in `shortcut`.
**Shortcut**: This is the keyword you'll type in your URL bar to have the custom
search kicks in.
**URL with %s in place of query**: The URL that should be reached, with `%s`
being the placeholder that will be replaced by what you typed.

Example:
![Shortcut Settings](shortcut-settings.png)

### Handful shortcuts

This is my personal shortcuts, some are more RD oriented.

_`Name` is purposely not listed, type what you want in that value, as explained
above._

| Description | Shortcut | URL with %s in place of query |
| - | - | - |
| Based on a **term**, search for it through all existing Odoo.sh repos and land on the results page | `ps` | `https://github.com/search?q=org%3Aodoo-ps%20%s&type=code` |
| Based on a **task ID**, land on its odoo.com form view | `task` | `https://www.odoo.com/web#id=%s&view_type=form&model=project.task&menu_id=` |
| Based on a **commit hash**, land on its github page | `commit` | `https://github.com/odoo/odoo/commit/%s` |
| Search for a **term** directly on runbot. Could be a branch name, a n-gram, a PR number.. Pro-tip: `-ngram` will show you all your branches | `run` | `https://runbot.odoo.com/runbot/r-d-1?search=%s` |
| Based on an Odoo **PR number**, land on the PR | `PR` | `https://github.com/odoo/odoo/pull/%s` |
| Based on a "runbot issue" **ID**, land on its runbot.odoo.com form view | `runbot` | `https://runbot.odoo.com/web/#id=%s&view_type=form&model=runbot.build.error` |
