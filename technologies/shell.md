We'll focus on different features and portability between shells.

# Features

## `ECHO`
Escape sequences are treated differently between shells:
```sh
bash -c "echo 'a\nb'"
# a\nb

dash -c "echo 'a\nb'"
# a
# b
```

So the better way is to use `printf`:
```sh
bash -c "printf 'a\nb\n'"
dash -c "printf 'a\nb\n'"
# a
# b


# BUT BE CAREFUL. ALWAYS USE DOUBLE QUOTES:
V="a b"
printf '%s\n' $V

# dash or bash:
# a
# b

# zsh:
# a b
```


## `IFS`
`zsh` works differently from other shells, so try to avoid relying on `IFS`.

```sh
values="aa:bb cc:dd ee:ff"

IFS=:
for item in $values; do
  echo $item
done

# output:
# aa:bb cc:dd ee:ff
```

To reset `IFS` in a portable way:
```sh
IFS="$(printf " \t\n"; printf x)"; IFS=${IFS%x}
# or
IFS="$(printf ' \t')
"

# Alternatives (won't work):

# Doesn't work in dash.
IFS=$' \t\n'

# Doesn't preserve the trailing newline
# because of the POSIX command substitution.
IFS="$(printf ' \t\n)"
```


## `$` STRINGS

`$` strings are used to expand escape sequences (`\n`, `\t`, ...). It doesn't
work in `dash`.

```sh
# Works in bash and zsh, but not dash.
VAR=$'1\n2\n'
```


## FDs:
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

# Closes any fd.
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

This extension allows to store new fd in a variable. It isn't POSIX. It
doesn't work with `dash`!!!
```sh
exec {data_fd}<> data.txt
```


## HEREDOC
```sh

# Multiline input
cat <<EOF
some $DATA here
EOF

# Multiline input without variable expansion
cat <<'EOF'
some $DATA here
EOF

# Multiline variable
V=$(cat <<EOF
some $DATA here
EOF
)

# Multiline fd (don't forget to close the fd).
exec 3<<EOF
some $DATA here
EOF
```


## HERESTRING
It ISN'T POSIX. It doesn't work with `dash`!!!
```sh
cat <<< "some data here"
cat <<< $(printf "some data here")
```


## `:`
It's a cool thing to do nothing like `true`. It can be used for variable substitution.
```sh
# Does nothing
:
: a b c

# If VAR isn't set, it will be set to "some data here".
: ${VAR:=some data here}
```


## SUBSHELLS AND GROUP COMMANDS
```sh
# - It's another subshell (though it might be the same process).
# - It doesn't affect the current shell (doesn't change vars, options, fds etc).
( command1 | command2 )

# - It's the same shell.
# - It affects the current shell (changes vars, options, fds etc).
# - Mandatory syntax:
#   - A space after `{`.
#   - A semicolon before `}` (if the last command and `}` at the same line).
{ command1 | command2; }
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
: "${V}"

# Won't exit
V=a
: "${V}"

```


## ARRAYS
Arrays aren't POSIX compliant. They don't work with `dash`!!! `bash` and
`zsh` use different initial indices (0 and 1 respectively). `bash` and `zsh`
don't agree on the `+=` operator either. I wouldn't recommend to use it.

```sh
# Creates an array.
my_array=(a "bbbbb bbbb" ccc)

# Access to all elements.
: "${my_array[@]}"

# bash: Gets the second element.
# zsh: Gets the first element.
: "${my_array[1]}"

# Gets last element.
: "${my_array[-1]}"

# Gets length.
: "${#my_array[@]}"

# Cycles through elements.
for v in "${my_array[@]}"; do
  : "$v"
done

# Changes an element.
my_array[3]="ddd"

# bash: Appends data to the first element.
# zsh: Appends an element to the end of the array.
my_array+="eee"

# Appends an array.
my_array+=("f" "g")
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

Aren't POSIX compliant substitutions (expansions). They don't work with `dash`.

```sh
: ${V:position} # Trancates "position" characters.
: ${V:position:length} # Gets "length" characters from the "position" character.
: ${V/pattern/replacement} # Substitutes the first pattern to replacement.
: ${V//pattern/replacement} # Substitutes all patterns to replacement.
: ${V/#pattern/replacement} # Substitutes the prefix pattern to replacement.
: ${V/%pattern/replacement} # Substitutes the suffix pattern to replacement.
```

# Tips and tricks

## SUBPROCESSES HANDLING
To kill all subprocesses we should kill the group pid. This is the portable way to do so.
```sh
handle_exit() {
  trap '' INT TERM
  kill -s TERM -- -$$
  wait
}
trap handle_exit INT TERM

process1 &
process2 &
process3 &

wait
```


## ITERATE OVER LINES
A portable way to iterate over multiple lines.
```sh
# Iterate over lines from stdin.
while IFS= read -r line; do
  echo "line: $line"
done

# Iterate over lines from a variable.
echo "$data" | while IFS= read -r line; do
  echo "line: $line"
done

# Iterate over lines from a file.
cat data.txt | while IFS= read -r line; do
  echo "line: $line"
done

```

## ITERATE OVER WORDS
A portable way to iterate over words.
```sh
word_pattern='[^[:space:]]+'
echo "$data" | grep -o -E "$word_pattern" | \
  while IFS= read -r word; do
    printf "word: %s\n" "$word"
  done
```
