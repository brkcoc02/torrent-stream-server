# Torrent Stream Server

[![Docker](https://github.com/KiraLT/torrent-stream-server/workflows/Docker/badge.svg?branch=master)](https://github.com/users/KiraLT/packages/container/package/torrent-stream-server)
[![CodeQL](https://github.com/KiraLT/torrent-stream-server/workflows/CodeQL/badge.svg?branch=master)](https://github.com/KiraLT/torrent-stream-server/actions?query=workflow%3ACodeQL)
[![Dependencies](https://david-dm.org/KiraLT/torrent-stream-server.svg)](https://david-dm.org/KiraLT/torrent-stream-server)
[![npm version](https://badge.fury.io/js/torrent-stream-server.svg)](https://www.npmjs.com/package/torrent-stream-server)

**Whats new:**

* Search & stream torrents - [preview](https://i.imgur.com/kSXYGrm.png)
* Stream any file from the torrent - [preview](https://i.imgur.com/qRmicai.png)
* Monitor activity - [preview](https://i.imgur.com/aPTcl9P.png)

HTTP server to convert any torrent to video stream

![Demo](https://i.imgur.com/mIzSYWV.png)

## Installation

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/KiraLT/torrent-stream-server)

### NPM package:

* `npm install -g torrent-stream-server`
* `torrent-stream-server serve`
* Go to http://127.0.0.1:3000

## Development

One-click ready-to-code development environments:

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/KiraLT/torrent-stream-server)

> Frontend & backend are separate packages.

So during developemnt you will need to run two dev servers with live reload:

* `npm run dev-backend` - start dev server on `3000` port
* `npm run dev-frontend` - start dev server on  `3001` port

## Commands

* `npm install` - will install both: frontend & backend
* `npm run build` - will build backend to `lib` directory & frontend to `frontend/build`
* `npm run start` - start HTTP server with frontend support
* `npm run dev-backend` - start `backend` server with live reload on `3000` port
* `npm run dev-frontend` - start `frontend` server with live reload on `3001` port

## Configuration

### ENV variables

_Check bellow for descriptions_

* `PORT`
* `HOST`
* `TRUST_PROXY`

> Configuration file can overwrite ENV variables

### File

You can pass JSON config file to any run command (e.g. `npm run start config.json`)

Available parameters:

* **host** - server host. Default is `process.env.HOST` or `0.0.0.0`
* **port** - server port. Default `process.env.PORT` or 3000.
* **trustProxy** - get ip from `X-Forwarded-*` header, [check more](https://expressjs.com/en/guide/behind-proxies.html), Default is `true` if running inside App Engine or Heroku else `false`
* **logging**
  * **level** - debug, info, warn or error. Default info.
  * **transports** - `{"type": "console"}` or `{"type": "loggly","subdomain": "my-subdomain","token": "abc","tags":["my-tag"]}`. Default `console`.
* **torrents**
  * **path** - torrents storage path. Default `/tmp/torrent-stream-server`.
  * **autocleanInternal** - how many seconds downloaded from last stream torrent is kept before deleting. Default is 1 hour. 
* **security**
  * **streamApi** - API is disabled when using this option unless `apiKey` is set.
    * **key** - JWT token.
    * **maxAge** - the maximum allowed age for tokens to still be valid.
  * **frontendEnabled** - enable demo page. Default is `true`.
  * **apiKey** - key which should be passed to headers to access the API (`authorization: bearer ${apiKey}`).

## API

API uses [swagger.yaml](https://kiralt.github.io/torrent-stream-server/docs/swagger.html) to:

1. To generate API documentation page, which can be accesed when using `npm run dev` on http://127.0.0.1:3000/api-docs.
2. To generate frontend client (`frontend/src/helpers/client`)
3. To generate backend models (`src/models`)

> [Check documentation](https://kiralt.github.io/torrent-stream-server/docs/swagger.html)

## Example

Running the following command from a shell will run VLC and start playing the Sintel movie stream from its public torrent:
```
$ vlc "http://localhost:3000/stream/08ada5a7a6183aae1e09d831df6748d566095a10"
```

## Security

API can be protected with `security.apiKey`, stream api can have additional [JSON Web Token](https://jwt.io/) (configurable via `security.streamApi.key`).

If only only one key specified in the config, it will be used as both: `API key` & `streamApi.key`.

### API protection

When api or stream key is enabled, each API call will require `Authorization` header:

```js
Authorization: Bearer <token>
```

### Generate stream API URL

```js
import { sign } from 'jsonwebtoken'

const url = `/stream/${encodeURIComponent(
    sign(
        {
            torrent: '08ada5a7a6183aae1e09d831df6748d566095a10',
            fileType: 'video',
        },
        key
    )
)}`
```

This API will have encoded parameters, so it's safe to share it publicly. It will automatically expire (configurable via `security.streamApi.maxAge`)
