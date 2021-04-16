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

```javascript
const express = require("express");
const session = require("express-session");
const passport = require("./config/passport");

const PORT = process.env.PORT || 8080;
const db = require("./models");

const app = express();

app.use(express.urlencoded({ extended: true }));
app.use(express.json());
app.use(express.static("public"));
app.use(session({ secret: "keyboard cat", resave: true, saveUninitialized: true }));
app.use(passport.initialize());
app.use(passport.session());

require("./routes/html-routes.js")(app);
require("./routes/api-routes.js")(app);

db.sequelize.sync().then(() => {
  app.listen(PORT, () => {
    console.log("==> 🌎  Listening on port %s. Visit http://localhost:%s/ in your browser.", PORT, PORT);
  });
});
```



```javascript
const passport = require("passport");
const LocalStrategy = require("passport-local").Strategy;
const db = require("../models");

passport.use(new LocalStrategy(
  {
    usernameField: "email"
  },
  function(email, password, done) {
    db.User.findOne({
      where: {
        email: email
      }
    }).then((dbUser) => {
      if (!dbUser) {
        return done(null, false, {
          message: "Incorrect email."
        });
      }
      else if (!dbUser.validPassword(password)) {
        return done(null, false, {
          message: "Incorrect password."
        });
      }
      return done(null, dbUser);
    });
  }
));

passport.serializeUser((user, cb) => {
  cb(null, user);
});

passport.deserializeUser((obj, cb) => {
  cb(null, obj);
});

module.exports = passport;
```



```javascript
// Requiring bcrypt for password hashing. Using the bcryptjs version as the regular bcrypt module sometimes causes errors on Windows machines
const bcrypt = require("bcryptjs");

module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define("User", {
    email: {
      type: DataTypes.STRING,
      allowNull: false,
      unique: true,
      validate: {
        isEmail: true
      }
    },
    password: {
      type: DataTypes.STRING,
      allowNull: false
    }
  });

  User.prototype.validPassword = (password) => {
    return bcrypt.compareSync(password, this.password);
  };

  User.addHook("beforeCreate", (user) => {
    user.password = bcrypt.hashSync(user.password, bcrypt.genSaltSync(10), null);
  });
  return User;
};
```