# O'Reilly apib2swagger fork information and usage

This fork of the apib2swagger library is being done as a stopgap to enable some
internal tooling while we determine how to transition to native OpenAPI
documentation.

There were some slight issues we noticed with the output of the main
library, it has not been updated for a while (2 years at time of writing of
this comment), and the api blueprint project itself has been archived.
So it seemed more expeditious to just make a fork for our quick fixes.

At the moment, we have not added our org CI/CD tooling to make this forked
version more widely available via `npm`.

As such, if you need to use this version of apib2swagger instead of the standard
npm-provided one, the current process is to run `npm ci` then `npm pack` in this
repo locally and then take the generated `tgz` file, copy it over to the repo
you need to use it in, and run `npm i <path-to-local-tgz-file>`.

If there's sufficient demand to make this version more widely available within
the org, we can add the tooling to make it installable by standard means; but
for now it's meant to be used temporarily in one place for internal tools.

What follows is the documentation for the main apib2swagger library. If you have
any questions, please reach out to the systems-engineering team.

## apib2swagger

![Build Status](https://github.com/kminami/apib2swagger/actions/workflows/nodejs.yml/badge.svg)
[![Coverage Status](https://coveralls.io/repos/github/kminami/apib2swagger/badge.svg?branch=master)](https://coveralls.io/github/kminami/apib2swagger?branch=master)
[![npm version](https://badge.fury.io/js/apib2swagger.svg)](https://badge.fury.io/js/apib2swagger)

Convert [API Blueprint](https://apiblueprint.org/) to [Swagger 2.0](http://swagger.io/) or [OpenAPI 3.0](https://github.com/OAI/OpenAPI-Specification).

Supported versions:

- API Blueprint 1A9
  - [Metadata section](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#def-metadata-section)
        - HOST -> .host, .basePath, .schemes
        - VERSION -> .info.version
    - [Include directive](https://github.com/danielgtaylor/aglio#including-files)
- Swagger 2.0
- OpenAPI 3.0.3
- Node.js 18.x, 20.x or higher

## Install

```shell
npm install -g apib2swagger
```

## Usage

Convert to Swagger specification.

```shell
apib2swagger -i api.md
apib2swagger -i api.md -o swagger.json
apib2swagger -i api.md --yaml -o swagger.yaml
apib2swagger -i api.md --prefer-reference
apib2swagger -i api.md --bearer-apikey
apib2swagger -i api.md --open-api-3
apib2swagger -i api.md --info-title "My API Document Title"
apib2swagger -i api.md --prefer-file-ref
```

Without -i option it reads from STDIN, without -o option writes to STDOUT.

```shell
apib2swagger < api.md > swagger.json
cat api.md | apib2swagger
```

Run http server with SwaggerUI.
SwaggerUI will be automatically downloaded to current dir.

```shell
$ apib2swagger -i api.md -s
$ apib2swagger -i api.md -s -p 3000

# When using file references and running the SwaggerUI server, you can specify the source
# directory with the -sd flag. It will check the input directory and execution directory
# if -sd is not given.
$ apib2swagger -i api.md -s --prefer-file-ref -sd ~/project/src/
```

Use as a library.

```javascript
var apib2swagger = require('apib2swagger'),
    apib = '...',
    options = {
        preferReference: true,

        // optional (Swagger 2.0 only).
        bearerAsApikey: false,

        // optional. swagger 2.0 is used by default.
        openApi3: true,

        // optional. title will be grabbed from blueprint if not specified.
        infoTitle: 'My API Document Title',

        // optional (Open API 3 only).
        // will set a $ref to the given file path instead of including the file contents.
        preferFileRef: true
    };

apib2swagger.convert(apib, options, function (error, result) {
    if (!error) console.log(result.swagger);
});
```

## npx

You can run apib2swagger via `npx` (without first needing to install it) like so:

```shell
cat api.md | npx apib2swagger > swagger.json
```

## Docker

You can also run apib2swagger inside a docker container.

```bash
docker run -it --rm -v $(pwd):/docs ghcr.io/kminami/apib2swagger -i /docs/api.md -o /docs/swagger.json
```

## License

Copyright (c) 2021 Keisuke Minami

MIT
