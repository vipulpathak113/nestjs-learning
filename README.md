# NestJS
**Sending Response :**
```js
//Without response injection
@Controller('/users')
@Post('/profile')
// here we have injected only request and not response then we can send response of the api only by returning from the function
getProfile(@Req req: Request){
  return {hello:"world"}
}


// With response injection
@Controller('/users')
@Post('/profile')
// here we have injected Response, it means we have to use Response object to send the response of the API. If we use return statement then API reponse will not be returned
getProfile(@Req req: Request,@Res res:Response){
  res.json({hello:"world"})
}

// With response injection and return 
@Controller('/users')
@Post('/profile')
// here we have injected Response, if we have to return API response using return statement then we have to use {passthrough:true}
getProfile(@Req req: Request,@Res({passthrough:true}) res:Response){
   return {hello:"world"}
}
```
---
**Redirect Response** :
- **Static Redirection:** Using **@Redirect** decorator
```js
@Controller('/users')
@Post('/profile')
@Redirect('/users/account')
getProfile(@Req req: Request,@Res({passthrough:true}) res:Response){
   return {hello:"world"}
}
```
- **Dynamic Redirection:** Using **url** in return statement
```js
@Controller('/users')
@Post('/profile')
getProfile(@Req req: Request,@Res({passthrough:true}) res:Response){
   return {url:/users/account,
           statusCode: 302
   }
}
```

**Route Parameters**
```js
@Controller('/users')
@Get('/profile/:id')
getProfile(@Param() params){
//here we do not pass anything in @Param then params will return an object with key as id and value as passed in API

      return {hello:"world"}
}
```

```js
@Controller('/users')
@Get('/profile/:id')
getProfile(@Param('id') params){
//here the params will be the value of id directly and not the object

      return {hello:"world"}
}
```
---
**Headers:**
**@Header:** To set the header
```js
@Controller('/users')
@Get('/profile/:id')
@Header('Cache-Control','none')
getProfile(@Param('id') params){
      return {hello:"world"}
}
```
**@Headers:** To extract the headers.
```js
@Controller('/users')
@Get('/profile/')
getProfile(@Headers() headers: Record<string:any>){
      return {hello:"world"}
}
```
