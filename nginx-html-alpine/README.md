# NGINX PHP with FPM

This folder contains a docker file that installs Nginx on an Alpine distribution.

All commands are based on a Ubuntu OS environment.

----------

## Pre-requirements

[Docker](https://docs.docker.com/engine/install/)

[Docker Compose](https://docs.docker.com/compose/install/)

[cURL](https://curl.se/)

----------


## Running

Run each command at a time:
```
cd nginx-html-alpine
docker build -t nginx-html-alpine:latest .
docker run -p 8080:80 nginx-html-alpine:latest
```

Go to the browser and tip localhost:8080

----------


## Testing

The `curl` flag `-I` will show you the HTTP Header which is very useful to test and debug any web application.

```
curl -I http://localhost:8080/
```

Output:
```
HTTP/1.1 200 OK
Server: nginx
(...)
```
