We'll focus on different features and portability between shells.


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

# Alternatives (don't work):

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


## `READ` line by line
A portable way to read line by line from stdin:
```sh
while IFS= read -r line; do
  echo "line: $line"
done
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
if { : >&3; } 2> /dev/null; then
  # Open
else
  # Closed
fi

```

This extension allows to store new fd in a variable. It isn't POSIX (doesn't
work with dash)!!!
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
It ISN'T POSIX. It doesn't work with dash!!!
```sh
cat <<< "some data here"
cat <<< $(printf "some data here")
```
