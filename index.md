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

The idea is to have the environment variables `CLICOLOR` and `CLICOLOR_FORCE` (which are currently
already used for this exacty reason on some UNIX systems). When set, the following rules
should apply:

 * `CLICOLOR != 0`
   * ANSI colors are supported and should be used when the program isn't
     piped.
 * `CLICOLOR == 0`
   * Don't output ANSI color escape codes.
 * `CLICOLOR_FORCE != 0`
   * ANSI colors should be enabled no matter what.

If you have ideas or comments please
[create a new issue on GitHub](https://github.com/jhasse/clicolors/issues/new)
or [edit this page](https://github.com/jhasse/clicolors/edit/gh-pages/index.md).

## How to Implement

If you want to check in your program if ANSI colors are supported, it should look
like this Python code example:

{% highlight python %}
import os, sys

def has_colors():
    if ((os.getenv("CLICOLOR", "0") != "0" and sys.stdout.isatty()) or
        os.getenv("CLICOLOR_FORCE", "0") != "0":
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

Windows has its own implementation of colored output. It's possible though
to install [ANSICON](https://github.com/adoxa/ansicon) to enable ANSI colors in `cmd.exe`.

## Bug Reports

This is a list of bug reports on the progress of supporting `CLICOLOR`:

 * [clang compiler](https://llvm.org/bugs/show_bug.cgi?id=23609)
 * [Waf build system](https://github.com/waf-project/waf/issues/1555)
 * [Jenkins ANSI color plugin](https://github.com/dblock/jenkins-ansicolor-plugin/issues/51)
 * [ANSICON](https://github.com/adoxa/ansicon/issues/77)
 * [Rust compiler](https://github.com/rust-lang/rust/pull/27867)
