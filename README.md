# Aimeos Docker images

The images are used for continuous integration tests and are base on Ubuntu 20.04 images from CircleCI.

## Available images

* aimeos/ci-php:7.1
* aimeos/ci-php:7.2
* aimeos/ci-php:7.3
* aimeos/ci-php:7.4
* aimeos/ci-php:8.0
* aimeos/ci-php:8.1

Run the images using:

```bash
docker pull aimeos/ci-php:<version>
docker run -it aimeos/ci-php:<version> bash
```

## Available PHP extesions

* apcu
* bcmath
* curl
* gd
* intl
* mbstring
* mysql
* pgsql
* sqlite
* xdebug
* xml
* zip

The images `aimeos/ci-php:7.4`, `aimeos/ci-php:8.0` and `aimeos/ci-php:8.1` contain in addition:

* ibm_db2
* oci8
* pdo_oci
* pdo_sqlsrv
* sqlsrv
