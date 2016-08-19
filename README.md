# Recast.AI - SDK Node.js

[logo]: https://github.com/RecastAI/SDK-NodeJs/blob/master/misc/logo-inline.png "Recast.AI"

![alt text][logo]

Recast.AI official SDK in Node.js

## Synospis

This module is a Node.js interface to the [Recast.AI](https://recast.ai) API. It allows you to make request to your bots

## Installation

```bash
npm install --save recastai
```
## Usage

```javascript
var recastai = require('recastai')

var client = new recastai.Client(YOUR_TOKEN, YOUR_LANGUAGE)

client.textRequest(YOUR_TEXT, function(res, err) {
  if (err) {
    // Handle error
  } else if (res.intent() === YOUR_INTENT) {
    // Do your code...
  }
})
```

## Specs

### Classes

This module contains 5 classes, as follows:

* Client is the client allowing you to make requests.
* Response contains the response from [Recast.AI](https://recast.ai).
* Intent represents an intent of the response
* RecastError is the error returned by the module.

Don't hesitate to dive into the code, it's commented ;)

## class Client

The Client can be instanciated with a token and a language (both optional).

```javascript
var client = new recastai.Client(YOUR_TOKEN, YOUR_LANGUAGE)
```

__Your tokens:__

[token]: https://github.com/RecastAI/SDK-NodeJs/blob/master/misc/recast-ai-tokens.png "Tokens"

![alt text][token]

*Copy paste your request access token from your bot's settings.*

__Your language__

```javascript
var client = new recastai.Client(YOUR_TOKEN, 'en')
```
*The language is a lowercase 639-1 isocode.*

## Text Request

textRequest(text, callback, options = { token: YOUR_TOKEN, language: YOUR_LANGUAGE })

If your pass a token or a language in the options parameter, it will override your default client language or token.

```javascript
client.textRequest(YOUR_TEXT, function(res, err) {
    // Do your code...¯

})
```

```javascript
// With optional parameters

client.textRequest(YOUR_TEXT, function(res, err) {
  // Do your code...

}, { token: YOUR_TOKEN, language: YOUR_LANGUAGE })
```

__If a language is provided:__ the language you've given is used for processing if your bot has expressions for it, else your bot's primary language is used.

__If no language is provided:__ the language of the text is detected and is used for processing if your bot has expressions for it, else your bot's primary language is used for processing.

## File Request

fileRequest(file, callback, options = { token: YOUR_TOKEN, language: YOUR_LANGUAGE })

If your pass a token or a language in the options parameter, it will override your default client language or token.

__file format: .wav__

```javascript
client.fileRequest('myFile.wav', function(err, res) {
  // Do your code...

})
```

```javascript
client.fileRequest('myFile.wav', function(err, res) {
  // Do your code...

}, { token: YOUR_TOKEN, language: YOUR_LANGUAGE })
```

__If a language is provided:__
Your bot's primary language is used for processing as we do not provide language detection for speech.

__If no language is provided:__
The language you've given is used for processing if your bot has expressions for it, else your bot's primary language is used

## class Response

The Response is generated after a call to either fileRequest or textRequest.

### Get the first detected intent

| Method        | Params | Return                    |
| ------------- |:------:| :-------------------------|
| intent()      |        | the first detected intent |

```javascript
client.textRequest(YOUR_TEXT, function(res, err) {

    var intent = res.intent()

    if (intent === 'greetings') {
      // Do your code...

    }

})

```

### Get the sentence

| Method        | Params | Return             |
| ------------- |:------:| :------------------|
| sentence()    |        | the first sentence |

```javascript
client.textRequest(YOUR_TEXT, function(res, err) {

    var firstSentence = res.sentence()

})
```

### Get one entity

| Method        | Params        | Return                    |
| ------------- |:-------------:| :-------------------------|
| get(name)     | name: String  | the first Entity matched  |


```javascript
client.textRequest(YOUR_TEXT, function(res, err) {

    var location = res.get('location')

})
```

### Get all entities matching name

| Method        | Params        | Return                    |
| ------------- |:-------------:| :-------------------------|
| all(name)     | name: String  | all the Entities matched  |

```javascript
client.textRequest(YOUR_TEXT, function(res, err) {

    var locations = res.all('location')

})
```

### Act helper

| Method        | Params | Return                                  |
| ------------- |-------:| :---------------------------------------|
| isAssert()    |        | wheither or not the act is an assertion |
| isCommand()   |        | wheither or not the act is a command    |
| isWhQuery()   |        | wheither or not the act is a wh-query   |
| isYnQuery()   |        | wheither or not the act is a yn-query   |

### Type helper

| Method           | Params | Return                                                     |
| ---------------- |-------:| :----------------------------------------------------------|
| isAbbreviation() |        | wheither or not the sentence is asking for an abbreviation |
| isEntity()       |        | wheither or not the sentence is asking for an entity       |
| isDescription()  |        | wheither or not the sentence is asking for a description   |
| isHuman()        |        | wheither or not the sentence is asking for a human         |
| isLocation()     |        | wheither or not the sentence is asking for a location      |
| isNumber()       |        | wheither or not the sentence is asking for a number        |

### Sentiment helper

| Method        | Params | Return                                    |
| ------------- |-------:| :-----------------------------------------|
| isPositive()  |        | wheither or not the sentiment is positive |
| isNegative()  |        | wheither or not the sentiment is negative |
| isNeutral()   |        | wheither or not the sentiment is neutral  |

### Attributes

Each of the following methods corresponds to a Response attribute

| Attributes  | Type                                                |
| ----------- | :---------------------------------------------------|
| raw         | String: the raw unparsed json response              |
| type        | String: the type of the processed sentence          |
| act         | String: the act of the processed sentence           |
| sentiment   | String: the sentiment of the processed sentence     |
| source      | String: the user input                              |
| intents     | Array[object]: all the matched intents              |
| sentences   | Array[Sentence]: all the detected sentences         |
| status      | String: the status of the response                  |
| version     | String: the version of the json                     |
| timestamp   | String: the timestamp at the end of the processing  |

## class Entity

The Entity is generated by the Sentence constructor.

### Attributes 

Each of the following methods corresponds to a Response attribute

| Attributes  | Description                                                   |
| ----------- |:--------------------------------------------------------------|
| name        | String: the name of the entity                                |
| raw         | String: the unparsed json value of the entity                 |

In addition to those methods, more attributes are generated depending of the nature of the entity.
The full list can be found there: [man.recast.ai](https://man.recast.ai/#list-of-entities)

```javascript
client.textRequest(YOUR_TEXT, function(res, err) {

    var location = res.get('location')

    console.log(location.raw)
    console.log(location.name)
})
```

## class RecastError

The Recast.AI Error is thrown when receiving an non-200 response from Recast.AI.

As it inherits from Error, it implements the default Error methods.

## More

You can view the whole API reference at [man.recast.ai](https://man.recast.ai).

## Author

Jérôme Houdan, jerome.houdan@recast.ai

You can follow us on Twitter at [@recastai](https://twitter.com/recastai) for updates and releases.

## License

Copyright (c) [2016] [Recast.AI](https://recast.ai)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
