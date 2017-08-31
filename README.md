Thermostat Web Gateway + Web Client Builder Docker image
=============================

This repository contains a Dockerfile for a Thermostat Web Gateway + Web Client Builder image, which in turn
can be used to build a Thermostat Web Gateway image which also serves the Thermostat Web Client, 
call this image `icedtea/thermostat-web-gateway-client`.

Environment variables
---------------------------------

The following variables must be specified when building the web client by passing `-e VAR=VALUE` 
to your `s2i build` command:

|    Variable name              |    Description                              |
| :---------------------------- | -----------------------------------------   |
|  `GATEWAY_URL`                | URL used by the web client to connect to the web gateway |

The icedtea/thermostat-web-gateway-client image recognizes the following environment
variables that you can set during initialization by passing `-e VAR=VALUE` to
the Docker run command

|    Variable name              |    Description                              |
| :---------------------------- | -----------------------------------------   |
|  `MONGO_URL`                  | URL for the MongoDB storage instance        |
|  `MONGO_DB`                   | Name of the database to use within the MongoDB storage          |
|  `MONGO_USERNAME`             | Username for the MongoDB database           |
|  `MONGO_PASSWORD`             | Password for the MongoDB database           |
|  `TLS_ENABLED`                | Whether to encrypt communication with the web gateway using TLS (default value: "true") |
|  `WEB_CLIENT_ENABLED`         | Whether to include an endpoint for the Thermostat web client (default value: "true") |
|  `APP_USER`                   | The application user the Java app Thermostat shall monitor runs as (default value: "default") |

Usage
---------------------------------
First, you need to build this image, let's call it `icedtea/thermostat-web-gateway-client-builder`:

    $ docker build -t icedtea/thermostat-web-gateway-client-builder .

Next, build a Thermostat Web Gateway version into `icedtea/thermostat-web-gateway` using the builder
image:

    $ s2i build http://icedtea.classpath.org/mirror/git/thermostat-ng-web-gateway icedtea/thermostat-web-gateway-client-builder icedtea/thermostat-web-gateway

Then, build the Thermostat Web Client and include it as an endpoint in the Web Gateway using the image
built in the previous step:

    $ s2i build --scripts-url=image:///usr/libexec/s2i/webclient -e GATEWAY_URL="[...]" http://icedtea.classpath.org/mirror/git/thermostat-ng-web-client icedtea/thermostat-web-gateway icedtea/thermostat-web-gateway-client

Finally, run the built image while setting the required environment variables:

    $ docker run -e [...] -it icedtea/thermostat-web-gateway-client
