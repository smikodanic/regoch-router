# Regoch Router
> Regoch Router is fast router with no dependencies for NodeJS and browser environment.
Routing is process that determines which function will be executed on the fly. Decision depends on URI. For example:
context.uri = '/user/register' will execute user registration function.

Minimalistic but very powerful library inspired by ExpressJS.

## Installation
```
npm install --save regoch-router
```

## Dependencies
No dependencies.


## Website
[www.regoch.org](http://www.regoch.org/libs/router)


## Features
- intuitive API simmilar to ExpressJS
- redirection to another route
- not found route
- regular expressions in route def
- parse URI parameters (trx.params)
- parse URI query string (trx.query)
- convert JSON string in the object, for example: *{"uri": "/shop/prod?myJson={\"qty\": 22}"}*
- automatically convert to number or boolean



## Integration
```javascript
// NodeJS or browserify
const rr = require('regoch-router');

// Browser (client side)
<script src="./Regoch.js"></script>
<script>
const rr = window.regochRouter;
</script>
```


## API
- **rr.trx** - set the transitional variable
- **rr.def(route, ...funcs)**  - define functions which will be executed on certain route
- **rr.redirect(fromRoute, toRoute)**  - redirect from one route to another route
- **rr.notfound(...funcs)**  - execute functions when no route is matched
- **rr.do(...funcs)**  - execute functions every time
- **rr.exe()**  - execute the router definitions


## Transitional Variable "trx"
```javascript
{
  uri:string,
  body:any,
  uriParsed: {path: string, segments:number, queryString:string, queryObject:any},
  routeParsed: {full:string, segments:number, base:string},
  query:any,
  params:any
}
```

trx example
```
{
  uri: '/shop/register/john/23/true',
  body: {},
  uriParsed: {
    path: 'shop/register/john/23/true',
    segments: 5,
    queryString: undefined,
    queryObject: {}
  },
  routeParsed: {
    full: 'shop/register/:name/:year/:employed',
    segments: 5,
    base: 'shop/register'
  },
  query: {},
  params: { name: 'john', year: 23, employed: true }
}

```


## Examples
```
{"uri": "/"} - root route
{"uri": "/shop/list", "body": [{"id": 12}, {"id": 13}, {"id": 14}]} - exact match
{"uri": "/shop/login?username=peter&password=pan"} - query string
{"uri": "/shop/prod?myJson={\"qty\": 22}"} - converts JSON string in the object
{"uri": "/shop/getnames/12345"} - match regular expression router.def('/shop/get.+/[0-9]+', fja)
{"uri": "/shop/users/matej/44"} - route with parameter
{"uri": "/shop/register/john/23/true?x=123&y=abc&z=false", "body": {"nick": "johnny"}} -route with parameter and query string
{"uri": "/shop/shops/www/CloudShop/1971"} - regular expression router.def('/shop/shop(s)?/w{3}/:name/:year', fja)
{"uri": "/shop/shop/www/CloudShop/1972"} - regular expression router.def('/shop/shop(s)?/w{3}/:name/:year', fja)
{"uri": "/shop/shop/5/BetaShop/1978/red"} - regular expression with integer router.def('/SHOP/shop/\\d+/:name/:year/:color', fja)
{"uri": "/shop/shop/567/Betashop/1979/green"} - regular expression with integer router.def('/SHOP/shop/\\d+/:name/:year/:color', fja)
{"uri": "/shop/shop567/{\"a\": 22}"} - convert JSON in the object

{"uri": "/someurl"} - redirect router.redirect('/someurl', '/')

{"uri": "/shop/badurl"} - notfound
{"uri": ""} - notfound
```



### Licence
“Freely you received, freely you give”, Matthew 10:5-8

Copyright (c) 2020 Saša Mikodanić licensed under [AGPL-3.0](./LICENSE) .
