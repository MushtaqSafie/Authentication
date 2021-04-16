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

> Using passwort and passort-local you will be able to authenticate rquests, and also using bcryptjs library helps you to hash password and then store it in database.

> Sequelize is a promise-based Node.js ORM tool for Postgres, MySQL, MariaDB, SQLite and Microsoft SQL server. For this application we will be using it with mysql2 to manage MySQL database.


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
### Folder Structure
- `config` store the database configration and passport authentication.
- `models` define the structure of MySQL database using sequlize.
- `public` front-end file that display application interface to clients.
- `routes` define the API and HTML routes of the application.
<!-- 
- `config.json` 
- `isAuthenticated.js` 
- `passport.js` 
- `index.js` 
- `user.js` 
- `api-routes.js` 
- `html-routes.js` 
- `server.js`  -->