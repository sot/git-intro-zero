***********************************************************
Zero-level introduction to git for single-user development
                    OR
 How to SAVE time using git for busy people who multitask
***********************************************************

Typical scenario:

You have a script that is not a complete throwaway and is either:

- Useful enough to maintain and use again.
- Complicated / long enough that bugs are possible.

Using git can save you time.

Driver #1:

Like many of us, you work in a time-fragmented environment where you
might be in the middle of something and then get pulled away for an hour or for
a week... you come back and ask "what the heck was I doing here??"

Driver #2:

Even when focused on a particular script, you might make a few changes and
suddenly find that something is not working.  Keeping track of each set of
changes helps alot with debugging and development. Being able to revert to
the working version helps alot with sanity and stress.

Driver #3:

Little things like protecting against accidental keystrokes and making it
safe to enable editor auto-save.

Prerequisite
============

Need git installed. On all of our supported platforms (Windows, Mac, linux)
this is the case. Where to find git may vary, but you need to be able to open
a shell or PowerShell and type `git`.
::

  git
  git help -a
  git help everyday
  git <subcommand> --help

Command line version
====================

Let's play with a simple single-user script that is not very pretty.
::

  cd ~/git/git-intro-zero/fss_trend_annual_cmd_line
  code .
  code ../git-intro-zero/README.rst
  ls

Make an empty git repository
::

  git init

What happened?  A .git directory got created locally on this computer, and
that's all!  This just sets up the infrastructure to track files and changes
locally. We could just delete this directory and the git repository would be
wiped out. Nobody else in the world knows you just did this.  :-)
::

  ls .git  # Lots of gobbledy-gook that you can ignore

**Digression : git prompt magic**

NOTICE on my screen the little magic that happened to the prompt:
::

  (ska3-shiny) ➜  fss-git-demo
  (ska3-shiny) ➜  fss-git-demo git:(master)

This is zsh + oh-my-zsh (https://ohmyz.sh/). I highly recommend this
for folks writing code. We can have a tutorial / install fest another
day.  HUGE feature: what branch are you on and has it been modified
(is it "dirty"?).

For Windows, life is a little trickier but there are options:

- https://git-scm.com/book/en/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-PowerShell
- https://stackoverflow.com/questions/1287718

Repository Status
-----------------

First we just want to see what's going on in the repository.
::

  git status

To cover:

- Master branch
- Commits (represents state of repo at a current point NOT a set of changes)
- Tracked and untracked files

**Digression : What is a branch?**

  https://www.atlassian.com/git/tutorials/using-branches

**Single-developer simplification**

If you are just working on your own little script(s), most of the time you
DON'T NEED branches. So just work on the `master` branch.

BUT .. if you are making changes to your script that might leave it in a broken
state and you still need to be able to run the latest "working" version there
are some options:

- Have an official "installed" release version that you put somewhere
- Develop in a branch and keep the master branch as the official working version

For single-developer single-user, a branch is probably easier since often these
analysis scripts end up having a lot of data / untracked files alongside.

Git configuration
-----------------

Before going further it is useful to set up your git configuration. There are a
few required elements and some customizations that can help.
::

  (ska3-shiny) ➜  fss-git-demo git:(master) cat ~/.gitconfig
  [user]
          name = Tom Aldcroft
          email = taldcroft@gmail.com
  [core]
  	editor = nano -w
  [alias]
          br = rev-parse --abbrev-ref HEAD
          st = status -s
          graph = log --graph --pretty=oneline
          cleans = clean -e .vscode -e .idea -e go*.py -e *.ipynb
  [color]
          ui = true
  [gui]
          fontui = -family helvetica -size 14 -weight bold -slant roman -underline 0 -overstrike 0
          fontdiff = -family lucidatypewriter -size 14 -weight normal -slant roman -underline 0 -overstrike 0
  [log]
          decorate = full
          abbrevCommit = true
  [credential]
          helper = cache --timeout=3600
  [push]
          default = simple

Adding files to the repo for tracking
-------------------------------------

Pretty simple, almost...
::

  git add plot_fss_daily_maxes.py
  git add mean_fss.py
  git status

Notice the "dirty" repo marker in my prompt. This is super helpful if you have
10 different repos you are working on at once (yes, this happens!).

**Digression: the git stage**

Git uses a two-step approach to tracking new file or changes to files:

- Add the file or changes to the "stage", i.e. "promote pending changes to the
  git staging area".
- Commit changes that have been staged.

This two-step approach turns out to be very useful for a more complicated
development scenario where you have made a number of changes at once that cover
conceptually different updates.

In software development it is good practice to make small atomic commits that
address a single conceptual change. By using the stage it is easy to commit only
the relevant changes and leave the other ones for a later commit.

Upshot: good to know but for now you don't need to worry about this. Let's just
commit the file!
::

  git commit -a -m "Add script for plotting daily FSS maxes"
    #  -a, --all
    #       Tell the command to automatically stage files that have been added,
    #       modified, or deleted, but new files you have not told Git about are
    #       not affected.
    #   -m <msg>, --message=<msg>
    #       Use the given <msg> as the commit message. If multiple -m options
    #       are given, their values are concatenated as separate paragraphs.

  git status
  git log
  git show

Making changes
--------------

Edit the script to fix pylance errors.
::

  git diff

Pretend I don't like those changes.
::

  git checkout -- plot_fss_daily_maxes.py

Note: ``git checkout`` has *much* more functionality (JC's favorite gripe
about git), so do check out the docs!

Make the edits again.
::

  git diff
  git commit -a -m "Update logic to avoid possibly unbound dat"

About the care to take with commit message -- it all depends. For work that will
be a public and potentially viewed by others, it is a good idea to spend a few
seconds crafting a succint message that describes the changes. Sometimes you
even need to commit without the ``-m <msg>`` flag in order to make a
multi-line commit message.

For your own standalone work, you can get often away with "Some changes".

Ignoring files
--------------

The ``git status`` output has a lot of cruft from the output plots and old
intermediate files. It is not necessary but depending on your OCD level it can
make life easier to tell git to ignore them.

Add to .gitignore::
::

  *~
  *.png
  2018/

Then::

  git status

VS code
=======

Settings
--------

Use Cmd-, or Ctrl-, to get the settings.
::

    "terminal.integrated.inheritEnv": false,
    "terminal.integrated.shell.osx": "/bin/zsh",
    "terminal.integrated.fontSize": 14,
    "window.zoomLevel": 0,
    "keyboard.touchbar.enabled": false,
    "files.autoSave": "afterDelay",
    "files.trimTrailingWhitespace": true,

    "workbench.editor.limit.enabled": true,
    "workbench.editor.limit.value": 8,

    "editor.minimap.enabled": false,
    "editor.fontSize": 14,
    "editor.quickSuggestions": {
        "other": true,
        "comments": false,
        "strings": false
    },
    "editor.quickSuggestionsDelay": 250,
    "editor.acceptSuggestionOnEnter": "off",
    "editor.suggestOnTriggerCharacters": true,
    "editor.parameterHints.enabled": true,
    "editor.columnSelection": false,
    "editor.multiCursorModifier": "alt",
    "editor.suggest.showWords": false,
    "editor.wordBasedSuggestions": false,
    "editor.formatOnSaveMode": "modifications",
    "editor.rulers": [
        80,
        100
    ],

    "python.linting.flake8Enabled": true,
    "python.linting.pylintEnabled": false,
    "python.languageServer": "Pylance",
    "python.analysis.useLibraryCodeForTypes": false,
    "python.dataScience.sendSelectionToInteractiveWindow": true,
    "python.dataScience.askForKernelRestart": false,

    "git.autofetch": true,
    "git.confirmSync": false,
    "git.untrackedChanges": "separate",
    "scm.alwaysShowRepositories": true,
    "diffEditor.renderSideBySide": false,

Extensions
==========

VS code relies on open-source extensions to achieve full potential.
Here are my extensions::

  donjayamanne.githistory
  dracula-theme.theme-dracula
  fttx.language-forth
  Gimly81.fortran
  Gimly81.matlab
  guyskk.language-cython
  hansec.fortran-ls
  johnpapa.vscode-peacock
  karigari.chat
  KevinRose.vsc-python-indent
  krvajalm.linter-gfortran
  lextudio.restructuredtext
  ms-python.python
  ms-python.vscode-pylance
  ms-vscode.cpptools
  ms-vsliveshare.vsliveshare
  ms-vsliveshare.vsliveshare-audio
  ms-vsliveshare.vsliveshare-pack
  npxms.hide-gitignored
  stkb.rewrap
  zhuangtongfa.material-theme

Repeat process using VS code only
=================================
::

  cd ~/git/git-intro-zero/fss_trend_annual_cmd_line
  code .
  code ../git-intro-zero/README.rst

Create repo
-----------

``git init`` by clicking on Source Control

Repo status
-----------

``git status``

- Master branch
- Commits (show also more complicated example using chandra_aca)
- Tracked and untracked files
- Git History extension

Add files for tracking
----------------------

``git add``, ``git commit`` using Source Control

- Add files (the two ``*.py`` files) to the repo using Source Control.
- Check the status again using Explorer, Source Control.
- Check commit history from (``git log``) using Explorer Timeline, Source
  Control using Git History extension.
- See diffs from a commit (``git diff``) using Git History

Making changes
--------------

- Edit and notice indications of changes
- Revert immediately with context click
- See changes in Source Control
- Revert from Source Control
- Stage and commit, OR just commit
- Check commit history from (``git log``) using Explorer Timeline, Source
  Control using Git History extension.

Ignoring files
--------------

Add to .gitignore::
::

  *~
  *.png
