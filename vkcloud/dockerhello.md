create files Dockerfile and name.txt in one directory
```
cd /~/~~
touch Dockerfile
touch name.txt
```
write somethinng in name.txt
```
nano /~/~~/name.txt
Hello pi
```
write this in Docker file
```
FROM alpine:latest
RUN mkdir -p /hello
WORKDIR "/hello"
COPY name.txt
ENTRYPOINT exec echo "<your text> $(cat name.txt)"
```
