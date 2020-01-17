---
title: Cache Gems in Docker
date: 2020-01-17
published: true
tags: []
series: false
cover_image: ./images/docker.png
canonical_url: false
description: "While dockerizing a rails app or any other app using bundler for gems management, one of the problem is slow bundle install while building the image"
---

Assume you have a `docker-compose.yaml` below. You will be required to run `docker-compose build` every time you change your `Gemfile`, this can become time-consuming since it reinstalls all the `gems` a fresh. What if you can only run `bundle install` and get back to development just like you've been doing it without docker. Let's see how it's possible.

```yaml
version: "3"
services:
  app:
    build: .
    volumes:
      - .:/app
    depends_on:
      - postgres
  postgres:
    image: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      - POSTGRESS_USER = gathuku
      - POSTGRESS_PASSWORD = password

volumes:
  postgres-data:

```

### Setting up `docker-compose.yaml`
To achieve caching you will need.
- Set up `BUNDLE_PATH` - set up bundle path in-service environment
- Set up a named volume `bundle-data` and add it to top-level volume.

Below is the modified `docker-compose.yml`
```yaml
version: "3"
services:
  app:
    build: .
    volumes:
      - .:/app
      - bundle-data: /bundle
    depends_on:
      - postgres
    environment:
      - BUNDLE_PATH = /bundle/vendor
  postgres:
    image: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      - POSTGRESS_USER = gathuku
      - POSTGRESS_PASSWORD = password

volumes:
  postgres-data:
  bundle-data:

```
### How to use it?
To create a cache on the gem run
```
docker-compose app bundle install
```
The command will install all the gems in the volume we have defined.

After adding or remove a gem in `Gemfile`. Stop the container
```
docker-compose down
```
Then install
```
docker-compose app bundle install
```
Then get the container up and running.
```
docker-compose down
```

### Conclusion
We this set up you can cache gems. This helps a lot in saving time.
