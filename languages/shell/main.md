# MANIFEST
- It isn't a tutorial.
- It covers portability problems between shells.
- It covers some advanced features (that I always forgets).

We can find POSIX standard about shell
[here](https://pubs.opengroup.org/onlinepubs/9799919799/) (top-left corner
"Shell & Utilities" -> bottom-left corner "Shell Command Language").

## `ECHO`
`echo` is used to print ONE LINE. Don't use escape sequences. They work
differently between shells:
```sh
echo "one line"
# one line
```

```sh
echo 'a\nb'
# bash: a\nb
# dash or zsh: a
# dash or zsh: b
```

To work with escape sequences use `printf`:
```sh
printf 'a\nb\n'
# a
# b
```


## `:`
It's a cool thing to do nothing like `true`. It can be used for variable substitution.
```sh
# Does nothing
:
: a b c

# If VAR isn't set or is empty, it will be set to "some data here".
: ${VAR:=some data here}
```


## `IFS`
`IFS` is the input field separator variable. It's used to split or join
fields. It's used in different places like the `read` command, variable
expansions, command substitutions and `$*`/`"$*"`/`$@` expansions.

```sh
IFS=:
V="a:b c:d"
: $V
# dash or bash:
# "a:b c:d" -> "a" "b c" "d"

# zsh:
# "a:b c:d" -> "a:b c:d"
```

`zsh` doesn't split words by `IFS` when it's expanding variables by default. I
wouldn't recomend to relying on it, but if you want this behaviour, you can
enable it in a portable way:
```sh
if [ $ZSH_VERSION ]; then
  setopt sh_word_split
fi
```

To reset `IFS` in a portable way:
```sh
IFS="$(printf " \t\n"; printf x)"; IFS=${IFS%x}
# or
IFS="$(printf ' \t')
"
# or
# This doesn't restore the default value ' \t\n', but everything works the same way.
unset IFS


# Alternatives (won't work):

# Doesn't work in dash.
IFS=$' \t\n'

# Doesn't preserve the trailing newline
# because of the POSIX command substitution.
IFS="$(printf ' \t\n')"
```


## SCRIPT / FUNCTION ARGUMENTS
- `$1`, `$2` ... `$n` parameters are used to access values of arguments.
- `$#` parameter is used to access the count of arguments.
- `$@` and `$*` parameters are used to access all arguments.

`IFS` variable and double quotes `""` havily affect expansions.

```sh
# Sets two arguments and IFS.
set -- "a:b" "c:d"
IFS=:

# Expands into first argument (no IFS splitting):
# "a:b" -> "a:b"
: "$1"

# Splits the first argument by IFS (for zsh, check IFS section):
# "a:b" -> "a" "b"
: $1

# Expands into the number of arguments:
# "a:b" "c:d" -> 2
: "$#"

# Expands into real arguments (no IFS splitting or combination):
# "a:b" "c:d" -> "a:b" "c:d"
: "$@"

# Combines with the first IFS character:
# "a:b" "c:d" -> "a:b:c:d"
: "$*"

# Splits by IFS (for zsh, check IFS section):
# "a:b" "c:d" -> "a" "b" "c" "d"
: $@
: $*
```

## SPECIAL PARAMETERS
- `$0` expands into the name that was used to launch the script.
```sh
# -> "./script.sh"
: "$0"
```

- `$-` expands into the current shell options.
```sh
set -eu
# -> "ue" (in dash)
: "$-"
```

- `$$` expands into the pid of the invoked shell. The subshells provides the
same pid, but new shells provide new pids.
```sh
# -> "34990"
: "$$"
# -> "34990"
(: "$$")
# -> "34996"
dash -c ': "$$"'
```

- `$?` expands into the exit code of the last finished command, program or
subshell.
```sh
# -> "1"
false || (: "$?")
# -> "0"
true && (: "$?")
# -> "5"
(exit 5) || (: "$?")
```

- `$!` expands into the pid of the last launched background process.
```sh
sleep 1 &
# -> 35002
PID="$!"
```


## `$''` STRINGS

`$''` strings are used to expand escape sequences (`\n`, `\t`, ...). It's
POSIX compliant, but it doesn't work in `dash`.

```sh
# Works in bash and zsh, but not dash.
VAR=$'1\n2\n'
```


## FILE DESCRIPTORS
```sh
# Assigns a readonly fd.
exec 3< file.txt

# Assigns a writeonly fd (rewrites the file).
exec 3> file.txt

# Assigns a writeonly fd (appends to the file).
exec 3>> file.txt

# Assigns a read-write fd (current position dependent).
exec 3<> data.txt

# Copy another fd.
exec 3>&1

# Closes any fd (regardless of the type).
exec 3>&-
exec 3<&-

# Input redirection
cat <&3

# Output redirection
printf "abc\n" >&3

# Checks if open
if (: >&3) 2> /dev/null; then
  # Open
else
  # Closed
fi

```

This extension allows to store new fd in a variable. It's not POSIX compliant
and doesn't work with `dash`!!!
```sh
# Saves new fd into DATA_FD variable
exec {DATA_FD}<> data.txt

# Reads from DATA_FD fd
cat <&$DATA_FD
```


## HEREDOC
```sh
# Multiline input.
cat <<EOF
some $DATA here
EOF

# Multiline input without variable expansion.
cat <<'EOF'
some $DATA here
EOF

# Multiline variable with tab supression.
# ONLY WORKS WITH TABS, NOT SPACES (wouldn't recommend to use).
cat <<-EOF
	something here
	something here
	something here
	EOF

# Multiline variable
V=$(cat <<EOF
something here
something here
something here
EOF
)

# Multiline fd (don't forget to close the fd).
exec 3<<EOF
something here
EOF
```


## HERESTRING
It ISN'T POSIX. It doesn't work with `dash`!!!
```sh
cat <<< "some data here"
cat <<< $(printf "some data here")
```


## SUBSHELLS AND GROUP COMMANDS
```sh
# - It's another subshell (though it might be the same process!).
# - It doesn't affect the current shell (doesn't change vars, options, fds etc).
( command1 | command2 )

# - It's the same shell.
# - It affects the current shell (changes vars, options, fds etc).
# - Mandatory syntax:
#   - A space after `{`.
#   - A semicolon before `}` (if the last command and `}` at the same line).
{ command1 | command2; }
```


## SOURCE VS DOT
`source` or `.` do the same. They execute commands from a file, but with a
little difference:
```sh
# It isn't POSIX and won't work with dash.
source ./utils.sh

# It's POSIX and works everywhere.
. ./utils.sh
```


## FLAGS
These flags are POSIX and hence portable.
```sh
set -eu
```

`-o pipefail` isn't posix and don't work with `dash`.
```sh
set -euo pipefail
```

### `set -e`
It's the errexit flag. It forces shell to exit on any error.
```sh
set -e

# Exits
false
# Exits
(false)

# Won't exit
false || (
  # Error handling
)

# Won't exit
(false) || (
  # Error handling
)

##############################
# Be careful

# Won't exit
: $(false)

# Won't exit
false | true
```

### `set -u`
It's the nounset flag. It forces shell to exit if the script tries to expand
a variable that isn't set.
```sh
set -u

# Exits
unset V
: "$V"

# Won't exit
V=
: "$V"

# Won't exit
V=a
: "$V"

```


## ARRAYS
Arrays aren't POSIX compliant. They don't work with `dash`!!! `bash` and
`zsh` use different initial indices (0 and 1 respectively). `bash` and `zsh`
don't agree on the `+=` operator either. I wouldn't recommend to use it.

```sh
# Creates an array.
MY_ARRAY=(a "bbbbb bbbb" ccc)

# Access to all elements.
: "${MY_ARRAY[@]}"

# bash: Gets the second element.
# zsh: Gets the first element.
: "${MY_ARRAY[1]}"

# Gets last element.
: "${MY_ARRAY[-1]}"

# Gets length.
: "${#MY_ARRAY[@]}"

# Cycles through elements.
for V in "${MY_ARRAY[@]}"; do
  : "$V"
done

# Changes an element.
MY_ARRAY[3]="ddd"

# bash: Appends data to the first element.
# zsh: Appends an element to the end of the array.
MY_ARRAY+="eee"

# Appends an array.
MY_ARRAY+=("f" "g")
```

## PARAMETER EXPANSION (VARIABLE SUBSTITUTION)
```sh
##############################
# Expands into a value

# Expands into "value" if V is empty or unset.
: ${V:-value}

# Expands into "value" if V is unset.
: ${V-value}

# Expands into "another value" if V isn't empty or unset.
: ${V:+another value}

# Expands into "another value" if V isn't unset.
: ${V+another value}

# Expands into a length of a value even if it's unset.
: "${#V}"


##############################
# Variable substitutions (expansions)

# Removes the smallest prefix by the "pattern".
: "${V#pattern}"

# Removes the largest prefix by the "pattern".
: "${V##pattern}"

# Removes the smallest suffix by the "pattern".
: "${V%pattern}"

# Removes the largest suffix by the "pattern".
: "${V%%pattern}"


##############################
# Assigns to a value

# Assigns to "value" if V is empty or unset.
: ${V:=value}

# Assigns to "value" if V is unset.
: ${V=value}


##############################
# Raises a error

# Raises a error with "error message" and exits if V is empty or unset.
: ${V:?error message}

# Raises a error with "error message" and exits if V is unset.
: ${V?error message}
```

These aren't POSIX compliant substitutions (expansions) and won't work with `dash`.

```sh
: ${V:position} # Trancates "position" characters.
: ${V:position:length} # Gets "length" characters from the "position" character.
: ${V/pattern/replacement} # Substitutes the first pattern to replacement.
: ${V//pattern/replacement} # Substitutes all patterns to replacement.
: ${V/#pattern/replacement} # Substitutes the prefix pattern to replacement.
: ${V/%pattern/replacement} # Substitutes the suffix pattern to replacement.
```
