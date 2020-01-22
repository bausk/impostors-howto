# Impostor's Howto #2: Setting Up Docker-based Frontend Development

## Problem

I want to have a custom theme for the Ghost website engine.
I want to integrate it with libraries and components I find usable on another, unrelated frontend project (think SASS, etc.).
I want to have easy, single-command setup for my development environment.
I found an existing starter theme that uses `gulp`.

## Challenges

- Come up with `docker-compose` configuration for "devserver - asset build watcher" pattern from scratch

- Allow live testing of the theme with theme build not dependent on dev server

- Install npm packages without the need to rebuild Docker images

## Where we pick up prior art

- Docker and `docker-compose`

- Ghost development server (Node.js)

- Ghost starter theme with Gulp-based build

## Prerequisites

WSL 1 or 2 with Docker and Docker-Compose installed ([workflow-setup.md](workflow-setup.md)).

## Solutions

### `docker-compose` configuration

I don't need to change the CMS source itself, just the theme, therefore an off-the-shelf image should be enough for now,
and I could build off it if I need any customization beyond what's possible with theming.

``` yaml
version: '3'
services:
  blog:
    image: ghost:3
    # working_dir: /var/lib/ghost  # Shouldn't need this anyway
    ports: 
      - 2368:2368  # Expose Ghost dev server to host
    volumes: 
      - .:/var/lib/ghost/content/themes/ghost-theme # [1]
      - ../ghost-data:/var/lib/ghost/content # [2]
    environment: 
      - NODE_ENV=development
    command: >
      bash -c "bash /usr/local/bin/docker-entrypoint.sh node current/index.js"
```

#### [1]
