---
layout: post
title: Vim as a Python IDE
tags:
  - Vim
  - Python
  - IDE
---

A few weeks ago, I replaced Sublime Text and PyCharm with Vim for developing
Python code. Here is a brief overview of why and how I did that.

### Why Vim?

* **Vim is Mature.** Vim came out before I turned 1 and has been under active
  development ever since. The most recent commit (at time of writing) was 3 days
  ago.
* **Vim is FREE.** You won't ever have to buy a license or pay an upgrade fee
  once the next version comes out of beta.
* **Vim has a ton of built in features.** Vim supports every language syntax I've
  ever come across out of the box. Things like egex search/replace, macros and
  multiple clipboards all come standard.
* **Vim is _very_ extensible.** There are more than 4,000 vim scripts/plugins on
  the [Vim Script Index][vim-script-index]. Think of something you want Vim to
  do, Google it, and you will probably find more than one package that can fit
  your needs.
* **Vim works everywhere.** I don't just mean Vim runs on Mac/Linux/Windows. Vim
  runs on [iOS][ios-vim] and [Android][android-vim] too. Vim can run on tiny
  embedded linux systems, like the [Raspberry Pi][raspi]. Since Vim runs inside
  your terminal, it can easily be used on headless server systems. It also works
  _the same way_ in all these scenarios. No more platform specific keyboard
  shortcuts! Most Unix style OSes come with Vim pre-installed, so it's probably
  already on your computer if you want to try it out.
* **Vim is easy to configure.** Your settings are stored in a file called
  `.vimrc` inside your home folder. Key mappings, preferences, plugins, etc. You
  can also create local `.vimrc` files that define setting specific to a single
  project. Since the config files are just text, they can easily be backed up to
  a GitHub repository. Then, restoring your Vim setup is as easy as running `git
  clone`.

### Desired Features
Out of the box, Vim is just an editor. It reads and writes text files. What else
do we need Vim to do before it can replace an IDE? For me, the list of features
I wanted is as follows:

1. Code completion
2. Style/syntax checking (pep8/pylint/pep257/etc)
3. Intelligent go-to-definition and refactoring
4. Integration with test runners and coverage reporters
5. Other stuff like VCS integration, file tree view, fuzzy find file,
search in project...

I'll share some of the Vim plugins that I've found to satisfy these needs.
I won't go into how to install or configure each plugin since these processes
are already very well described elsewhere. I'd reccomend looking into
[Vundle][vundle] for installing plugins, and browsing [my `.vimrc` file][vimrc]
for configuration examples.

### YouCompleteMe
From the project's GitHub page: "YouCompleteMe is a fast, as-you-type,
fuzzy-search code completion engine for Vim". YouCompleteMe (hereafter YCM)
works on Python using the [jedi][jedi] library, as well as the C family of
languages.

Here's a few examples of YCM in action:

After importing the `os` module, YCM offers me all the items under `os` that
start with the letter "p". Selection works like most IDEs where "Tab" will
select the first item in the list and using Up/Down arrows or Tab multiple times
will select others farther down in the list:

![YCM Completion](/assets/images/ycm-module-complete.png)

After selecting the `path` sub module, I can select the `abspath` funciton and
look at it's docs:

![YCM Docs](/assets/images/ycm-function-docs.png)

YCM will also work on file paths, such as the shebang at the top of the file!

![YCM Shebang](/assets/images/ycm-shebang-completion.png)

For languages without a semantic completion engine built in to YCM, completion
suggestions can still be offered based on the contents of the file and recently
typed items.

### Syntastic
[Syntastic][syntastic] is a plugin that lets you integrate external syntax
checkers into Vim. When files are saved in Vim, Syntastic will run your
configured checkers for that filetype and report the results in the gutter
(column next to the line numbers), in the status bar (i.e. Powerline/Airline)
and also in the errors window:

![Syntastic Error Display](/assets/images/syntastic-lint-errors.png)

When an error is selectred in the errors window, Syntastic will place the cursor
on the offending line. The error message for the current line (if there is one)
will also be displayed below the statusbar.

Syntastic works with every Python language checker I use regularly (PEP8,
Flake8, and PEP257) as well as [many others][syntastic-checkers].

### Python Mode
[Python Mode][python-mode] adds a slew of other nice features for working on
Python code such as improved syntax highlighting/folding/indentation,
`virtualenv` support, documentation viewing, goto definition and intelligent
refactoring. Python Mode also includes support for syntax/style checkers,
similar to Syntastic. I leave this turned off in favor of Syntastic because
Syntastic supports other languages besides Python, has more features, and
seemed to work better in general than the Python Mode linting.

Python Mode uses [Rope][rope] for looking up documentation and refactoring
operations. With a single keystroke, I can look at the documentation for
whatever is under my cursor:

![Rope Docs](/assets/images/rope-documentation.png)

With a different keystroke, Rope can refactor the names of
variables/functions/etc. Rope analyzes your code and can present a diff of the
proposed changes which you can then accept or reject:

![Rope Refactor](/assets/images/rope-refactor-diff.png)

Rope also has a code completion engine, but since Rope is primarily a "Python
refactoring library", I disable it in favor of the Jedi completion engine
provided by YCM.

### Test Runners and Coverage Reporting
For showing coverage, I use [Coveragepy.vim][coveragepy.vim]. This works the
same way as the coverage reporting found in the paid version of PyCharm (except
**way** faster). After running your unit tests with coverage reporting,
Coveragepy.vim will read the report file and show red/green marks in the "Sign
column" to indicate where test coverage is lacking.

I like to use [py.test][pytest] as my test runner so use the
[pytest.vim][pytest.vim] plugin. I have a hotkey (`leader-m`) for running the
test case I'm currently working on. The pytest.vim plugin can also run the whole
project's test suite, or just the tests in a class or file. Test failures are
reported in the Quickfix window so you can easily jump to the offending tests
without having to scroll through a long list of traceback in the console.

For Nose users, there is [nose.vim][nose.vim] which adds a Nose compiler
definition to Vim which can be used with other generic test runner plugins such
as [Vim-Makegreen][makegreen].

### The "Other Stuff"
Here's some other plugins I've added that I've found to be helpful:

* File tree view: [NERDTree][nerdtree]
* Fuzzy find files: [Ctrl-P][ctrlp]. This will feel VERY familiar to Sublime
  Text users.
* Git integration: [Fugitive][fugitive]
* Find in project: [Ack.vim][ack.vim]. Provides Vim integration for the Ack
  command line tool (`ag`).
* Statusbar: [Vim-Airline][airline]. Shows the current file, VCS branch, line
  ending mode, etc. Integrates really well with some of the other plugins
  mentioned such as Syntastic and Fugitive.

### Epilogue
It takes a bit of effort to become accustomed to using Vim. I felt a bit
overwhelmed at first and wondered how I would ever remember all these different
keyboard commands. I spent part of a weekend configuring some plugins and
learning a few basic keymappings, then decided that I was going to really try to
learn and use Vim for the next week. After using Vim for a bit, I began to
discover that Vim commands are basically just verbs, nouns, and modifiers. Once
you know a few basic commands, it becomes very easy to remember them and string
them together into more complex commands. For example, "`c`" means change, "`i`"
means inside, so "`ci"`" changes everything inside the current string wrapped
with double-quotes.

Welcome to the world of Vim! Enjoy!

[vim-script-index]: http://www.vim.org/scripts/index.php
[ios-vim]: https://itunes.apple.com/us/app/vim/id492668168
[android-vim]: https://play.google.com/store/apps/details?id=net.momodalo.app.vimtouch&hl=en
[raspi]: http://www.raspberrypi.org
[vundle]: https://github.com/gmarik/Vundle.vim
[ycm]: https://github.com/Valloric/YouCompleteMe
[jedi]: https://github.com/davidhalter/jedi
[syntastic]: https://github.com/scrooloose/syntastic
[syntastic-checkers]: https://github.com/scrooloose/syntastic/wiki/Syntax-Checkers
[python-mode]: https://github.com/klen/python-mode
[rope]: http://rope.sourceforge.net/
[coveragepy.vim]: https://github.com/alfredodeza/coveragepy.vim
[pytest]: http://pytest.org/latest/
[pytest.vim]: https://github.com/alfredodeza/pytest.vim
[nose.vim]: https://github.com/lambdalisue/nose.vim
[makegreen]: https://github.com/reinh/vim-makegreen
[nerdtree]: https://github.com/scrooloose/nerdtree
[ctrlp]: http://kien.github.io/ctrlp.vim/
[fugitive]: https://github.com/tpope/vim-fugitive
[ack.vim]: https://github.com/mileszs/ack.vim
[airline]: https://github.com/bling/vim-airline
[vimrc]: https://github.com/joshfriend/dotvim
