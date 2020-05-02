---
title: "Asynchronous operation in Javascript: Async/Await"
date: 2018-09-23T16:35:35-04:00
tags: ["JavaScript", "Fundamentals"]
---

## Asynchronous programming with Async/Await:

This is the second post in the multi part series about understanding and using async/await. The second part tries to cover the asynchronous programming using async/await.

### Simple asynchronous function using `Promise`

Imagine a piece of functionality that fetches the data and based on the returned result it get has to decide weather to get more information or return the current result

```javascript

const simpleRequest = () => {
  return getSomeData()
    .then(data => {
      if (data.isIncomplete) {
        return secondRequest(data)
          .then(moreData => moreData)
      } else {
        return data
      }
    })
}
```

As you may have already noticed, this is not an easy code to read or understand. Something which isn't easy to understand by other *human* programmers can quickly become buggy and hard to maintain. Now, let's rewrite above code with `async/await`

```javascript
async function simpleRequest(){
const data = await getSomeData();

if(data.isIncomplete){
    const someMoreData = await secondRequest(data);
    return someMoreData
}

return data;
}
```

The above code is much more easier to read and understand. This is the biggest advantage of async/await. It can make asynchronous code *look* like synchronous code. 

### Summary
In conclusion, there are the main advantages of using `async/await` over `Promise` for writing asynchronous code

1. Concise & Clean code 
2. As showed above, better conditional handling
3. Better error handling because now both synchronous and asynchronous errors can be handled with same construct - `try/catch`

