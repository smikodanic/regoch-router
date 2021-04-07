# Regoch Router
> Regoch Router is fast router with no dependencies for NodeJS and browser environment. Minimalistic but very powerful library.

*Routing is process that determines which function will be executed on the fly. Decision depends on URI. For example:
trx.uri = '/user/register' will execute user registration function.*



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
const Router = require('regoch-router');
const router = new Router({debug: false});


// Browser (client side)
<script src="./Regoch.js"></script>
<script>
const router = new window.regochRouter({debug: false});
</script>
```


## API
- **router.trx** - set the transitional variable
- **router.def(route, ...funcs)**  - define functions which will be executed on certain route
- **router.redirect(fromRoute, toRoute)**  - redirect from one route to another route
- **router.notfound(...funcs)**  - execute functions when no route is matched
- **router.do(...funcs)**  - execute functions every time
- **router.exe()**  - execute the router definitions


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


## Route Example Definitions
```
// set transitional object (which is used in )
  router.trx = trx; // {uri, body, ...}


  ////////////////////// R O U T E S /////////////////////

  /***** REGEX MATCH (NO PARAMS) (_routeRegexMatch) *****/
  /* root route */
  // {"uri": "/"}
  router.def('/', trx => console.log('ROOT-A'), trx => console.log('ROOT-B')); // exact match

  /* exact match */
  // {"uri": "/shop/list", "body": [{"id": 12}, {"id": 13}, {"id": 14}]}
  router.def('/shop/list', rFun1, rFun2); // exact match


  /* examples with uri query string */
  // {"uri": "/shop/login?username=peter&password=pan"}
  router.def('/shop/login', trx => console.log(`LOGIN:: username:${trx.query.username} password:${trx.query.password}`));

  // {"uri": "/shop/prod?myJson={\"qty\": 22}"}   -- parse JSON
  router.def('/shop/prod', trx => console.log(`myJson:: ${JSON.stringify(trx.query.myJson)}`));


  /* examples with regular expression */
  // {"uri": "/shop/getnames/12345"}
  router.def('/shop/get.+/[0-9]+', trx => console.log('REGEXP MATCH'));




  /***** PARAM MATCH (_routeWithParamsMatch) *****/
  // {"uri": "/shop/users/matej/44"}
  router.def('/shop/users/:name/:age', trx => console.log(`name: ${typeof trx.params.name} ${trx.params.name} , age: ${typeof trx.params.age} ${trx.params.age}`));


  /* examples with uri query string */
  // {"uri": "/shop/register/john/23/true?x=123&y=abc&z=false", "body": {"nick": "johnny"}}
  router.def('/shop/register/:name/:year/:employed', trx => console.log(`employed: ${trx.params.employed}`));


  /* examples with regular expression */
  // {"uri": "/shop/shops/www/CloudShop/1971"}
  // {"uri": "/shop/shop/www/CloudShop/1972"}
  router.def('/shop/shop(s)?/w{3}/:name/:year', trx => console.log(`name: ${trx.params.name} year: ${trx.params.year}`));

  //// \\d+ replaces one or more digits (integer numbers)
  // {"uri": "/shop/shop/5/BetaShop/1978/red"}
  // {"uri": "/shop/shop/567/Betashop/1979/green"}
  router.def('/SHOP/shop/\\d+/:name/:year/:color', trx => console.log(`name: ${trx.params.name} year: ${trx.params.year} color: ${trx.params.color}`));


  // {"uri": "/shop/shop567/{\"a\": 22}"}
  router.def('/SHOP/shop\\d+/:myJSON', trx => console.log(`myJSON: ${JSON.stringify(trx.params.myJSON)}`));


  /***** REDIRECTS *****/
  // {"uri": "/someurl"}
  router.redirect('/someurl', '/');

  // {"uri": "/shop/badurl"}
  router.def('/shop/notfound', () => console.log('--SHOP ROUTE Not Found !--')).redirect('/shop/.+', '/shop/notfound'); // redirect any route to the '/notfound' route. SHOULD BE DEFINED LAST



  /***** NO MATCH (bad uri - Error 404) *****/
  // {"uri": ""}
  // {"uri": "/badurl"}
  router.notfound(trx => {console.log('Route not found.'); });




  /***** DO -  always will be executed on each URI *****/
  const f1 = (trx) => { console.log('always f1'); };
  const f2 = (trx) => { console.log('always f2'); };
  router.do(f1, f2);




  /***** EXECUTE ROUTER *****/
  router.exe()
    .then(trx => console.log('then(trx):: ', trx))
    .catch(err => console.log('ERRrouter:: ', err));
```


## Route Example Tests
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

Copyright (c) 2020 Saša Mikodanić licensed under [MIT](./LICENSE) .
