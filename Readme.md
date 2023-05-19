# TYPO3 Docker

This repository contains the definitions for building the typo3 runtime environment and a buildbox. 

The images are based on `php:8.1-fpm-alpine` and add the minimal suggested php extensions. The webserver is an NGINX which is also installed and prepared the very basic NGINX config from the typo3 documentation. 

#### Todo's
* add usage