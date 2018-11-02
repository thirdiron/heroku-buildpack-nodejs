# Heroku Buildpack for Node.js

This is a minor edit of the the official [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for Node.js apps. We forked the repository in order to provide a simple solution to allow pulling npm modules from private github repositories without checking in passwords or other sensitive credentials into source control

How it Works Differently
------------------------
This buildpack looks for a specific config value set through heroku config: ```$GIT_SSH_KEY```. If present, the buildpack expects the base64 encoded contents of a private key whose public key counterpart has been registered with github on a github account with access to any private repositories needed by the application.  It decodes the contents into a file, launches ssh-agent and registers that keyfile, prior to executing ```npm install```.  Once npm install is finished, it cleans up the environment and file system of the key contents.

How to Use:
-----------
* Generate a key: ```ssh-keygen -t rsa -C "your_email@example.com"``` (Enter no passphrase. This buildpack does not support keys with passphrases)
* Add the public key to github: ```pbcopy < ~/.ssh/id_rsa.pub``` and paste the results into the github admin
* Add the private key to your heroku app's config: ```cat id_rsa | base64 | pbcopy```, then ```heroku config:set GIT_SSH_KEY=<paste_here> --app your-app-name```
* Setup your app to use this buildpack as described below



How it Works Identically to the Official Buildpack
--------------------------------------------------

## Documentation

For more information about using this Node.js buildpack on Heroku, see these Dev Center articles:

- [Heroku Node.js Support](https://devcenter.heroku.com/articles/nodejs-support)
- [Getting Started with Node.js on Heroku](https://devcenter.heroku.com/articles/nodejs)

For more general information about buildpacks on Heroku:

- [Buildpacks](https://devcenter.heroku.com/articles/buildpacks)
- [Buildpack API](https://devcenter.heroku.com/articles/buildpack-api)

## Locking to a buildpack version

In production, you frequently want to lock all of your dependencies - including
buildpacks - to a specific version. That way, you can regularly update and
test them, upgrading with confidence.

First, find the version you want from
[the list of buildpack versions](https://github.com/heroku/heroku-buildpack-nodejs/releases).
Then, specify that version with `buildpacks:set`:

```
heroku buildpacks:set https://github.com/heroku/heroku-buildpack-nodejs#v83 -a my-app
```

If you have trouble upgrading to the latest version of the buildpack, please
open a support ticket at [help.heroku.com](https://help.heroku.com/) so we can assist.

### Chain Node with multiple buildpacks

This buildpack automatically exports node, npm, and any node_modules binaries
into the `$PATH` for easy use in subsequent buildpacks.

## Feedback

Having trouble? Dig it? Feature request?

- [help.heroku.com](https://help.heroku.com/)
- [@jeremymorrell](http://twitter.com/jeremymorrell)
- [GitHub issues](https://github.com/heroku/heroku-buildpack-nodejs/issues)

## Hacking

To make changes to this buildpack, fork it on GitHub.
Push up changes to your fork, then create a new Heroku app to test it,
or configure an existing app to use your buildpack:

```
# Create a new Heroku app that uses your buildpack
heroku create --buildpack <your-github-url>

# Configure an existing Heroku app to use your buildpack
heroku buildpacks:set <your-github-url>

# You can also use a git branch!
heroku buildpacks:set <your-github-url>#your-branch
```

## Tests

The buildpack tests use [Docker](https://www.docker.com/) to simulate
Heroku's Cedar-14 and Heroku-16 containers.

To run the test suite:

```
make test
```

Or to just test in cedar or cedar-14:

```
make test-cedar-14
make test-heroku-16
```

The tests are run via the vendored
[shunit2](https://github.com/kward/shunit2)
test framework.
