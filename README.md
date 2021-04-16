# Authentication
This tutorial's core objective is to study how to set up real-world authentication in the Node.js Express app. 
We will be using a passport to manage user authentication and protect a client's routes that consume an API.

## Node.js Frameworks
Node.js frameworks that are been used in this application are
1. Express
2. Express-session
3. bcryptjs
4. passport
5. passport-local
6. mysql2
7. sequelize

> Express is a back-end Node.js framework for for web application that manage routing, and HTTP requests, etc & Express-session is require to manage cookie.



## Authentication Project Structure
Let’s look at the project directory tree

```bash
Authentication
├─ config
│  ├─ config.json
│  ├─ middleware
│  │  └─ isAuthenticated.js
│  └─ passport.js
├─ models
│  ├─ index.js
│  └─ user.js
├─ public
│  ├─ js
│  │  ├─ login.js
│  │  ├─ members.js
│  │  └─ signup.js
│  ├─ login.html
│  ├─ members.html
│  ├─ signup.html
│  └─ stylesheets
│     └─ style.css
├─ routes
│  ├─ api-routes.js
│  └─ html-routes.js
├─ package.json
└─ server.js
```

- `config.json` 
- `isAuthenticated.js` 
- `passport.js` 
- `index.js` 
- `user.js` 
- `api-routes.js` 
- `html-routes.js` 
- `server.js` 