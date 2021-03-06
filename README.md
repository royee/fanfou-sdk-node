# fanfou-sdk-node

[![Greenkeeper badge](https://badges.greenkeeper.io/LitoMore/fanfou-sdk-node.svg)](https://greenkeeper.io/)
[![](https://img.shields.io/travis/LitoMore/fanfou-sdk-node/master.svg)](https://travis-ci.org/LitoMore/fanfou-sdk-node)
[![](https://img.shields.io/appveyor/ci/LitoMore/fanfou-sdk-node/master.svg)](https://ci.appveyor.com/project/LitoMore/fanfou-sdk-node)
[![](https://img.shields.io/npm/v/fanfou-sdk.svg)](https://www.npmjs.com/package/fanfou-sdk)
[![](https://img.shields.io/npm/l/fanfou-sdk.svg)](https://github.com/LitoMore/fanfou-sdk-node/blob/master/LICENSE)
[![](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

Fanfou SDK for Node.js

## Installation

```bash
$ npm install fanfou-sdk
```
## Initialization

```javascript
const Fanfou = require('fanfou-sdk')

// OAuth
const ffOAuth = new Fanfou({
  auth_type: 'oauth',
  consumer_key: consumer_key,
  consumer_secret: consumer_secret,
  oauth_token: oauth_token,
  oauth_token_secret: oauth_token_secret
})

// XAuth
const ffXAuth = new Fanfou({
  auth_type: 'xauth',
  consumer_key: consumer_key,
  consumer_secret: consumer_secret,
  username: username,
  password: password
})
```
**Parameters**

- `consumser_key: String` The Consumser Key
- `consumser_secret: String` The Consumer Secret
- `oauth_token: String` The OAuth Token
- `oauth_token_secret: String` The OAuth Token Secret
- `username: String` The Fanfou username
- `password: String` The Fanfou password

## Usage

**Methods**

```javascript
ff.get(uri, parameters, callback)
ff.post(uri, parameters, callback)
ff.upload(stream, text, callback)
ff.stream(uri)
```

**Parameters**

- `uri: String` URI to be requested upon
- `parameters: Object` Parameters to be submitted
- `stream: String | Buffer` Readable Stream
- `text: String` Status content
- `callback: Function` Method to be invoked upon result

**Examples**

```javascript
// OAuth
ff.get('/statuses/home_timeline', {}, (e, timeline) => {
  if (e) console.log(e.message)
  else {
    console.log(timeline)
  }
})

ff.post('/statuses/update', {status: 'post test'}, (e, status) => {
  if (e) console.log(e.message)
  else {
    console.log(status)
  }
})

ff.upload(fs.createReadStream(path), 'nice day', (e, status) => {
  if (e) console.log(e.message)
  else {
    console.log(status)
  }
})

// XAuth
ff.xauth((e, res) => {
  if (e) console.log(e.message)
  else {
    ff.get('/statuses/public_timeline', {}, (e, timeline) => {
      if (e) console.log(e.message)
      else {
        console.log(timeline)
      }
    })
  }
})
```

**Streaming API**

Fanfou SDK offers the ability to work with the Streaming API, based on the EventEmitter.

```javascript
// To start a streamer
const streamer = ff.stream()

// Now post a new status on fanfou, this will trigger the 'message' event
streamer.on('message', data => {
  console.log(data.schema + ' ' + data.action + ': ' + data.object.text)
  // OUTPUT: message create: hello fanfou
})

// Listen to reply or mention events
streamer.on('message', data => {
  if (data.is_mentioned) {
    console.log('Is mentioned by @' + data.mentioned_by)
    // OUTPUT: Is mentioned by @fanfou
  }

  if (data.is_replied) {
    console.log('Is replied by @' + data.replied_by)
    // OUTPUT: Is replied by @fanfou
  }
})

// Or listen to the favourite events
streamer.on('fav', data => {
  console.log(data.schema + ' ' + data.action + ': ' + data.object.text)
})

// To stop the streamer
streamer.stop()
```

Available events:

|Event|Available Actions|
:---|:---
message|<p>`create`: Current user posts a new status, or receives a reply/mention from another user</p><p>`delete`: Current user deletes a status
user|`updateprofile`: Current user updates the profile</p>
friends|<p>`create`: Current user follows a user</p><p>`delete`: Current user unfollows a user (being unfollowed will not trigger any events)</p><p>`request`: Current user makes a follow request to a user</p>
fav|<p>`create`: Current user favourites a status</p><p>`delete`: Current user deletes a favourite</p>

For the `data` structures of these events respectively please refer to the Fanfou [Streaming API](http://wiki.fanfou.com/Streaming-API) docs.

## Related

- [nofan](https://github.com/LitoMore/nofan) - Fanfou Cli for Node.js
- [alfred-fanfou](https://github.com/LitoMore/alfred-fanfou) - Alfred 3 workflow for Fanfou
- [cerebro-fanfou](https://github.com/LitoMore/cerebro-fanfou) - A cerebro plugin for Fanfou

## License

MIT @ [LitoMore](https://github.com/LitoMore)
