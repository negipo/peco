peco
======

Simplistic interactive filtering tool

Description
===========

`peco` is based on [percol](https://github.com/mooz/percol). The idea is that percol was darn useful, but I wanted a tool that was a single binary. peco is written in Go, and as of this writing only implements the basic filtering feature (mainly because that's the only thing I use -- you're welcome to send me pull requests to make peco more compatible with percol).

`peco` can be a great tool to filter stuff like logs, process stats, find files, because unlike grep, you can type as you think and look through the current results.

For basic usage, continue down below. For more cool elaborate usage samples, [please see the wiki](https://github.com/peco/peco/wiki/Sample-Usage), and if you have any other tricks you want to share, please add to it!

## Demo

Demos speak more than a thousand words! Here's me looking for a process on my mac. As you can see, you can page through your results, and you can keep changing the query:

![optimized](http://peco.github.io/images/peco-demo-ps.gif)

Here's me trying to figure out which file to open:

![optimized](http://peco.github.io/images/peco-demo-filename.gif)

When you combine tools like zsh, peco, and [ghq](https://github.com/motemen/ghq), you can make managing/moving around your huge dev area a piece of cake! (this example doesn't use zsh functions so you can see what I'm doing)

![optimized](http://peco.github.io/images/peco-demo-ghq.gif)


Features
========

## Incremental Search

Search results are filtered as you type. This is great to drill down to the
line you are looking for

Multiple terms turn the query into an "AND" query:

![optimized](http://peco.github.io/images/peco-demo-multiple-queries.gif)

When you find that line that you want, press enter, and the resulting line
is printed to stdout, which allows you to pipe it to other tools

## Select Multiple Lines

You can select multiple lines! 

![optimized](http://peco.github.io/images/peco-demo-multiple-selection.gif)

## Select Matchers

Different types of matchers are available. Default is case-insensitive matcher, so lines with any case will match. You can toggle between IgnoreCase, CaseSensitive, and RegExp matchers. The RegExp matcher allows you to use any valid regular expression to match lines

![optimized](http://peco.github.io/images/peco-demo-matcher.gif)

## Works on Windows!

I have been told that peco even works on windows :)

Installation
============

### Just want the binary?

Go to the [releases page](https://github.com/peco/peco/releases), find the version you want, and download the zip file. Unpack the zip file, and put the binary to somewhere you want (on UNIX-y systems, /usr/local/bin or the like). Make sure it has execution bits turned on. Yes, it is a single binary! You can put it anywhere you want :)

### Mac OS X / Homebrew

If you're on OS X and want to use homebrew, use my custom tap:

```
brew tap peco/peco
brew install peco
```

![optimized](http://peco.github.io/images/peco-demo-homebrew.gif)

### go get

If you want to go the Go way (install in GOPATH/bin) and just want the command:

```
go get github.com/peco/peco/cmd/peco
```

Command Line Options
====================

### --help

Display a help message

### --version

Display the version of peco

### --query <query>

Specifies the default query to be used upon startup. This is useful for scripts and functions where you can figure out before hand what the most likely query string is.

### --rcfile <filename>

Pass peco a configuration file, which currently must be a JSON file. If unspecified it will try a series of files by default. See `Configuration File` for the actual locationes searched.

### --null

WARNING: EXPERIMENTAL. This feature will probably stay, but the option name may change in the future.

Changes how peco interprets incoming data. When this flag is set, you may insert NUL ('\0') characters in your input. Anything before the NUL character is treated as the string to be displaed by peco and is used for matching against user query. Anything after the NUL character is used as the "result": i.e., when peco is about to exit, it displays this string instead of the original string displayed.

[Here's a simple example of how to use this feature](https://gist.github.com/mattn/3c7a14c1677ecb193acd)

### --no-ignore-case

By default peco starts in case insensitive mode. When this option is specified, peco will start in case sensitive mode. This can be toggled while peco is still in session.

Configuration File
==================

peco by default consults a few locations for the config files.

1. Location specified in --rcfile. If this doesn't exist, peco complains and exits
2. $XDG\_CONFIG\_HOME/config.json
3. $HOME/.config/peco/config.json
4. for each directories listed in $XDG\_CONFIG\_DIRS, $DIR/peco/config.json
5. If all else fails, $HOME/.peco/config.json

Below are configuration sections that you may specify in your config file:

## Keymaps

Example:

```json
{
    "Keymap": {
        "M-v": "peco.SelectPreviousPage",
        "C-v": "peco.SelectNextPage"
    }
}
```

### Available keys

Since v0.1.8, in addition to values below, you may put a `M-` prefix on any 
key item to use Alt/Option key as a mask.

| Name        | Notes |
|-------------|-------|
| C-a ... C-z | Control + whatever character |
| C-1 ... C-8 | Control + 1..8 |
| C-[         ||
| C-]         ||
| C-~         ||
| C-\_        ||
| C-\\\\      | Note that you need to escape the backslash |
| C-/         ||
| Esc         ||
| Tab         ||
| Insert      ||
| Delete      ||
| Home        ||
| End         ||
| Pgup        ||
| Pgdn        ||
| ArrowUp     ||
| ArrowDown   ||
| ArrowLeft   ||
| ArrowRight  ||

### Available actions

| Name | Notes |
|------|-------|
| peco.ForwardChar        | Move caret forward 1 character |
| peco.BackwardChar       | Move caret backward 1 character |
| peco.ForwardWord        | Move caret forward 1 word |
| peco.BackwardWord       | Move caret backward 1 word|
| peco.BeginningOfLine    | Move caret to the beginning of line |
| peco.EndOfLine          | Move caret to the end of line |
| peco.EndOfFile          | Delete one character forward, otherwise exit from peco with failure status |
| peco.DeleteForwardChar  | Delete one character forward |
| peco.DeleteBackwardChar | Delete one character backward |
| peco.DeleteForwardWord  | Delete one word forward |
| peco.DeleteBackwardWord | Delete one word backward |
| peco.KillEndOfLine      | Delete the characters under the cursor until the end of the line |
| peco.DeleteAll          | Delete all entered characters |
| peco.SelectPreviousPage | Jumps to previous page |
| peco.SelectNextPage     | Jumps to next page|
| peco.SelectPrevious     | Selects previous line |
| peco.SelectNext         | Selects next line |
| peco.ToggleSelection    | Selects the current line, and saves it |
| peco.ToggleSelectionAndSelectNext | Selects the current line, saves it, and proceeds to the next line |
| peco.RotateMatcher      | Rotate between matchers (by default, ignore-case/no-ignore-case)|
| peco.Finish             | Exits from peco, with success status |
| peco.Cancel             | Exits from peco, with failure status |

### Default Keymap

Note: If in case below keymap seems wrong, check the source code in [keymap.go](https://github.com/peco/peco/blob/master/keymap.go) (look for NewKeymap).

|Key|Action|
|---|------|
|ESC|handleCancel|
|Enter|handleFinish|
|Ctrl-n|handleSelectNext|
|Ctrl-p|handleSelectPrevious|
|Ctrl-f|handleForwardChar|
|Ctrl-b|handleBackwardChar|
|Ctrl-a|handleBeginningOfLine|
|Ctrl-e|handleEndOfLine|
|Ctrl-d|handleDeleteBackwardWord|
|Ctrl-w|handleKillEndOfLine|
|Ctrl-u|handleKillBeginOfLine|
|Ctrl-k|handleKillEndOfLine|
|Ctrl-space|handleToggleSelectionAndSelectNext|
|ArrorUp|handleSelectPrevious|
|ArrowDown|handleSelectNext|
|ArrowLeft|handleSelectPreviousPage|
|ArrowRight|handleSelectNextPage|
|Backspace|handleDeleteBackwardChar|
|Ctrl-r|handleRotateMatcher|

## Styles

For now, styles of following 3 items can be customized in `config.json`.

```json
{
    "Style": {
        "Basic": ["on_default", "default"],
        "Selected": ["underline", "on_cyan", "black"],
        "Query": ["yellow", "bold"]
    }
}
```

### Foreground Colors

- `"black"` for `termbox.ColorBlack`
- `"red"` for `termbox.ColorRed`
- `"green"` for `termbox.ColorGreen`
- `"yellow"` for `termbox.ColorYellow`
- `"blue"` for `termbox.ColorBlue`
- `"magenta"` for `termbox.ColorMagenta`
- `"cyan"` for `termbox.ColorCyan`
- `"white"` for `termbox.ColorWhite`

### Background Colors

- `"on_black"` for `termbox.ColorBlack`
- `"on_red"` for `termbox.ColorRed`
- `"on_green"` for `termbox.ColorGreen`
- `"on_yellow"` for `termbox.ColorYellow`
- `"on_blue"` for `termbox.ColorBlue`
- `"on_magenta"` for `termbox.ColorMagenta`
- `"on_cyan"` for `termbox.ColorCyan`
- `"on_white"` for `termbox.ColorWhite`

### Attributes

- `"bold"` for `termbox.AttrBold`
- `"underline"` for `termbox.AttrUnderline`
- `"blink"` for `termbox.AttrReverse`

## CustomMatcher

This is an experimental feature. Please note that some details of this specificaiton may change

By default `peco` comes with `IgnoreCase`, `CaseSensitive`, and `Regexp` matchers, but since v0.1.3, it is possible to create your own custom matcher.

The matcher will be executed via  `Command.Run()` as an external process, and it will be passed the query values in the command line, and the original unaltered buffer is passed via `os.Stdin`. Your matcher must perform the matching, and print out to `os.Stdout` matched lines. Note that currently there is no way to specify where in the line the match occurred. Note that the matcher does not need to be a go program. It can be a perl/ruby/python/bash script, or anything else that is executable.

Once you have a matcher, you must specify how the matcher is spawned:

```json
{
    "CustomMatcher": {
        "MyMatcher": [ "/path/to/my-matcher", "$QUERY" ]
    }
}
```

Elements in the `CustomMatcher` section are string keys to array of program arguments. The special token `$QUERY` will be replaced with the unaltered query as the user typed in (i.e. multiple-word queries will be passed as a single string). You may pass in any other arguments in this array.

You may specify as many matchers as you like. 

## Examples

* [An example of a simple perl regexp matcher](https://gist.github.com/mattn/24712964da6e3112251c)
* [C/Migemo](https://github.com/mattn/peco-cmigemo/)

Hacking
=======

First, fork this repo, and get your clone locally.

1. Make sure you have [go 1.x](http://golang.org), with GOPATH appropriately set
2. Run `go get github.com/jessevdk/go-flags`
3. Run `go get github.com/mattn/go-runewidth`
4. Run `go get github.com/nsf/termbox-go`

Note that we have a Godeps file in source tree, for now it's just there for a peace of mind. If you already know about [godep](https://github.com/tools/godep), when you may use that instead of steps 2~4

Then from the root of this repository run:

```
go build cmd/peco/peco.go
```

This will create a `peco` binary in the local directory.

TODO
====

Test it. In doing so, we may change the repo structure

Implement all(?) of the original percol options

AUTHORS
=======

* Daisuke Maki (lestrrat)
* Ryota Arai
* Shinya Ohyanagi
* syohex
* Takashi Kokubun
* cho45
* cubicdaiya
* mattn
* sugyan
* swdyh

Notes
=====

Obviously, kudos to the original percol: https://github.com/mooz/percol
Much code stolen from https://github.com/mattn/gof
