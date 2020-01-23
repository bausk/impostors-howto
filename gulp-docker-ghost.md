# Impostor's Howto #2: Setting Up Docker-based Frontend Development

## Problem

I want to have a custom theme for the Ghost website engine.
I want to integrate it with libraries and components I find usable on another, unrelated frontend project (think SASS, etc.).
I want to have easy, single-command setup for my development environment.
I found an existing starter theme that uses `gulp`.

## Where we pick up prior art

- `docker-compose` for process-as-code and easy setup.

- Ghost development server (Node.js) image for testing the theme.

- An existing Ghost starter theme with Gulp-based build.

- A Node.js based image for building the theme and rebuilding on changes.

## Challenges

- Allow live testing of the theme.

- Keep data between container runs.

- Configure docker-compose as 2-process "devserver <-> theme build watcher" pattern, with theme build
process decoupled from dev server.

- Install npm packages without the need to rebuild Docker images.

## Prerequisites

WSL 1 or 2 with Docker and Docker-Compose installed ([workflow-setup.md](workflow-setup.md)).

## Solutions

### Ghost server for testing the theme

I don't need to change the CMS source itself, just the theme, therefore an off-the-shelf image should be enough for now,
and I could build off it if I need any customization beyond what's possible with theming.

``` yaml
# docker-compose.yml
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

- [1] Mount current sources for theme testing.
- [2] Keep site DB persistent in a separate directory.

### Theme/assets build and watch

We have some existing starter theme sources, a gulpfile.js that defines how final assets are built,
and a command `yarn && yarn dev` to start the build/watch process via `gulp` script in `package.json`.

In a previous bad attempt at setting up the docker environment, `nohup` was used to start the command separately
from the main Node.js process executing the Ghost server itself. Instead, define a separate `build` container
in our docker-compose.yml:

``` yaml
version: '3'
services:
  blog:
    image: ghost:3
    # working_dir: /var/lib/ghost
    ports: 
      - 2368:2368
    volumes: 
      - .:/var/lib/ghost/content/themes/ghost-theme
      - ../ghost-data:/var/lib/ghost/content
    environment: 
      - NODE_ENV=development
    command: >
      bash -c "bash /usr/local/bin/docker-entrypoint.sh node current/index.js"
  build:
    image: node:lts-buster-slim  # [3]
    working_dir: /app
    volumes: 
      - .:/app
    environment: 
      - NODE_ENV=development
    command: >
      bash -c "yarn && yarn dev"
```

- [3] We use a basic Node.js image because it has `yarn` installed already and we need nothing else so far.
In case my frontend uses another build process, for example Jekyll, I would simply swap out `node:lts-buster-slim`
for an image with Ruby/Jekyll.

### Install npm packages without rebuild

Since we can run arbitrary commands with a Docker container and node_modules is persisted to host disk due to [1],
installing a package requires, as an example:

``` bash
λ docker-compose stop build
λ docker-compos run build bash -c "yarn add @csstools/postcss-sass --dev"  # [4]
λ docker-compos run build bash -c "yarn add tachyons-sass --dev"  # [4]
λ docker-compose stop start
```
[4] See https://marmelab.com/blog/2017/02/08/yarn-npm-install-within-docker-container.html

### Connect Tooling: SASS

My "root" CSS processor is PostCSS as defined in prior art, the original theme's gulpfile.js. I have just installed
a SASS PostCSS plugin and an external CSS library that should be ingested via that plugin. Let's see how it goes:

``` js
// gulpfile.js
// ...whatever theme already has
var sass = require('@csstools/postcss-sass')

function css(done) {
    var processors = [
        easyimport,
        customProperties({preserve: false}),
        colorFunction(),
        sass({
            includePaths: ['node_modules']
          }),
        // tailwind(), // Used to try connecting tachyons here, not anymore!
        autoprefixer(),
        cssnano()
    ];
    pump([
        src('assets/css/*.css', {sourcemaps: true}),
        postcss(processors),
        dest('assets/built/', {sourcemaps: '.'}),
        livereload()
    ], handleError(done));
}    
```
