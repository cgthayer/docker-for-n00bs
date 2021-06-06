FROM alpine
RUN apk update && apk add strace
 
RUN mkdir /app
WORKDIR /app
COPY Dockerfile .

CMD strace -f -s1024 -o strace.out ls -lR && cat strace.out
