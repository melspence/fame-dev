# Forrondo Artist Management Excellence Inc

This project implements the case study used in my Database Design course. In fully implementing the case study, I aim to bring together my learnings from Database Design, UX Design, frontend evelopment, backend development and Dev Ops.

The docker development environment is based on a [tutorial series](https://tech.osteel.me/posts/docker-for-local-web-development-why-should-you-care "Docker for local web development, introduction: why should you care?") about leveraging Docker for local web development.

The current branch is based on part 3 of the series, which is about managing a three-tier architecture with frameworks.

## Content

This branch contains a three-tier architecture application running on a LEMP stack, supported by Docker and orchestrated by Docker Compose.

It includes

* A container for Nginx;
* A container for the backend application based on [Laravel](https://laravel.com/);
*  A container for the frontend application based on [Vue.js](https://vuejs.org/);
* A container for MySQL;
* A container for phpMyAdmin;
* A volume to persist MySQL data.

The containers are based on Alpine images when available, for an optimised size.

## Prerequisites

Make sure [Docker Desktop for Mac or PC](https://www.docker.com/products/docker-desktop) is installed and running, or head [over here](https://docs.docker.com/install/) if you are a Linux user. You will also need a terminal running [Git](https://git-scm.com/).

This setup also uses localhost's port 80, so make sure it is available.

## Directions of use

Add the following domains to your machine's hosts file:

127.0.0.1 backend.fame-dev.test frontend.fame-dev.test phpmyadmin.fame-dev.test

Clone the repository and checkout the 3-tier branch:

$ git@github.com:melspence/fame-dev.git cd fame-dev

$ git checkout 3-tier

The rest of the commands are to be run from the root of the project.

Copy .env.example to .env, both at the root of the project and in src/backend:

$ cp .env.example .env && cd src/backend && cp .env.example .env && cd ../..

To ensure compatibility across operating systems, specify your system's current user ID in the newly created .env at the root of the project, e.g.:

HOST_UID=1000

You can obtain that ID by running the following command in a terminal:

$ id -u

The following commands may take a little bit of time, as some Docker images might need downloading.

Install the backend dependencies:

$ docker compose run --rm backend composer install

Install the frontend dependencies:

$ docker compose run --rm frontend yarn install

Start the project:

$ docker compose up -d

Once the project is started, generate the Laravel application's key:

$ docker compose exec backend php artisan key:generate

You can now visit frontend.demo.test and backend.demo.test.

Yarn, Artisan and Composer are now directly available from the running containers:

$ docker compose exec frontend yarn
$ docker compose exec backend php artisan
$ docker compose exec backend composer

For example, you could run Laravel's default database migrations to confirm the MySQL connection is working:

$ docker compose exec backend php artisan migrate

Explanation

The images used by the setup are listed and configured in docker-compose.yml.

When building and starting the containers based on the images for the first time, a MySQL database named demo is automatically created (you can pick a different name in the MySQL service's description in docker-compose.yml).

Minimalist Nginx configurations for the backend application, the frontend application and phpMyAdmin are also copied over to Nginx's container, making them available at backend.fame-dev.test, frontend.fame-dev.test and phpmyadmin.fame-dev.test respectively (the database credentials are root / root).

The directories containing the backend and frontend applications are mounted onto both Nginx's and the applications' containers, meaning any update to the code is immediately available upon refreshing the page, without having to rebuild any container.

Moreover, the Vue.js development server is automatically started, meaning you can update the code and benefit from hot-reload straight away.

The frontend application is consuming a simple endpoint from the backend application to fetch and display the text below the animated gif.

Finally, the database data is persisted in its own local directory through the volume mysqldata, which is mounted onto MySQL's container.