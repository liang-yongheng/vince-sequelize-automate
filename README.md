# Vince-Sequelize-Automate

该组件通过 sequelize-automate 组件进行改造 [github](https://github.com/nodejh/sequelize-automate.git)

Automatically generate models for [SequelizeJS](https://github.com/sequelize/sequelize). Support javascript, typescript, egg.js and midway.

## Installing

### global

```shell script
$ npm install -g vince-sequelize-automate
```

You'll also have to manually install the driver for your database of choice:

```shell script
# One of the following:
$ npm install -g pg pg-hstore # Postgres
$ npm install -g mysql2
$ npm install -g mariadb
$ npm install -g sqlite3
$ npm install -g tedious # Microsoft SQL Server
```

### in poroject

```shell script
$ npm install vince-sequelize-automate --save
```

You'll also have to manually install the driver for your database of choice:

```shell script
# One of the following:
$ npm install --save pg pg-hstore # Postgres
$ npm install --save mysql2
$ npm install --save mariadb
$ npm install --save sqlite3
$ npm install --save tedious # Microsoft SQL Server
```


## Usage

### Command Line

```shell script
Usage: vince-sequelize-automate -t [type] -h <host> -d <database> -u <user> -p [password] -P [port]  -e [dialect] -o [/path/to/models] -c [/path/to/config]

Options:
  --version       Show version number                                  [boolean]
  --help          Show help                                            [boolean]
  --type, -t      Which code style want to generate.
                           [choices: "js", "ts", "egg", "midway", "@ali/midway"]
  --host, -h      IP/Hostname for the database.  [string] [default: "localhost"]
  --database, -d  Database name.                      [string] [default: "test"]
  --user, -u      Username for database.              [string] [default: "root"]
  --password, -p  Password for database.              [string] [default: "root"]
  --port, -P      Port number for database. e.g. MySQL/MariaDB: 3306, Postgres:
                  5432, MSSQL: 1433                                     [number]
  --dialect, -e   The dialect/engine that you're using: mysql, sqlite, postgres,
                  mssql
            [choices: "mysql", "sqlite", "postgres", "mssql"] [default: "mysql"]
  --output, -o    What directory to place the models.
                                                    [string] [default: "models"]
  --camel, -C     Use camel case to name models       [boolean] [default: false]
  --config, -c    Sequelize automate config file, see README.md         [string]
  --emptyDir, -r  Remove all files in `dir` and `typesDir` directories before
                  generate models.                    [boolean] [default: false]
```

#### Example

```shell script
$ vince-sequelize-automate -t js -h localhost -d test -u root -p root -P 3306  -e mysql -o models
```

Produces a file/files such as ./models/user.js which looks like:

```javascript
const {
  DataTypes
} = require('sequelize');

module.exports = sequelize => {
  const attributes = {
    id: {
      type: DataTypes.INTEGER(11).UNSIGNED,
      allowNull: false,
      defaultValue: null,
      primaryKey: true,
      autoIncrement: true,
      comment: "primary ket",
      field: "id"
    },
    name: {
      type: DataTypes.STRING(100),
      allowNull: false,
      defaultValue: null,
      primaryKey: false,
      autoIncrement: false,
      comment: "user name",
      field: "name",
      unique: "uk_name"
    },
    email: {
      type: DataTypes.STRING(255),
      allowNull: false,
      defaultValue: null,
      primaryKey: false,
      autoIncrement: false,
      comment: "user email",
      field: "email"
    },
    createdAt: {
      type: DataTypes.DATE,
      allowNull: false,
      defaultValue: null,
      primaryKey: false,
      autoIncrement: false,
      comment: "created datetime",
      field: "created_at"
    },
    updatedAt: {
      type: DataTypes.DATE,
      allowNull: false,
      defaultValue: null,
      primaryKey: false,
      autoIncrement: false,
      comment: "updated datetime",
      field: "updated_at"
    }
  };
  const options = {
    tableName: "user",
    comment: "",
    indexes: []
  };
  const UserModel = sequelize.define("userModel", attributes, options);
  return UserModel;
};
```

#### Configuration options

You can use `-c, --config` option to specify a configuration file.

```shell script
$ vince-sequelize-automate -c "./sequelize-automate.config.json"
```

For now, you must create a file called `sequelize-automate.config.json` with the following content:

```json
{
  "dbOptions": {
    "database": "test",
    "username": "root",
    "password": "root",
    "dialect": "mysql",
    "host": "localhost",
    "port": 3306,
    "logging": false
  },
  "options": {
    "type": "js",
    "dir": "models"
  }
}
```

Or a `.js` file: `vince-sequelize-automate -c "./sequelize-automate.config.js"`

```javascript
module.exports = {
  dbOptions: {
    database: "test",
    username: "root",
    password: "root",
    dialect: "mysql",
    host: "localhost",
    port: 3306,
    logging: false
  },
  options: {
    type: "js",
    dir: "models"
 }
}
```

#### In project

Also, you can use `vince-sequelize-automate` in project.

First add a configuration file `sequelize-automate.config.json` as above and add `automate` script to `package.json`:

```json
"script": {
  "automate": "vince-sequelize-automate -c sequelize-automate.config.json"
}
```

Then you can use `npm run automate` to generate models.


## Programmatic API

```javascript
const Automate = require('vince-sequelize-automate');

// Database options, is the same with sequelize constructor options.
const dbOptions = {
  database: 'test',
  username: 'root',
  password: 'root',
  dialect: 'mysql',
  host: '127.0.0.1',
  port: 3306,
  define: {
    underscored: false,
    freezeTableName: false,
    charset: 'utf8mb4',
    timezone: '+00:00',
    dialectOptions: {
      collate: 'utf8_general_ci',
    },
    timestamps: false,
  },
};

// Automate options
const options = {
  type: 'js', // Which code style want to generate, supported: js/ts/egg/midway. Default is `js`.
  camelCase: false, // Model name camel case. Default is false.
  fileNameCamelCase: true, // Model file name camel case. Default is false.
  dir: 'models', // What directory to place the models. Default is `models`.
  typesDir: 'models', // What directory to place the models' definitions (for typescript), default is the same with dir.
  emptyDir: false, // Remove all files in `dir` and `typesDir` directories before generate models.
  tables: null, // Use these tables, Example: ['user'], default is null.
  skipTables: null, // Skip these tables. Example: ['user'], default is null.
  tsNoCheck: false, // Whether add @ts-nocheck to model files, default is false.
  tablePrefix: '', // The file name is unprefixed
}

const automate = new Automate(dbOptions, options);

(async function main() {
  // // get table definitions
  // const definitions = await automate.getDefinitions();
  // console.log(definitions);

  // or generate codes
  const code = await automate.run();
  console.log(code);
})()
```

Database options `dbOptions` is the same with sequelize constructor options, you can find all options here: [https://sequelize.org/master/class/lib/sequelize.js~Sequelize.html#instance-constructor-constructor](https://sequelize.org/master/class/lib/sequelize.js~Sequelize.html#instance-constructor-constructor).

### Methods

- `automate.getDefinitions()`: Get all model definitions. `vince-sequelize-automate` will use these definitions to generate different codes.
- `automate.run()`: Generate model codes.


## Type

You can generate different (node.js framework's) codes use `type` option.

### JavaScript

```shell script
$ vince-sequelize-automate -t js
```

### TypeScript

```shell script
$ vince-sequelize-automate -t ts
```

### Egg.js

```shell script
$ vince-sequelize-automate -t egg
```

### Midway.js

```shell script
$ vince-sequelize-automate -t midway
```

If you want to generate codes for other frameworks, please let me know.
