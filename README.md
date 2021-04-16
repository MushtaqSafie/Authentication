# Authentication
This tutorial's core objective is to study how to set up real-world authentication in the Node.js Express app. 
We will be using a passport to manage user authentication and protect a client's routes that consume an API.

## Node.js Frameworks
Node.js frameworks that are been used in this application are
* express
> a back end web application framework for Node.js
* express-session
> a back end web application framework for Node.js
* bcryptjs
* passport
* passport-local
* mysql2
* sequelize


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