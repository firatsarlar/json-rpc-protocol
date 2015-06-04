# json-rpc-protocol [![Build Status](https://travis-ci.org/julien-f/js-json-rpc-protocol.png?branch=master)](https://travis-ci.org/julien-f/js-json-rpc-protocol)

> JSON-RPC 2 protocol messages parsing and formatting

## Install

Installation of the [npm package](https://npmjs.org/package/json-rpc-protocol):

```
> npm install --save json-rpc-protocol
```

## Usage

### Errors

```javascript
// ES5
var protocol = require('json-rpc-protocol')

var JsonRpcError = protocol.JsonRpcError
var InvalidJson = protocol.InvalidJson
var InvalidRequest = protocol.InvalidRequest
var MehtodNotFound = protocol.MehtodNotFound
var InvalidParameters = protocol.InvalidParameters

// ES6
import {
  JsonRpcError,
  InvalidJson,
  InvalidRequest,
  MethodNotFound,
  InvalidParameters
} from 'json-rpc-protocol'
```

This is the base error for all JSON-RPC errors:

```javascript
throw new JsonRpcError(message, code)
```

The JSON-RPC 2 specification defined also the following specialized
errors:

```javascript
// Parse error: invalid JSON was received by the peer.
throw new InvalidJson()

// Invalid request: the JSON sent is not a valid JSON-RPC 2 message.
throw new InvalidRequest()

// Method not found: the method does not exist or is not available.
throw new MethodNotFound(methodName)

// Invalid parameters.
throw new InvalidParameters(data)
```

Custom errors can of course be created, they just have to inherit
`JsonRpcError`:

```javascript
// ES5
function MyError () {
  JsonRpcError.call(this, 'my error', 1)
}
MyError.prototype = Object.create(JsonRpcError.prototype, {
  constructor: {
    value: MyError
  }
})

// ES6
class MyError extends JsonRpcError {
  constructor () {
    super('my error', 1)
  }
}
```

### Parsing

```javascript
// ES5
var parse = require('json-rpc-protocol').parse

// ES6
import {parse} from 'json-rpc-protocol'
```

The `parse()` function parses, normalizes and validates JSON-RPC
messages.

These message can be either JS objects or JSON strings (they will be
parsed automatically).

This function may throws:

- `InvalidJson`: if the string cannot be parsed as a JSON;
- `InvalidRequest`: if the message is not a valid JSON-RPC message.

```javascript
parse('{"jsonrpc":"2.0", "method": "foo", "params": ["bar"]}')
// → {
//   [type: 'notification']
//   jsonrpc: '2.0',
//   method: 'foo',
//   params: ['bar']
// }

parse('{"jsonrpc":"2.0", "id": 0, "method": "add", "params": [1, 2]}')
// → {
//   [type: 'request']
//   jsonrpc: '2.0',
//   id: 0,
//   method: 'add',
//   params: [1, 2]
// }

parse('{"jsonrpc":"2.0", "id": 0, "result": 3}')
// → {
//   [type: 'response']
//   jsonrpc: '2.0',
//   id: 0,
//   result: 3
// }
```

> A parsed message has a non enumerable property `type` set to easily
> differentiate between types of JSON-RPC messages.

### Formatting

```javascript
// ES5
var format = require('json-rpc-protocol').format

// ES6
import {format} from 'json-rpc-protocol'
```

The `format.*()` functions can be used to create valid JSON-RPC
message in the form of JS objects. It is up to you to format them in
JSON if necessary.

#### Notification

```javascript
format.notification('foo', ['bars'])
// → {
//   [type: 'notification']
//   jsonrpc: '2.0',
//   method: 'foo',
//   params: ['bar']
// }
```

The last argument, the parameters of the notification is optional and
defaults to `[]`.

#### Request

The second argument, the parameters of the notification is optional and
defaults to `[]`.

The last argument, the identifier of the request is optional and is
generated if missing via an increment.

```javascript
format.request('add', [1, 2], 0)
// → {
//   [type: 'request']
//   jsonrpc: '2.0',
//   id: 0,
//   method: 'add',
//   params: [1, 2]
// }
```

#### Response

A successful response:

```javascript
format.response(0, 3)
// → {
//   [type: 'response']
//   jsonrpc: '2.0',
//   id: 0,
//   result: 3
// }
```

A failed response:

```javascript
var MethodNotFound = require('json-rpc-protocol').MethodNotFound

format.error(0, new MethodNotFound('add'))
// → {
//   [type: 'error']
//   jsonrpc: '2.0',
//   id: 0,
//   error: {
//     code: -3601,
//     message: 'method not found: add',
//     data: 'add'
//   }
// }
```

Note: the error to format must be an instance of `JsonRpcError` or it
will be automatically replaced by an unknown error for security
reasons.

## Development

### Installing dependencies

```
> npm install
```

### Compilation

The sources files are watched and automatically recompiled on changes.

```
> npm run dev
```

### Tests

```
> npm run test-dev
```

## Contributions

Contributions are *very* welcomed, either on the documentation or on
the code.

You may:

- report any [issue](https://github.com/julien-f/js-json-rpc-protocol/issues)
  you've encountered;
- fork and create a pull request.

## License

ISC © [Julien Fontanet](http://julien.isonoe.net)
