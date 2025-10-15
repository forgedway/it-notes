# MANIFEST
- The note consists of SOME tips and tricks that I reckon as important.

## SUBPROCESSES HANDLING
To kill all subprocesses we should kill the group pid. This is the portable
way to do so.
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
# From stdin.
while IFS= read -r LINE; do
  : "$LINE"
done

# From a variable.
echo "$DATA" | while IFS= read -r LINE; do
  : "$LINE"
done

# From a file.
cat data.txt | while IFS= read -r LINE; do
  : "$LINE"
done
```

## ITERATE OVER WORDS
A portable way to iterate over words.
```sh
WORD_PATTERN='[^[:space:]]+'
echo "$DATA" | grep -o -E "$WORD_PATTERN" | \
  while IFS= read -r WORD; do
    : "$WORD"
  done
```

## FROM PIPE TO AN ARGUMENT
To pass data from pipe as an argument, we can use `xargs`:
```sh
echo "file.txt" | xargs -n 1 touch
```

But it works only with binaries and it won't work with commands. So, the
other way is to use shell features:
```sh
command1 | (command2 "$(cat)")

# For example:
validate_file() {
  test -f "$1"
}
echo "file.txt" | (validate_file "$(cat)")
```

Be aware! we use a subshell to make the code portable:
```sh
echo a | echo $(cat -n)
# bash: a
# dash: a
# zsh:  *stucks* (the cat waits for the input from the shell and not the pipe)

echo a | ( echo b | echo $(cat) )
# bash: b
# dash: b
# zsh:  a

# Portable behavior
echo a | (echo $(cat))
# bash: a
# dash: a
# zsh:  a
```
