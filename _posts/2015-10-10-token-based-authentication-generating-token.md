---
layout: post
title: Token-based Authentication&#58; Generating Token
---

When it comes to authentication between frontend client and backend API without using an outside service (for example, Facebook or Google OAuth 2.0), the two main methods are <b>Session-based Authentication</b> and <b>Token-based Authentication</b>.

Session-based authentication is still very popular today. However, the newer token-based authentication has been quickly gaining popularity. A few advantages of token-based authentication are: cross-domain flexibility, mobile compatibility, and server scalability.

In order to issue JSON Web Tokens using express as your backend framework, first install the <b>jsonwebtoken</b> module into your project folder. Include the --save flag if you want to automatically add it into your <i>package.json</i> file.

```javascript
npm install jsonwebtoken --save
```

Next, <b>require</b> the jsonwebtoken module into your javascript file, and use the <b>sign</b> function to actually generate the token.

```javascript
var jwt = require('jsonwebtoken');

var issueToken = function(username) {
  return jwt.sign(username, config.secret, {
      expiresIn: '1d'
    });
};
```
In this snippet, the <i>username</i> is included into the token (this is called the <b>payload</b> part of the token), and can be decoded later if needed. You can also use an object literal instead of a string as the payload if you'd like. Be aware, this information is <b>not encrypted</b>, so don't include any sensitive information in it.

<i>config.secret</i> here should be a private key that you store on your server, this information will be used to verify the token in the future.

The <b>sign</b> function also takes a third, optional argument for <b>options</b>. In this example we used the <i>expiresIn</i> property to declare that this token will expire in one day. You can find the other available options [here](https://github.com/auth0/node-jsonwebtoken).

Stay tuned for part 2 of this post series, where we will look at storing and verify the token we issued here!