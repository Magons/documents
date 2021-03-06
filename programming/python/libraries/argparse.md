<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [About](#about)
- [Tips and tricks](#tips-and-tricks)
  - [Formatting help width](#formatting-help-width)
  - [Allowing help output to contain line breaks](#allowing-help-output-to-contain-line-breaks)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# About

The argparse module makes it easy to write user-friendly command-line interfaces. The program defines what arguments it requires, and argparse will figure out how to parse those out of sys.argv. The argparse module also automatically generates help and usage messages and issues errors when users give the program invalid arguments.

# Tips and tricks

## Formatting help width

Set the help_position/width values appropriatly to space our long argument text. Avoid setting metavar/dest overrides to control argument output if don't you rely on it's value

```
aparser = argparse.ArgumentParser(description="Basic Python binding test for Hive",
formatter_class=lambda prog: argparse.HelpFormatter(prog,max_help_position=60,width=90))
```

Source: [StackOverflow](https://stackoverflow.com/a/5464440)  
See also [StackOverflow (metavar)](https://stackoverflow.com/questions/16968188/how-do-i-avoid-the-capital-placeholders-in-pythons-argparse-module), [StackOverflow (dest)](https://stackoverflow.com/questions/11999416/python-argparse-metavar-and-action-store-true-together)

## Allowing help output to contain line breaks

Use the `RawTextHelpFormatter` module
```
import from argparse import RawTextHelpFormatter

aparser = argparse.ArgumentParser(description="My program,
formatter_class=lambda prog: argparse.RawTextHelpFormatter(prog,max_help_position=80,width=100))
aparser.add_argument('-dbg', '--debug', nargs='?', const=1, type=int, help="Enable debug output:\n" \
+ "level 0: Clean stdout to screen\n" \
+ "level 1: INFO messages from this script and modules it relies on\n" \
+ "level 2: DEBUG messages from this script and modules it relies on\n" \
, metavar='')
``

# Resources

* https://docs.python.org/3/library/argparse.html
