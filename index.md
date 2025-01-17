---
layout: default
title:  Standard for ANSI Colors in Terminals
---

# {{ page.title }}

The current situation to get colored output from most console commands is a
mess.
Some check for the `TERM` environment variable, or look for `ANSICON` on
Windows, or need to be passed a parameter like `--color`, `--ansi` or
`-fdiagnostics-color`.

That's why this page tries to create some sort of "standard" which programs
should comply to no matter on which platform they are run.

The idea is to have the environment variables `NO_COLOR`, `CLICOLOR_FORCE`, and `CLICOLOR`
(which are currently already used for this exact reason on some UNIX systems).
When set, the following rules should apply:

 * `NO_COLOR` set
   * Don't output ANSI color escape codes, see [no-color.org](https://no-color.org)
 * `CLICOLOR_FORCE` set, but `NO_COLOR` unset
   * ANSI colors should be enabled no matter what
 * `CLICOLOR` set, `NO_COLOR` and `CLICOLOR_FORCE` unset
   * ANSI colors are supported and should be used when the program is writing to a
     terminal
 * none of the above environment variables are set
   * ANSI colors are not explicitly requested
   * Older programs newly gaining colors may prefer to disable them by default
   * New programs may prefer to operate as if `CLICOLOR` is set

If you have ideas or comments please
[create a new issue on GitHub](https://github.com/jhasse/clicolors/issues/new)
or [edit this page](https://github.com/jhasse/clicolors/edit/gh-pages/index.md).

## How to Implement

If you want to check in your program if ANSI colors are supported, it should look
like this Python code example:

{% highlight python %}
import os, sys

def has_colors():
    if os.environ.get('NO_COLOR'):
        return False
    elif os.environ.get('CLICOLOR_FORCE'):
        return True
    return sys.stdout.isatty()
{% endhighlight %}

Python code example with `CLICOLOR` for auto-detection:

{% highlight python %}
import os, sys

def has_colors():
    if os.environ.get('NO_COLOR'):
        return False
    elif os.environ.get('CLICOLOR_FORCE'):
        return True
    elif os.environ.get('CLICOLOR'):
        return sys.stdout.isatty()
    else
        return False
{% endhighlight %}

Also consider letting the user override these with command-line options like `--color` and `--color=WHEN` where `WHEN` would be one of:
* `--color` or `--color=always` or `--color=on`: like `CLICOLOR_FORCE`
* `--color=never` or `--color=no`: like `NO_COLOR`, a good default for software newly gaining colors
* `--color=auto`: like `CLICOLOR`, a good default for newer software

## Supported Colors

To get an idea what colors you can use, run the following Python script:

{% highlight python %}
RESET = "\x1b[0m"
print("To reset attributes: \\x1b[0m\n")
for i in range(0, 8):
    print("\x1b[1;3{0}m\\x1b[1;3{0}m{1} \x1b[0;3{0}m\\x1b[0;3{0}m{1} "
          "\x1b[1;4{0};3{0}m\\x1b[1;3{0};4{0}m{1}".format(i, RESET))
{% endhighlight %}

Which should print:

<pre>To reset attributes: \x1b[0m

<span class="bold"><span class="f0">\x1b[1;30m</span></span> <span class="f0">\x1b[0;30m</span> <span class="bold"><span class="f0 b0">\x1b[1;30;40m</span></span>
<span class="bold"><span class="f1">\x1b[1;31m</span></span> <span class="f1">\x1b[0;31m</span> <span class="bold"><span class="f1 b1">\x1b[1;31;41m</span></span>
<span class="bold"><span class="f2">\x1b[1;32m</span></span> <span class="f2">\x1b[0;32m</span> <span class="bold"><span class="f2 b2">\x1b[1;32;42m</span></span>
<span class="bold"><span class="f3">\x1b[1;33m</span></span> <span class="f3">\x1b[0;33m</span> <span class="bold"><span class="f3 b3">\x1b[1;33;43m</span></span>
<span class="bold"><span class="f4">\x1b[1;34m</span></span> <span class="f4">\x1b[0;34m</span> <span class="bold"><span class="f4 b4">\x1b[1;34;44m</span></span>
<span class="bold"><span class="f5">\x1b[1;35m</span></span> <span class="f5">\x1b[0;35m</span> <span class="bold"><span class="f5 b5">\x1b[1;35;45m</span></span>
<span class="bold"><span class="f6">\x1b[1;36m</span></span> <span class="f6">\x1b[0;36m</span> <span class="bold"><span class="f6 b6">\x1b[1;36;46m</span></span>
<span class="bold"><span class="f7">\x1b[1;37m</span></span> <span class="f7">\x1b[0;37m</span> <span class="bold"><span class="f7 b7">\x1b[1;37;47m</span></span>
</pre>

Also see the [ISO 6429 (ANSI) color sequences](http://www.perpetualpc.net/6429_colors.html#color_list).

## Windows

Windows 10 supports ANSI colors [since v1511](https://www.reddit.com/r/Windows10/comments/44czox/windows_10_v1511_adds_support_for_ansi_escape/).
For earlier versions of Windows installing [ANSICON](https://github.com/adoxa/ansicon) will
enable ANSI colors in `cmd.exe`.

## Bug Reports

This is a list of bug reports on the progress of supporting `CLICOLOR_FORCE`:

 * [clang compiler](https://llvm.org/bugs/show_bug.cgi?id=23609)
 * [GCC compiler](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=80271)
 * [IntelliJ](https://youtrack.jetbrains.com/issue/IDEA-205699)
 * [Jenkins ANSI color plugin](https://github.com/dblock/jenkins-ansicolor-plugin/issues/51)
 * [Rust compiler](https://github.com/rust-lang/rust/pull/27867)
 * [supports-color package](https://github.com/chalk/supports-color/issues/32)
 * [phpunit](https://github.com/sebastianbergmann/phpunit/issues/2662)
 * [GitLab CI](https://gitlab.com/gitlab-org/gitlab/issues/28598)

The following bug reports have been resolved:

 * [CMake build system](https://cmake.org/Bug/view.php?id=15789)
 * [Waf build system](https://github.com/waf-project/waf/pull/1928)
 * [ANSICON](https://github.com/adoxa/ansicon/issues/77)
 * [Premake](https://github.com/premake/premake-core/pull/1077)
 * [picocli](https://github.com/remkop/picocli/releases/tag/v3.9.0#3.9.0-ANSI-heuristics)
 * [ninja build system](https://github.com/ninja-build/ninja/commit/bf7107bb864d0383028202e3f4a4228c02302961)
 * [clifm](https://github.com/leo-arch/clifm/commit/057b7c684b25707d5ad5505a382dde2becb3eed0)
 * [Clipboard](https://github.com/Slackadays/Clipboard/commit/8c6da08c93e567318bb268f10db5e95fba41461e)
 * [cppcheck](https://github.com/danmar/cppcheck/pull/6270)
