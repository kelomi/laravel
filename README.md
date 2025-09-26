## Laranodechat – Real-time One-to-One Chat App

## Project Overview

Laranodechat is a real-time one-to-one chat application built with:

Laravel (PHP) → Backend (port 8000)

Node.js + Socket.io → Frontend for real-time messaging (port 3000)

Redis → Message broker (port 6379)

MySQL → Relational database for persistence (port 3306)

Unlike many chat examples online that only provide public chat, this project implements private one-to-one chat, while still storing messages for later retrieval.

##  How I Built It

Created two folders: laravel/ and nodejs/.

Wrote separate Dockerfiles for Laravel and Node.js.

Wrote a docker-compose.yml file to orchestrate Laravel, Node.js, MySQL, and Redis containers.

Configured Laravel and Node.js to talk to each other via Docker network.

▶️ Running the App

Follow these steps to run the app on your own machine:

## Step 1 – Build the Containers
docker compose build


This will:

Build the Laravel app image from ./laravel/Dockerfile

Build the Node.js image from ./nodejs/Dockerfile

Pull MySQL & Redis official images

## Step 2 – Start the Containers
docker compose up -d


This will start:

MySQL → localhost:3306

Redis → localhost:6379

Laravel app → PHP-FPM (exposed on port 8000)

Node.js app → Socket.io server (exposed on port 3000)

## Step 3 – Laravel Setup

Get inside the Laravel container:

docker exec -it laravel_redis_chat_app bash


Inside the container: run the below

composer install              # install Laravel dependencies

php artisan key:generate      # generate Laravel app key

php artisan migrate           # create database tables


Update your .env file inside Laravel to match the MySQL and Redis details from docker-compose.yml.

Exit the container:

exit

## Step 4 – Node.js Setup

Enter your Node.js container:

docker exec -it laravel_chat_nodejs bash


Inside the container: run

npm install   # install dependencies


Exit:

exit

## Step 5 – Access the App

Laravel (backend + auth): → http://localhost:8000

Node.js (real-time chat): → http://localhost:3000

## Troubleshooting & Fixes

# When I was running the project, I faced a few errors:

1. Laravel container kept restarting

✅ Fixed by running composer manually:

docker-compose run --rm laravel composer install --no-interaction --prefer-dist --no-dev

2. Database tables missing

Error:

SQLSTATE[42S02]: Base table or view not found: 1146 Table 'laravel.users' doesn't exist


✅ Fixed by running migrations:

php artisan migrate

3. APP_KEY (Laravel encryption key) issue

Error:

RuntimeException: The only supported ciphers are AES-128-CBC and AES-256-CBC...


👉 Cause: APP_KEY missing or invalid inside the container’s .env.

✅ Fix (what I did):

Generated a proper key: RUN

docker exec -it laravel_redis_chat_app bash

php artisan key:generate

Or generated locally and copied .env in:

docker cp .env laravel_redis_chat_app:/var/www/.env


Cleared Laravel’s cached configs:

php artisan config:clear

php artisan cache:clear

php artisan route:clear

php artisan view:clear

php artisan config:cache


Restarted the container → encryption error gone ✅

## How to Persist APP_KEY Across docker compose down && up

Recommended (for dev): Bind-mount .env into container

services:
  laravel_redis_chat_app:
    volumes:
      - ./laravel/.env:/var/www/.env


Alternative: Keep .env locally, re-copy on container recreate:

docker cp .env laravel_redis_chat_app:/var/www/.env

docker exec -it laravel_redis_chat_app bash -c "php artisan config:clear && php artisan config:cache"


⚠️ Security Note: Never commit .env with real secrets to Git. Keep .env in .gitignore and provide .env.example for others.

✅ Proof of Success

📸 Screenshots included:

Login/Register page

![alt text](image.png)

Registering a user

![alt text](image-1.png)

Logged in & ready to chat

![alt text](image-2.png)

 Final Notes

With this setup, anyone can:

Clone the repo

Run docker compose up -d

Run setup steps for Laravel & Node.js

Open in browser → and start chatting in real-time 

This project demonstrates containerized full-stack real-time apps with Laravel + Node.js + Redis + MySQL, and documents common containerization pitfalls & fixes.