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


## REPEAT SEVERAL TIMES
This is a portable and stable way to repeat something several times.
```sh
i=0;
while [ $i -lt "$COUNT" ]; do
  i=$((i + 1))
  :
done
```

Alternatives with drawbacks:
```sh
# Depends on IFS (will run once with IFS='').
for _ in $(seq 10); do
  :
done

# Runs in a subshell (can't change the shell environment).
(unset IFS; for _ in $(seq 10); do
  :
done)

# Neat, but it destroys STDIN processing.
seq 10 | while read _; do
  :
done
```

Not portable alternatives:
```sh
# Isn't POSIX and won't work in dash.
for ((i=0; i<10; i++)); do
  :
done

# Isn't POSIX and won't work in dash.
for _ in {1..10}; do
  :
done
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

## SUBSHELL FUNCTIONS
We can use a subshell in function definitions and these functions won't change
the parent shell state.
```sh
# Sets V
f() { V=a; }
f

# Doesn't set V
f() ( V=b )
f
```

## RUN A SCRIPT IN ANOTHER LANGUAGE
```sh
cat <<'EOF' | perl
my @a = (1..5);
for(@a){
  print("value: $_\n");
}
EOF
```
