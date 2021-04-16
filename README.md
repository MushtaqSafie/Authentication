# Authentication
This tutorial's core objective is to study how to set up real-world authentication in the Node.js Express app. 
In this application we will be using a passport to manage user authentication and protect a client's routes that consume an API.

## Project files/folders Structure
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
- `config` contains the database configration and passport authentication.
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

## Setup Node.js Modules
After setting the the folders structure, open command prompt and `cd` to current directory of the project. 

Run the following command to initizlize a new npm package:
```bash
npm init
```
`npm init` is initializer to set up a new npm package.


Then install Express, express-session, bcryptjs, passport, passport-local, mysql2, and sequelize using the following command:

```bash
npm install express express-session bcryptjs passport passport-local mysql2 sequelize
```

### Node.js Frameworks
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

## Configure MySQL database & Sequelize
In the **config** folder create a `config.json` file and add the MySQL configuration which is like this:
```javascript
{
  "development": {
    "username": "root",
    "password": null,
    "database": "passport_demo",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}
```
This file include database configuration for **development**, **test**, and **production** mode, that each has object.key for:

* `username` the username of database.
* `password` the password of database user.
* `database` the name of the database.
* `host` the port of the database that is being hosted.
* `dialect` the type of database.

Above values for each object.key might be different, depends on your database configration.

For more information about Sequelize Configuration visit [Sequelize Configuration](https://sequelize.org/master/manual/migrations.html)

## Initialize Sequelize
Now we initialize Sequelize in `models` folder in `index.js` file.
```javascript
const fs = require("fs");
const path = require("path");
const Sequelize = require("sequelize");
const basename = path.basename(__filename);
const env = process.env.NODE_ENV || "development";
const config = require(__dirname + "/../config/config.json")[env];
const db = {};
let sequelize;
if (config.use_env_variable) {
  sequelize = new Sequelize(process.env[config.use_env_variable], config);
} else {
  sequelize = new Sequelize(
    config.database,
    config.username,
    config.password,
    config
  );
}
fs.readdirSync(__dirname)
  .filter(file => {
    return (
      file.indexOf(".") !== 0 && file !== basename && file.slice(-3) === ".js"
    );
  })
  .forEach(file => {
    const model = require(path.join(__dirname, file))(
      sequelize,
      Sequelize.DataTypes
    );
    db[model.name] = model;
  });
Object.keys(db).forEach(modelName => {
  if (db[modelName].associate) {
    db[modelName].associate(db);
  }
});
db.sequelize = sequelize;
db.Sequelize = Sequelize;

module.exports = db;
```
This file initialize the MySQL database using Sequelize and create all models or table that exict in the `models` folder.
In our case we have `user.js` model and create a table to store the user's email address and password.

This file is provide by Sequelize and for detailed information about Initialize Sequelize [Getting Started with Sequelize](https://sequelize.org/master/manual/getting-started.html)


## Define the Sequelize Model user.js

```javascript
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


### Server.js
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

### Passport.js
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
