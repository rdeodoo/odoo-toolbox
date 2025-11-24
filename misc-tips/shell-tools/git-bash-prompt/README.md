# Visual git repository hints

Find here how to install `magicmonty`'s `bash-git-prompt` tool and my own
configuration.

This tool will change the UI of your bash prompt when you enter a `git`
repository.\
It will directly let you know information like the current branch name, the
number of commit you need to pull or to push, the number of pending changes you
have and so on.\
It will make your life a thousand time easier and save you a lot of time.

This is how it looks like with my configuration, even though there are a lot of
different tools and configs to achieve this:

![Git Bash Prompt](../img/git-bash-prompt.png)

I am using `magicmonty`'s tool that you can find here:
https://github.com/magicmonty/bash-git-prompt

You can go through the repository documentation or follow those steps:

1. Clone this repository to your home directory:
    ```bash
    git clone https://github.com/magicmonty/bash-git-prompt.git ~/.bash-git-prompt --depth=1
    ```

2. The tool comes with a few layouts you can choose from, those are find in the
  `/themes` folder.\
  We will create one based on the `Single_line_Ubuntu` one:
    ```bash
    nano ~/.bash-git-prompt/themes/Single_line_Ubuntu_RDE.bgptheme
    ```
    Add this inside this newly created file. It will remove wasted space and
    change a few colors.
    ```bash
    # This is an alternative approach. Single line in git repo.
    override_git_prompt_colors() {
        GIT_PROMPT_THEME_NAME="Single_line_Ubuntu_RDE"

        GIT_PROMPT_PREFIX="["
        GIT_PROMPT_SUFFIX="]"
        GIT_PROMPT_SEPARATOR="|"
        GIT_PROMPT_STAGED="${Red}●${ResetColor}"
        GIT_PROMPT_CONFLICTS="${Red}✖${ResetColor}"
        GIT_PROMPT_CHANGED="${Blue}✚${ResetColor}"
        GIT_PROMPT_UNTRACKED="${Cyan}…${ResetColor}"
        GIT_PROMPT_STASHED="${BoldBlue}⚑${ResetColor}"
        GIT_PROMPT_CLEAN="${BoldGreen}✔${ResetColor}"

        GIT_PROMPT_COMMAND_OK="${Green}✔"
        GIT_PROMPT_COMMAND_FAIL="${Red}✘"

        # GIT_PROMPT_START_USER="_LAST_COMMAND_INDICATOR_ ${White}${Time12a}${ResetColor} ${Cyan}${PathShort}${ResetColor}"
        GIT_PROMPT_START_USER="${Cyan}${PathShort}${ResetColor}"
        GIT_PROMPT_END_USER="${ResetColor}$ "
        GIT_PROMPT_END_ROOT="${BoldRed} #"

        GIT_PROMPT_IGNORE_STASH="1"
        GIT_PROMPT_BRANCH="${BoldBlue}"
        GIT_PROMPT_SYMBOLS_AHEAD="${BoldGreen}↑"
        GIT_PROMPT_SYMBOLS_BEHIND="${BoldRed}↓"
    }

    reload_git_prompt_colors "Single_line_Ubuntu_RDE"
    ```

3. Add this to your `~/.bashrc` file to load the tool and this new theme:
    ```bash
    # GIT PROMPT CUSTO #
    GIT_PROMPT_ONLY_IN_REPO=1
    GIT_PROMPT_THEME=Single_line_Ubuntu_RDE
    source ~/.bash-git-prompt/gitprompt.sh
    ```
