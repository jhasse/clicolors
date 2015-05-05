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

 * `CLICOLOR`
   * If set, ANSI colors are supported and should be used when the program isn't
     piped.
 * `CLICOLOR_FORCE`
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
    if (("CLICOLOR" in os.environ and sys.stdout.isatty()) or
        "CLICOLOR_FORCE" in os.environ):
        return True
    else:
        return False
{% endhighlight %}

## Supported Colors

To get an idea what colors you can use, run the following Python script:

{% highlight python %}
RESET = "\x1b[0m"
print("To reset attributes: \\x1b[0m\n")
for i in range(0, 8):
    print("\x1b[1;3{0}m\\x1b[1;3{0}m{1} \x1b[0;3{0}m\\x1b[0;3{0}m{1} "
          "\x1b[1;4{0};3{0}m\\x1b[1;3{0};4{0}m{1}".format(i, RESET))
{% endhighlight %}

Also see the [ISO 6429 (ANSI) color sequences](http://www.perpetualpc.net/6429_colors.html#color_list).

## Windows

Windows users should install [ANSICON](https://github.com/adoxa/ansicon) to
enable colors in `cmd.exe`.

## Bug Reports

This is a list of bug reports on the progress of supporting `CLICOLOR`:

 * [Waf build sytem](https://github.com/waf-project/waf/issues/1555)
 * [Jenkins ANSI color plugin](https://github.com/dblock/jenkins-ansicolor-plugin/issues/51)
 * [ANSICON](https://github.com/adoxa/ansicon/issues/77)
