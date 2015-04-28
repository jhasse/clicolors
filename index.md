---
layout: default
title:  Standard for ANSI Colors in Terminals
---

# {{ page.title }}

The current situation to get colored output from most console commands is a
mess.
Some check for the `TERM` environment variable, or look for `ANSICON` on
Windows, or need to be passed a parameter like `--color` or
`-fdiagnostics-color`.

That's why this page tries to create some sort of "standard" which programs
should comply to no matter on which platform they are run.

The idea is to have the following environment variables (which are currently
already used for this exacty reason on some UNIX systems):

 * `CLICOLORS`
   * If set, ANSI colors are supported and should be used when the program isn't
     piped.
 * `CLICOLORS_FORCE`
   * If set, ANSI colors should be enabled no matter what.

If you have ideas or comments please
[create a new issue on GitHub](https://github.com/jhasse/clicolors/issues/new)
or [edit this page](https://github.com/jhasse/clicolors/edit/gh-pages/index.md).

## How to Implement

If you want to check in your program if ANSI colors are supported, it should
like this Python code example:

{% highlight python %}
import os, sys

def has_colors():
    if (("CLICOLORS" in os.environ and sys.stdout.isatty()) or
        "CLICOLORS_FORCE" in os.environ):
        return True
    else:
        return False
{% endhighlight %}

## Windows

Windows users should install [ANSICON](https://github.com/adoxa/ansicon) to
enable colors in `cmd.exe`.

## Bug Reports

This is a list of bug reports on programs which aren't yet acting properly to
`CLICOLORS`:

 * [Waf build sytem](https://github.com/waf-project/waf/issues/1555)
