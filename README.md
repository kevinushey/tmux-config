# tmux-config

Generate [tmux](https://github.com/tmux/tmux) configuration files from a
single, annotated `.tmux.conf`.

`tmux-config` seeks to ease the pain of maintaining different `.tmux.conf`
files for different versions of tmux, or across different platforms. Using
`tmux-config`, you can annotate a single `.tmux.conf` file with commented
directives that indicate whether a particular chunk of configuration should be
applied in your current environment, and `tmux-config` will do the work of
generating a `.tmux.conf` configuration file tuned for your current
environment, and instruct tmux to load that. See **Examples** below for more
details.


## Installation

The following snippet of code will:

1. Copy a pre-existing `~/.tmux.conf` to `~/.tmux/.tmux.conf`,
1. Write the `tmux-config` loader to `~/.tmux.conf`.

Be sure to back up a pre-existing `~/.tmux.conf` before running this,
just in case!

```
git clone https://github.com/kevinushey/tmux-config
cd tmux-config && ./tmux-config --bootstrap
```


## Documentation

Currently, the following directives are understood by `tmux-config`:

- `# @if [bash-statement]`
- `# @elif [bash-statement]`
- `# @else`
- `# @fi`
- `# @eval [bash-statement]`
- `# @source [bash-script]`

When an `@if` directive is encountered, the following Bash snippet of code is
evaluated, and the return status is used to choose whether any following tmux
configuration is added to the generated file. `@else` switches the condition
from the previously encountered `@if`, and `@fi` closes the `@if` block.

`@eval` is provided as a catch-all mechanism for defining your own Bash
variables for use in later `@if` statements. `@source` is used to source
a Bash script.

`tmux-config` provides the `TMUX_VERSION` environment variable for use in
the `@if` directives.

Generated tmux configuration files are populated (by default) at:

- `~/.tmux/config/`

Folder names are (currently) generated based on a combination of the tmux
version, as well as the output of `$(uname)`. If necessary, you can override
this behavior by setting the `TMUX_CONFIG_OUTPUT` environment variable (e.g. in
your `~/.bashrc`).

## Examples

See [my own .tmux.conf](https://github.com/kevinushey/etc/blob/master/tmux/.tmux.conf)
for a relatively complete example of what can be done with `tmux-config`.

### Snippets

- Use different code for activating mouse selection:

```
# @if [ "${TMUX_VERSION}" \< "2.0" ]
set -gq mode-mouse on
set -gq mouse-resize-pane on
set -gq mouse-select-pane on
set -gq mouse-select-window on
# @else
set -gq mouse on
# @fi
``````

- Use the
[`reattach-to-user-namespace`](https://github.com/ChrisJohnsen/tmux-MacOSX-pasteboard)
workaround on macOS:

```
# @if [ "$(uname)" = "Darwin" ]
set -g default-command "reattach-to-user-namespace -l $SHELL"
# @fi
```


