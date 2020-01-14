# erlang_ls

![erlang_ls](images/erlang-ls-logo-small.png?raw=true "Erlang LS")

[![Build Status](https://travis-ci.org/erlang-ls/erlang_ls.svg?branch=master)](https://travis-ci.org/erlang-ls/erlang_ls)
[![Coverage Status](https://coveralls.io/repos/github/erlang-ls/erlang_ls/badge.svg?branch=master)](https://coveralls.io/github/erlang-ls/erlang_ls?branch=master)

An Erlang server implementing Microsoft's Language Server Protocol 3.0.

## Getting in Touch

If you have any questions about the project, feel free to open a new
issue. You can also join the `#language-server` channel in the
_Erlanger_ Slack if you would like to get involved or if you prefer a
more informal mean of communication.

All contributions are welcome, be them in the form of a bug report, a
question, feedback, or code.

A [wiki](https://github.com/erlang-ls/erlang_ls/wiki) is also
available and I occasionally blog about the project on
[Medium](https://medium.com/about-erlang).

## Minimum Requirements

* Erlang OTP 21+
* rebar3 3.9.1+

## Quickstart

Compile the project:

    make

## Command-line Arguments

These are the command-line arguments that can be provided to the
`erlang_ls` escript:

| Argument                | Description                                                                                     |
|-------------------------|-------------------------------------------------------------------------------------------------|
| --transport tcp / stdio | Specifies the transport the server will use for the connection with the client (default: `tcp`) |
| --port PORT             | Used when the transport is `tcp` (default: `10000`)                                             |
| --log-dir DIR           | Directory where logs will be written. When not provided no logs are generated.                  |

### Emacs Setup

The official `lsp-mode` package already includes a client for the
Erlang Language Server, so simply add the following to your `.emacs`
file (assuming the `erlang_ls` executable is available in your `PATH`:

```elisp
;; Install the yasnippet dependency
(package-install 'yasnippet)

;; ----- lsp-mode -----
;; Install the official lsp-mode package (minimum required version 6.2)
(package-install 'lsp-mode)
;; Set path to erlang_ls escript (unless it's in your PATH)
(setq lsp-erlang-server-path "/path/to/erlang_ls")
;; Enable LSP automatically for Erlang files
(add-hook 'erlang-mode-hook #'lsp)

;; ----- lsp-ui -----
;; It is usually a good idea to install lsp-ui as well
(package-install 'lsp-ui)
;; The lsp-ui sideline can become a serious distraction, so you
;; may want to disable it
(setq lsp-ui-sideline-enable nil)
;; Ensure docs are visible
(setq lsp-ui-doc-enable t)

;; ----- company-lsp -----
;; Enables better integration with company (auto-completion)
(package-install 'company-lsp)
(push 'company-lsp company-backends)

;; ----- helm-lsp -----
;; Provides commands to list workspace symbols:
;; - helm-lsp-workspace-symbol
;; - helm-lsp-global-workspace-symbol
(package-install 'helm-lsp)

;; ----- lsp-origami -----
;; Be able to fold ranges via origami.el
(package-install 'lsp-origami)
(add-hook 'origami-mode-hook #'lsp-origami-mode)
(add-hook 'erlang-mode-hook #'origami-mode)
```

Ensure you have Erlang (i.e. `erl`, `escript` and friends) as part
of your Emacs path. If you don't, you can try the following:

```elisp
;; Ensure your Emacs environment looks like your user's shell one
(package-require 'exec-path-from-shell)
(exec-path-from-shell-initialize)
```

To enable logging on the client-side, just:

```elisp
(setq lsp-log-io t)
```

Whenever opening a project for the first time, you will be prompted by
`emacs-lsp` to select the correct project root. In that occasion, you
also have the opportunity to _blacklist_ projects. Information about
projects is stored in a file pointed by the `lsp-session-file`
variable. Its default location is `~/.emacs.d/.lsp-session-v1`. You
may need to prune or amend this file if you change your mind about
blacklisting a project or if you erroneously select a project
root. For more information about the `lsp-session-file` and
`emacs-lsp` in general, please refer to the [official
documentation](https://github.com/emacs-lsp/lsp-mode/blob/master/README.org).

Remember that the Erlang Language Server requires Erlang/OTP 21 or
higher to run, so ensure that OTP 21+ is available in your `PATH`.
This can be achieved, for example, by using the
[exec-path-from-shell](https://github.com/purcell/exec-path-from-shell)
Emacs package.

### VSCode Setup

The Erlang Language Server is available in VSCode via a [dedicated
extension](https://github.com/erlang-ls/vscode).

To try it out, simply open VSCode and install the extension via the
Marketplace:

    Preferences > Extensions

Look for the `erlang-ls` extension and install it. That's it.

Remember that the Erlang Language Server requires Erlang/OTP 21 or
higher to run, so ensure that OTP 21+ is available in your `PATH`.

### IntelliJ Setup

> **WARNING**: The current version of the IntelliJ LSP plugin (1.5.4)
> is quite limited, so not all of the Erlang Language Server
> capabilities are available in IntelliJ.

First of all, ensure you have the [LSP
Support](https://github.com/gtache/intellij-lsp) plugin installed. If
you don't, you can simply navigate to:

    Preferences > Plugins > Browse Repositories

Search for "LSP Support" and install the respective plugin.

Restart IntelliJ, then navigate to:

    Preferences > Languages and Frameworks > Language Server Protocol > Server Definitions

There you can instruct IntelliJ on how to start the server. Select
`Raw Command`, set `erl;hrl` as the extension, then add as the
command:

    /ABSOLUTE/PATH/TO/erlang_ls/_build/debug/bin/erlang_ls --transport stdio

Ensure you use an absolute path. The plugin does not seem to
understand the `~` symbol. For the above command to work, IntelliJ
requires the `PATH` variable to be correctly configured to include
Erlang 20+. To circumvent this issues on Mac OS, the best way is to
start IntelliJ from the terminal (i.e. via the `idea` command) and not
via Spotlight.

To visualize documentation and type specs while hovering a function,
ensure the _Show quick documentation on mouse move_ option is enabled
in your IntelliJ preferences:

    Preferences > Editor > General

There, you can also set a delay in milliseconds.

For more information about how to configure the IntelliJ LSP Client,
please refer to the project [GitHub
page](https://github.com/gtache/intellij-lsp).

#### Troubleshooting the IntelliJ Setup

In some cases, the IntelliJ LSP client may not be able to connect to
the server. In such cases, the first step is to enable logging:

    Preferences > Languages and Frameworks > Language Server Protocol

Check the _Log servers communications_ check-box there.

After restarting IntelliJ, you will notice an extra `lsp` directory
created inside your Erlang project. This directory contains the
`error` and `output` logs, which should give you a hint about what is
going on.

An alternative source of information is represented by the IntelliJ
logs:

    Help > Show Logs

### NeoVim Setup

**Note**: While these instructions are for NeoVim, they should be transferable
to Vim.

For [vim-plug](https://github.com/junegunn/vim-plug) users, install
[Coc](https://github.com/neoclide/coc.nvim) and its plugin
[coc-erlang_ls](https://github.com/hyhugh/coc-erlang_ls) by entering the
following in your `init.vim` or `.vimrc` plugin section:

```vim
" Use release branch (Recommend)
Plug 'neoclide/coc.nvim', {'branch': 'release'}
Plug 'hyhugh/coc-erlang_ls', {'do': 'yarn install --frozen-lockfile'}
```

Then restart vim and run `:PlugInstall`. If the erlang_ls server is running,
then when vim starts you should see the message that Coc has connected to it:

```vim
[coc.nvim] coc-erlang_ls is ready
```

For suggestions on configuring Coc see its [example configuration
documentation](https://github.com/neoclide/coc.nvim#example-vim-configuration).


## Features

This section summarizes the functionalities currently implemented by
the `erlang_ls` server.

### Code Navigation

Code navigation is currently available for the following elements:

* Function applications (both local and remote)
* BIFs
* Behaviour attributes
* Included files
* Type definitions
* Record definitions
* Macro definitions
* Export list entries
* Import list entries

### Code Completion

Completion requests are sent from the client to the server to compute
completion items at a given cursor position. Completion items are
presented to the user for easy selection.

Code completion are currently triggered by the following characters:
`:`, `?`.

Code completion is available for the following elements:

* Exported functions

## Customization

It is possible to customize the behaviour of the `erlang_ls` server
via a configuration file, named `erlang_ls.config`. The
`erlang_ls.config` file should be placed in the root directory of a
given project to store the configuration for that project.

It is also possible to store a system-wide default configuration in an
`erlang_ls.config` file located in the _User Config_ directory. The
exact location of the _User Config_ directory depends on the operating
system used and it can be identified by executing the following
command on an Erlang shell:

    > filename:basedir(user_config, "erlang_ls").

Normally, the location of the _User Config_ directory is:

| Operating System | User Config Directory                               |
|------------------|-----------------------------------------------------|
| Linux            | /home/USER/.config/erlang\_ls                       |
| OS X             | /Users/USER/Library/Application\ Support/erlang\_ls |
| Windows          | c:/Users/USER/AppData/Local/erlang\_ls              |

A sample `erlang_ls.config` file would look like the following:

    otp_path: "/path/to/otp/lib/erlang"
    deps_dirs:
      - "lib/*"
    include_dirs:
      - "include"
      - "_build/default/lib"

The following customizations are possible:

| Parameter          | Description                                                                                          |
|--------------------|------------------------------------------------------------------------------------------------------|
| otp\_path          | Path to the OTP installation                                                                         |
| plt\_path          | Path to the dialyzer PLT file. When none is provided the dialyzer diagnostics will not be available. |
| deps\_dirs         | List of directories containing dependencies. It supports wildcards.                                  |
| apps\_dirs         | List of directories containing project applications. It supports wildcards.                          |
| include\_dirs      | List of directories provided to the compiler as include dirs. It supports wildcards.                 |
| otp\_apps\_exclude | List of OTP applications that will not be indexed (default: megaco, diameter, snmp, wx)              |

## Troubleshooting

It is possible to compile and start the language server in _debug mode_:

    rebar3 as debug escriptize

Ensure you update your `PATH` to include the new executable which now
resides in:

    /path/to/erlang_ls/_build/debug/bin/erlang_ls

Once an instance of the server is running, find the name of the node in
the [logs](#logs) or by running `epmd -names`. It will look something like:

    $ epmd -names
    epmd: up and running on port 4369 with data:
    name erlang_ls_62880311918 at port 50819

And you can connect to it via:

    erl -sname debug -remsh erlang_ls_62880311918@`HOSTNAME`

The [redbug](https://github.com/massemanet/redbug) application is
included in _debug mode_, so feel free to use it.

## The `erlang_ls` Database

The `erlang_ls` language server uses
[Mnesia](https://erlang.org/doc/man/mnesia.html) to persist
information. A new database is created and maintained for each
project/OTP pair. Databases are stored in the application cache
directory, which varies according to the operating system used.

Generally speaking, the directory should be:

| Operating System | Database Dir                                 |
|------------------|----------------------------------------------|
| Linux            | /home/USER/.cache/erlang\_ls                 |
| OS X             | /Users/USER/Library/Caches/erlang\_ls        |
| Windows          | c:/Users/USER/AppData/Local/erlang\_ls/Cache |

You can also run the following command on an Erlang shell to identify
the Database Directory on your system:

    > filename:basedir(user_cache, "erlang_ls").

### Logging

When the escript is built using the `debug` profile as above, logging
will be enabled and the logs will be written to your platform's log
directory (i.e. return value from `filename:basedir(user_log, "erlang_ls")`).
It's possible to change this directory either by modifying the default
value in the `erlang_ls.app.src` file or by having the LSP client
being used to provide a `--log-dir` option.

When the `escript` is built in the `default` mode (i.e. `rebar3 escript`),
no log files are generated, unless the `--log-dir` option is provided.

## References

https://microsoft.github.io/language-server-protocol/

## License

The `erlang_ls` project is licensed under the Apache License 2.0. Please refer to the `LICENSE` file for details.
