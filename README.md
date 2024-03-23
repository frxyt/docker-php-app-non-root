# Docker Image for packaging a PHP application (Non-Root Version), by [FEROX](https://ferox.yt)

![Docker Pulls](https://img.shields.io/docker/pulls/frxyt/php-app-non-root.svg)
![GitHub issues](https://img.shields.io/github/issues/frxyt/docker-php-app-non-root.svg)
![GitHub last commit](https://img.shields.io/github/last-commit/frxyt/docker-php-app-non-root.svg)

This image packages PHP with PHP-FPM & NGINX in a slim image so you can focus only on your application!

> This image runs with non-root user `www-data` (`82:82` on Alpine, `33:33` on Debian), for both NGINX and PHP-FPM processes, can run without any capabilities (`cap_drop: [ all ]`), and will expose by default the application deployed in path `/app` on port `8080`.

* Docker Hub: <https://hub.docker.com/r/frxyt/php-app-non-root>
* GitHub: <https://github.com/frxyt/docker-php-app-non-root>

## Docker Hub Image

**`frxyt/php-app-non-root`**:

* Alpine variant:
  * PHP 7.4: `frxyt/php-app-non-root:7.4-alpine`
  * PHP 8.0: `frxyt/php-app-non-root:8.0-alpine`
  * PHP 8.1: `frxyt/php-app-non-root:8.1-alpine`
  * PHP 8.2: `frxyt/php-app-non-root:8.2-alpine`
  * PHP 8.3: `frxyt/php-app-non-root:8.3-alpine`
* Debian 11 (Bullseye) variant:
  * PHP 7.4: `frxyt/php-app-non-root:7.4-bullseye`
  * PHP 8.0: `frxyt/php-app-non-root:8.0-bullseye`
  * PHP 8.1: `frxyt/php-app-non-root:8.1-bullseye`
  * PHP 8.2: `frxyt/php-app-non-root:8.2-bullseye`
  * PHP 8.3: `frxyt/php-app-non-root:8.3-bullseye`

## Usage

1. Add your built PHP application into `/app`
1. Start the container
1. Access it on port `8080`

## Examples

### Symfony4 application with GD & MySQL extensions

`Dockerfile` file content:

```Dockerfile
FROM frxyt/php-dev-full:7.4-cli as build
COPY . /app
WORKDIR /app
RUN composer install --no-interaction --no-progress --no-suggest --no-dev --optimize-autoloader
RUN yarn install
RUN yarn encore production
RUN rm -rf node_modules

FROM frxyt/php-app-non-root:7.4-alpine as app
COPY --from=build /app /app
USER root
RUN     echo -n "#!/bin/bash\nphp bin/console doctrine:migrations:migrate -n" > /usr/local/bin/frx-start.d/app \
    &&  chmod +x /usr/local/bin/frx-start.d/app \
    &&  /usr/local/bin/frx-tz Europe/Paris \
    &&  apk add --no-cache \
            freetype-dev \
            libjpeg-turbo-dev \
            libpng-dev \
    &&  docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg \
    &&  docker-php-ext-install -j$(nproc) gd \
    &&  docker-php-ext-install -j$(nproc) pdo_mysql \
    &&  sed -i 's/^\(\s*\)root\(\s*\)[^;]*;/\1root\2\/app\/public;/g' /etc/nginx/conf.d/default.conf
USER www-data
```

## Build & Test

* Alpine variant:

  * PHP 7.4:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=alpine --build-arg FRX_PHP_VERSION=7.4 -t frxyt/php-app-non-root:7.4-alpine .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:7.4-alpine
    ```

  * PHP 8.0:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=alpine --build-arg FRX_PHP_VERSION=8.0 -t frxyt/php-app-non-root:8.0-alpine .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:8.0-alpine
    ```

  * PHP 8.1:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=alpine --build-arg FRX_PHP_VERSION=8.1 -t frxyt/php-app-non-root:8.1-alpine .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:8.1-alpine
    ```

  * PHP 8.2:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=alpine --build-arg FRX_PHP_VERSION=8.2 -t frxyt/php-app-non-root:8.2-alpine .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:8.2-alpine
    ```

  * PHP 8.3:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=alpine --build-arg FRX_PHP_VERSION=8.3 -t frxyt/php-app-non-root:8.3-alpine .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:8.3-alpine
    ```

* Debian 11 (Bullseye) variant:

  * PHP 7.4:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=bullseye --build-arg FRX_PHP_VERSION=7.4 -t frxyt/php-app-non-root:7.4-bullseye .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:7.4-bullseye
    ```

  * PHP 8.0:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=bullseye --build-arg FRX_PHP_VERSION=8.0 -t frxyt/php-app-non-root:8.0-bullseye .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:8.0-bullseye
    ```

  * PHP 8.1:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=bullseye --build-arg FRX_PHP_VERSION=8.1 -t frxyt/php-app-non-root:8.1-bullseye .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:8.1-bullseye
    ```

  * PHP 8.2:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=bullseye --build-arg FRX_PHP_VERSION=8.2 -t frxyt/php-app-non-root:8.2-bullseye .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:8.2-bullseye
    ```

  * PHP 8.3:
  
    ```sh
    docker build -f Dockerfile --build-arg FRX_OS_VARIANT=bullseye --build-arg FRX_PHP_VERSION=8.3 -t frxyt/php-app-non-root:8.3-bullseye .
    docker run --rm --cap-drop=all -p 8080:8080 -v $(pwd)/tests:/app:ro frxyt/php-app-non-root:8.3-bullseye
    ```

## License

This project and images are published under the [MIT License](LICENSE).

```txt
MIT License

Copyright (c) 2022,2023,2024 FEROX YT EIRL, www.ferox.yt <devops@ferox.yt>
Copyright (c) 2022,2023,2024 Jérémy WALTHER <jeremy.walther@golflima.net>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
