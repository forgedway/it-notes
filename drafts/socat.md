# Socat
```
socat [options] <address> <address>
```
It connects two streams (`addresses`) and transfers data between them.

EXAMPLE:
```
socat -u STDIN OPEN:./data.txt,trunc,creat,wronly
```
where:
  - `-u` - option for unidirectional data transfer.


## OPTIONS

## FILES
## SYSTEM, SHELL, EXEC
## STDIO
## TCP
## UDP
## UNIX SOCKETS
## PIPES
## HOW TO READ MANUAL
