# Example of Strace

There's a very minimal example of a Dockerfile.  It's made to run and
exit, and it runs `ls` under strace to show all the system calls being
made.

```
docker build . -t strace-docker
docker run strace-docker
```

Or just type `make`

If you're debugging something, you would probably install strace in
your Dockerfile and use an `ENTRYPOINT` shell script to run your thing
in strace.  It could write to a file (if it doesn't exit) or just
print everything to stdout/stderr (drop the -o arg to strace).

## curl

There's also an example here of running a network based thing
(e.g. `curl`) and using strace to see where it connects to and what it
sends, such as the endpoint requested (`GET /headers`)

Use `make curl-docker`

