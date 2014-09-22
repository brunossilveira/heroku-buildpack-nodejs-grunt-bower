Heroku buildpack: Node.js 
============================================


How it Works
------------

Here's an overview of what this buildpack does:

- Uses the [semver.io](https://semver.io) webservice to find the latest version of node that satisfies the [engines.node semver range](https://npmjs.org/doc/json.html#engines) in your package.json.
- Allows any recent version of node to be used, including [pre-release versions](https://semver.io/node.json).
- Uses an [S3 caching proxy](https://github.com/heroku/s3pository#readme) of nodejs.org for faster downloads of the node binary.
- Discourages use of dangerous semver ranges like `*` and `>0.10`.
- Uses the version of `npm` that comes bundled with `node`.
- Puts `node` and `npm` on the `PATH` so they can be executed with [heroku run](https://devcenter.heroku.com/articles/one-off-dynos#an-example-one-off-dyno).
- Caches the `node_modules` directory across builds for fast deploys.
- Doesn't use the cache if `node_modules` is checked into version control.
- Runs `npm rebuild` if `node_modules` is checked into version control.
- Always runs `npm install` (with devDependencies) to ensure [npm script hooks](https://npmjs.org/doc/misc/npm-scripts.html) are executed.
- Always runs `npm prune` after restoring cached modules to ensure cleanup of unused dependencies.

For more technical details, see the [heavily-commented compile script](https://github.com/abecadel/heroku-buildpack-nodejs-grunt-bower/blob/master/bin/compile).

Usage
-----

Create a new app with this buildpack:

    heroku create myapp --buildpack https://github.com/abecadel/heroku-buildpack-nodejs-grunt-bower.git

Or add this buildpack to your current app:

    heroku config:add BUILDPACK_URL=https://github.com/abecadel/heroku-buildpack-nodejs-grunt-bower.git
    
Enable heroku `user-env-compile` lab:
    
    heroku labs:enable user-env-compile

Set the `NODE_ENV` environment variable (e.g. `development` or `production`):

    heroku config:set NODE_ENV=production

Push to heroku

    git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom buildpack... done
    -----> Node.js app detected
    -----> Resolving engine versions
           Using Node.js version: 0.8.2
           Using npm version: 1.1.41
    -----> Fetching Node.js binaries
    -----> Vendoring node into slug
    -----> Installing dependencies with npm
           ...
           Dependencies installed
    -----> Building runtime environment
    -----> Found gruntfile, running grunt heroku task
    Running "heroku" task
    ...
    -----> Discovering process types

Debugging
---------

npm can be run with a verbose flag to help debugging if something fails when installing the dependencies. 

* if the `VERBOSE` environment variable is set, npm is always run with verbose logging.
* if `BUILDPACK_RETRY_VERBOSE` is set, npm is relaunched in verbose mode if npm failed.

Thanks to [mackwic](https://github.com/mackwic) for these extensions.

Further Information
-------------------

For more information about using Node.js and buildpacks on Heroku, see these Dev Center articles:

- [Heroku Node.js Support](https://devcenter.heroku.com/articles/nodejs-support)
- [Getting Started with Node.js on Heroku](https://devcenter.heroku.com/articles/nodejs)
- [Buildpacks](https://devcenter.heroku.com/articles/buildpacks)
- [Buildpack API](https://devcenter.heroku.com/articles/buildpack-api)
- [Grunt: a task-based command line build tool for JavaScript projects](http://gruntjs.com/)
