# WordPress Project

This is a WordPress project handled mostly by Composer.

# Installation

## Pre-requisites

- PHP >= 8.1
- Composer

## Composer

To install the project on your local environment, make sure you have Composer installed on your machine, and from the project root folder in a terminal, run:

```composer install```

That command alone should handle most of what's necessary for the project to be able to run.

## Database

- Grab a database dump from another working environment for this project
- Create a new database in your local environment
- Import the dump
- Replace the URLs, especially in the wp_options table, so they match the URL of your local environment
- Duplicate the the [.env.example file](/.env.example) to create a .env file
- Edit the newly created .env file to replace the default values with the correct ones

## Local Web Server

Whether you are using WAMP, MAMP, LAMP or something custom, you might need to configure it so you can access this project.

If your thing is to just have all your projects as subfolders and your URLs look like `localhost/project1/`, then it is very likely you will not have anything to do at all! Just know that the URL for this project will probably feature an extra "wp/", like `localhost/project1/wp/`.

If you'd rather configure a new vhost for each project, then you have two options:

1. Create a classic Wordpress vhost configuration that points to the "public" folder. Your final URL will feature a "/wp" in it, that's normal
2. If you don't want the "/wp" in your URL, you have to configure your vhost accordingly, and it can be very tricky depending on your level of knowledge. Here is an example of a working nginx configuration for this architecture:

```nginx
server {
    listen       80;
    server_name  project1.local;

    return 302   https://project1.local$request_uri;
}

server {
    listen       443 ssl http2;
    server_name  project1.local;
    root         /home/user/projects/project1/public;
    index        index.php;

    include snippets/self-signed.conf;
    include snippets/ssl-params.conf;

    client_max_body_size 100M;

    location = /favicon.ico {
        log_not_found off;
        access_log off;
    }

    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }

    location ~ /\. {
        deny all;
    }

    location ~* /(?:uploads|files)/.*\.php$ {
        deny all;
    }

    location /content/ {
        root /home/user/projects/project1/public;
    }

    location /wp-content/ {
        alias /home/user/projects/project1/public/content/;
    }

    location /wp-admin/ {
        root /home/user/projects/project1/public/wp;
    }

    location /wp-includes/ {
        root /home/user/projects/project1/public/wp;
    }

    location / {
        index index.php;
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        fastcgi_pass    php81;
        fastcgi_index   index.php;
        fastcgi_param   SCRIPT_FILENAME  $document_root/wp$fastcgi_script_name;
        include         fastcgi_params;
        fastcgi_intercept_errors on;
    }
}
```

## Images and Stuff

If you fancy having pretty media showing on your local version of this project, then feel free to open Filezilla or equivalent and download the "uploads" folder to the "public/content" folder of this project. It is already gitignored.
