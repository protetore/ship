# Shell Script Code Style

The code style used for shell scripts on the company is pretty much the same as used by [Google](https://google.github.io/styleguide/shell.xml)

This code style if for **bash** only!

I'm summarizing the most important ones here.

## File Extensions

Executables should have no extension (strongly preferred) or a .sh extension. Libraries, helpers and utils that will be included in other scripts must have a .sh extension and should not be executable.

```
util/array_util.sh
libs/my_lib.sh
main
```

## SUID/SGID

SUID and SGID are **forbidden** on shell scripts.

## File Headers

Every file must have a top-level comment including a brief overview of its contents. A copyright notice and author information are optional.

```bash
#!/bin/bash
#
# Perform some useful stuff

source libs/my_lib.sh
myLib::someFunction()
```

## Indentation

Indent 4 spaces. No tabs.

## Loops

Put ; do and ; then on the same line as the while, for or if.

```bash
for key in ${keys[@]}; do
    if [[ "${key}" == "banana" ]]; then
        echo "I don't like bananas"
    fi
done
```

## Variable expansion

In order of precedence: Stay consistent with what you find; quote your variables; prefer "${var}" over "$var".

## Command Substitution

Use `$(command)` instead of backticks.

## Test, [ and [[

`[[ ... ]]` is preferred over `[`, `test` and `/usr/bin/[`.

## Functions

- _Unlike Google_, I use camel-case (`function camelCase() {}`).
- Separate libraries with `::`. Parentheses are required after the function name.
- The keyword function is optional, but must be used consistently throughout a project and is **strongly** recommended.
- Brackets should open in the same line as function definition
- Local variables inside functions must be declared using the `local` keyword.
- Put all functions together in the file just below constants. Don't hide executable code between functions.
- Example:

```bash
# Normal function
function process() {

}

# Library function
# array_util.sh
function arrayUtil::inArray() {
    local needle=$1
    local arr=$2
}
```

## Variable/Constant Names

- Variables: Unlike Google, for variable names I use came case (`camelCase=`), but if you want to use snake-case (`snake_case=`), use it consistently throughout a project.
- Constants: All upper-case, words separated by underscore (`MY_CONST=`) and use readonly or declare -r to ensure they're read only.
- Local variables inside functions must be declared using the `local` keyword.

## Quoting

- Always quote strings containing variables, command substitutions, spaces or shell meta characters, unless careful unquoted expansion is required.
- Prefer quoting strings that are "words" (as opposed to command options or path names).
- Never quote literal integers.
- Be aware of the quoting rules for pattern matches in [[.
- Use `"$@"` unless you have a specific reason to use `$*`.

## Code Example

```bash
#!/bin/bash
#
# Do something cool

source libs/array_util.sh

readonly ATTEMPTS=10
readonly ACCEPTED=( 1 5 7 9 23 )

function subProcess() {
    local item=$1
    # do something...
    echo ${item}
}

function process() {
    local item=$1
    local result;
    for i in {1..${ATTEMPTS}}; do
        arrayUtil::inArray "${item}" ${ACCEPTED}
        if [[ $? -eq 0 ]]; then
            result=$(subProcess "${item}")
        fi
    done

    echo ${result}
}

process "${1}"
```