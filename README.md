<h1 align="center">
  <img src="https://res.cloudinary.com/eykhagen/image/upload/v1536487016/logo.png" height="300" width="300"/>
  <p align="center" style="font-size: 0.5em">:rocket: Flexible REST Tests</p>
</h1>

<p align="center">
  <img src="https://img.shields.io/badge/License-MIT-yellow.svg" alt="License: MIT">
  <img src="https://img.shields.io/github/package-json/v/eykhagen/strest.svg" alt="License: MIT">
</p>


**:link: Connect multiple requests**: _Example_ Embed an authorization token you got as a response from a login request in your following requests automatically

**:memo: YAML Syntax**: Write all of your tests in YAML files

**:tada: Easy to understand**: You'll understand the concept in seconds and be able to start instantly (seriously!)

## Getting Started

Install Strest using `yarn`
```
yarn global add strest-cli
```
Or via `npm`
```
npm i -g strest-cli
```

To test something, you have to create a REST-API first. If you already have an API to test, you can skip this step.

We'll be using [express](https://github.com/expressjs/express) in this tutorial to create a simple API endpoint but you can
use whatever you want

Let's get started by creating a simple endpoint first
```javascript
const express = require('express');

const app = express();

app.get('/user', (req, res) => {
  res.send({
    username: req.query.name,
    id: 123,
    premium: false,
  })
})

app.listen(3001)
```
Then, create a file called `tutorial.strest.yaml` _(You can name it however you want as long as it ends with `.strest.yaml`)_

```yaml
version: 1                            # only version at the moment

requests:                             # all test requests will be listed here
  userRequest:                        # name the request however you want
    url: http://localhost:3001/user   # required
    method: GET                       # required
    data:                             # valid data types: params, json and raw
      params:
        name: testUser
    log: true                         # false by default. This will log all response information in the console
    
```
_No more configuration needed, so you're ready to go!_

To run the test, open your terminal and type
```
strest tutorial.strest.yaml
```
You may also run multiple test files at the same time by pointing to the directory, where the files are stored
```
strest
// or
strest someDir/
```

Success! If you've done everything correctly, you'll get a response like this
```
[ Strest ] Found 1 test file(s)
[ Strest ] Schema validation: 1 of 1 file(s) passed

✔ Testing userRequest succeeded
Status: 200
Status Text: OK

Headers:

{
  "x-powered-by": "Express",
  "content-type": "application/json; charset=utf-8",
  "content-length": "48",
  "etag": "W/\"30-lW4maan3YXQcTCT4eKF1mDtPx3Y\"",
  "date": "Sun, 09 Sep 2018 11:20:19 GMT",
  "connection": "close"
}

Data:

{
  "username": "testUser",
  "id": 123,
  "premium": false
}


[ Strest ] ✨  Done in 0.046s
```
## Writing .strest.yaml test files
You can find a full __Documentation__ of how to write tests [here](SCHEMA.md)

## Documentation
- [How to write Tests](SCHEMA.md)
- [Validation Types](VALIDATION.md)
- [Examples](examples/)

## Using & Connecting multiple requests

With traditional tools like [Postman](https://www.getpostman.com/) or [Insomnia](https://insomnia.rest/) it's common to perform only a single request at a time. Moreover, you have to trigger each request on your own with a click on a button.

With __Strest__ you're able to predefine a very well structured test file once, and every time you make any changes to your API you can test it with just one command in your terminal. Additionally, you can add hundreds or thousands of requests and endpoints which will run synchronously one after the other.

To create multiple requests, simply add multiple entries into the `requests` yaml object.
```yaml
version: 1

requests:
  requestOne:
    ...
  requestTwo:
    ...
  requestThree:
    ...

```
Running this will result in something like
```
[ Strest ] Found 1 test file(s)
[ Strest ] Schema validation: 1 of 1 file(s) passed

✔ Testing requestOne succeeded
✔ Testing requestTwo succeeded
✔ Testing requestThree succeeded

[ Strest ] ✨  Done in 0.12s
```

### Connecting multiple requests

**What is meant by _connecting multiple requests_?**<br/>
Connecting multiple requests means that you write a request and in each of the following requests you are able to use and insert any of the data that was responded by this request.

**Usage**
```yaml
requests:
  
  login: # will return { token: "someToken" }
    ...

  authNeeded:
    ...
    headers:
      Authorization: Bearer Value(login.token)

```
As you could see, the usage is very simple. Just use `Value(requestName.jsonKey)` to use any of the JSON data that was retrieved from a previous request. If you want to use raw data, just use `Value(requestName)` without any keys. <br><br>
You can use this syntax __*anywhere*__ regardless of whether it is inside of some string like `https://localhost/posts/Value(postKey.key)/...` or as a standalone term like `Authorization: Value(login.token)`

## Response Validation
With **Strest** you can validate respones either by a specific value or by a `Type`. _[List of all valid Types](VALIDATION.md)_

#### Raw Validation
```yaml
requests:
  example:
    ...
    validate:
      raw: "the response has to match this string exactly"
```
#### JSON Validation
```yaml
requests:
  example:
    ...
    validate:
      json:
        user: 
          name: Type(String) # name has to be of type String
          id: Type(Null | Number | String) # id has to be of type Number, String or Null
          iconUrl: Type(String.Url)
        someOtherData: "match this string" 
```
## Errors
**Strest** is a testing library so of course, you'll run into a few errors when testing an endpoint. Error handling is made very simple so can instantly see what caused an error and fix it. 

_Example of a Validation Error_
```
[ Strest ] Found 1 test file(s)
[ Strest ] Schema validation: 1 of 1 file(s) passed

✖ Testing test failed

[ Validation ] The required item test wasn't found in the response data

[ Strest ] ✨  Done in 0.045s
```


## License
Strest is [MIT Licensed](LICENSE)