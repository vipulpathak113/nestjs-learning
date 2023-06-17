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

**DEPENDENCY INJECTION (DI):** 
- It uses IOC such that instead of creating the instance manually we give control to nestjs
- To use DI first we have to register it in the module within providers.
 ```js
 @Module({
      providers: [
      { provide: "STORE", useClass: UsersStore
      
       }
         ]
      })
```

To use this anywhere within module we use **@Inject**(*except when the provide is a class itself*) with the **name given in provide**.

```js
@Controller("users")
export class UsersController {
  constructor(@Inject("STORE") private store: UsersStore) {
    console.log(`[UsersController]:`, this.store.getStore());
  }
}
```

Here instance of UserStore is created and used in controller with the name "STORE"

- We can also use **useValue** and **useFactory** for injection**

- **Scope of DI:**
  - **DEFAULT:** It means only one instance will be created
  - **REQUEST**: It means per request of api, instance will be created. But it also has drawback as the anything which consumes the injection, the instance of that thing is also created which becomes heavy
  - **TRANSIENT:** New dedicated instance for each consumer

---
**Types of Modules :** 
- **Feature Modules:** Can be used per feature. We can do that by creating seperate folders feature wise.
- **Shared Modules:** using exports in the module we can share classes 
 ```js
 @Module({
      providers: [
      { provide: "STORE", useClass: UsersStore
      
       }
         ],
        exports: [UserService] 
      })
```
Now wherever we import this module we can inject UserService there.

- **Global Modules:** By using **@Global** we can make a module global and we do not need to import the module to use there classes. But we have to import in the main module to work.
 ```js
 @Global()
 @Module({
      providers: [
      { provide: "STORE", useClass: UsersStore
      
       }
         ],
        exports: [UserService] 
      })
```
- Router module: Provides routes to api within the module. Mention in the app module.
 ```js
 @Global()
 @Module({
      imports:[ JobModule,RouterModule.register([{path:'jobs',module:JobModule}])]
      })
```
We can also use children inside it for route within route.
 ```js
 @Global()
 @Module({
      imports:[ JobModule,RouterModule.register([{path:'jobs',module:JobModule,children:[{path:'finding',module:JobFindingModule}]}])]
      })
      //here path becomes jobs/finding 
```
