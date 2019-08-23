# JavaScript Errors Demystified

[Link](https://youtu.be/KpeftlLF99c?t=202)

## `window.onerror` & `window.onunhandledrejection`

- available in all browsers and **doesn't work in console explicitly**, has to be a part of the loaded script

```js
window.onerror = function(msg, url, line, col, err){
    // do something with error

    return false;
}
```

- available in chrome ONLY and for `Promise` kinda stuff

```js
window.onunhandledrejection = function(evt){
 // .. do something with the rejection..
 console.log(evt.reason);
}

```

- example `onerror`

```js
window.onerror = function(msg, url, line, col, err){
    console.log("Error message")
    console.log(msg, url, line, col, err)
    return false;
}
function foo(){
    bar();
}
foo();
```

## SAME ORIGIN POLICY

- `onerror` _cannot listen_ to errors thrown by Scripts on or from other `ORIGINS` (scheme + host + port)
- Because `Error messages` could leak session specific data
- SOLUTION: CORS
  - _if loading a script from CDN then use  `crossorigin` attribute on the script-tag_
  
  ```js
    <script crossorigin="anonymous" src="http://..">
  ```

  - _browser will load this file and will not send any cookies_
  - _From server side set header `Access-Control-Allow-Origin: *`, `*` means `any`_
  
  > Does browser send relevant cookies for any GET request to fetch scripts?

- **There is another way to get Error-info on cross-origin script: Use try-catch block**

```js
try{
    foo()
}catch(e){
    console.log(e);
    throw(e); // so onerror could be fired - but onerror will not get any info about the error due Cross_Origin stipulation
}

```

- `Async Instrumentation` and `Change detection` can be done by patching up the following :
  - ***`addEventListener`***
  - ***`requestAnimationFrame`***
  - ***`setTimeout`***
  - ***`setInterval`***
  - ***`fetch`***
  - ***`XMLHttpRequest ( onload, onerror, onprogress  )`***

- `Non-Error Exceptions`
  - _Raising Non-Error Exceptions in JS_
    - _You will only get filename, line and column but no event/error object_
    - _No STACK TRACE_
    - _You can throw a String, an Object_
    - _**YOU GET THE SAME SCENARIO IF REJECTING PROMISE WITH ANYTHING BUT AN ERROR OBJECT**_
    - Caused when throwing any non-error
    - Make determining root cause difficult
    - Promises ar common source of them
    - try/catch can add valuable missing context

## Right way to reject a promise

```js
    Promise.reject(new Error("SOME ERROR VALUE"));
```

- **BUT, Try/Catch block to the rescue - a nice trick**
  - _this will give the stack trace_

 ```js
 try{
     throw 'lol'; // or some code which throws string and objects BUT NOT PROPER ERROR OBJECTS
 }catch (e){
     let err = new Error(e);
     throw err
 }
 ```

## ENVIRONMENT CHALLENGES

- STACK TRACE has no specification so browsers present it in their own unique flavours
- _on Error - different browsers give different line and column numbers_
- _perfect grouping is impossible_

> Collecting meaningful JS Errors from the browser is HARD

- window.onerror is insufficient
- try/catch adds missing context in different scenarios
- JS engine will produce different results

> [what the heck is "Script Error" ?](https://blog.sentry.io/2016/05/17/what-is-script-error)

<hr/>

> [Capture and report error w/ window on Error](https://blog.sentry.io/2016/01/04/client-javascript-reporting-window-onerror)

## TODO

- [ ] Error Object in JS - MDN Docs
