# How to Create a Backend Project

## 1. Set Up the Server

1. Create a folder for your backend project.
2. Open the folder in VS Code.
3. Open the terminal.
4. Initialize a `package.json` file by running the following command:

```
npm init -y
```

5. Install the necessary packages for your project by running the following commands:

```
npm i express
npm i dotenv
npm i nodemon (if you don't have it installed globally)
```

6. Create a `.env` file to save your sensitive data.
7. Create a `.gitignore` file to ignore `node_modules` and environment variables. Add the following inside your `.gitignore` file:

```
/node_modules
.env
```

8. Create an `index.js` file.
9. Update `package.json` scripts by adding the following inside it:

```
"start": "node index.js",
"dev": "nodemon index.js"
```

10. Go to your `index.js` file and import the necessary packages:

```
const express = require("express");
const dotenv = require("dotenv")
```

11. Instantiate Express inside the `index.js` file:

```
const app = express();
```

12. Load your environment variables inside the `index.js` file:

```
dotenv.config();
```

13. Define your port number in the `.env` file:

```
PORT=5000
```

14. Create a variable called `PORT` in the `index.js` file to hold the value from the `.env` file:

```
const PORT = process.env.PORT;
```

15. Listen to your app connection on the port you defined before:

```
app.listen(PORT, () => console.log("Server is running on port " + PORT));
```

16. Open your terminal and run your server using the `nodemon` package with the following command:

```
npm run dev
```

## 2. Use Middleware to Handle JSON Data

Before working on your MVC (Model-View-Controller) and your database, use a built-in app-level middleware to handle your JSON data:

```
app.use(express.json())
```

## 3. Set Up Your Database

1. Install Mongoose (a MongoDB object modeling tool):

```
npm i mongoose
```

2. Create a folder called `db` inside your backend folder.
3. Inside the `db` folder, create a file called `index.js`.
4. Import Mongoose in the `index.js` file:

```
const mongoose = require("mongoose");
```

5. Establish a connection by adding options and DB name:

```
const options = {
 useNewUrlParser: true,
 useUnifiedTopology: true
};
```

6. Save your DB name as an environment variable by adding it in the `.env` file:

```
DB_URI=DB_NAME
```

7. Configure dotenv and connect to MongoDB in the `index.js` file:

```
const dotenv = require("dotenv");
dotenv.config();

const DB_URI = process.env.DB_URI;

mongoose.connect("mongodb://localhost:27017/" + DB_URI, options).then(() => console.log("DB Ready To Use"), (err) =>  console.log(err));
```

8. Import the `db/index.js` file in your `index.js` server file:

```
const db = require("./db/index.js");
```

9. Run your Express server. You should see the following output: "Server is running on port 5000 DB Ready To Use"

## 4. Create Your Schemas

1. Inside the `db` folder, create a folder called `models`.
2. Inside the `models` folder, create a file called `someSchema.js`.
3. Import Mongoose in the `someSchema.js` file:

```
const mongoose = require("mongoose");
```

4. Create your schema:

```
const someSchema = new mongoose.Schema({
    someKey: { type: String, required: true },
    date: { type: Date, default: new Date() },
    isDeleted: { type: String, default: false },
    someNumber: { type: Number, required: true, unique: true }
});

```

5. Export the schema so you can use it in your controllers:

```
module.exports = mongoose.model("Some", someSchema);
```

## 5. Set Up MVC (Model-View-Controller)

1. Create a folder called `routers` inside your backend folder.
2. Inside the `routers` folder, create two folders: `routes` and `controllers`.
3. Inside the `controllers` folder, create a file (`someFile.js`) to handle requests and responses:
   - Import the schema:

```
const someModel = require("./../../db/models/someSchema.js");

// Example function for creating data:
const someFunc = (req, res) => {
    const { someKey, someNumber } = req.body;

    const newSomeData = new someModel({ someKey, someNumber });

    newSomeData
      .save()
      .then((result) => res.json(result))
      .catch((err) => res.send(err))
};

// Example function for retrieving all data:
const anotherFunc = (req, res) => {

    someModel
      .find({})
      .then((result) => res.send(result))
      .catch((err) =>  res.send(err))
};
```

- Don't forget to export the functions created in the controller:

```
module.exports = { someFunc, anotherFunc };
```

4. Inside the `routes` folder, create a file (`someFile.js`) to handle your endpoints and controllers:
   - Import Express and your controllers:

```const express = require("express");
const { someFunc, anotherFunc } = require("./../controllers/someFile.js");

const someRouter = express.Router();
```

- Create your endpoints:

```
someRouter.get("/someEndpoint", someFunc);
someRouter.get("/anotherEndpoint", anotherFunc);

// Export the router:
module.exports = someRouter;
```

5. Import the router in your `index.js` file and create a router-level middleware:

```
const someRouter = require("./routers/routes/someFile.js");

app.use("/somePath", someRouter);
```

6. Test your endpoints on Postman. If it's not working, check the previous steps.

## 6. Useful Middleware

### Optional: Morgan Middleware

- Install Morgan to help read requests and responses:

```
npm i morgan
```

- Import Morgan in your `index.js` file:

```
const morgan = require("morgan");
```

- Use Morgan middleware:

```
app.use(morgan("dev"));
```

### Must-Have: CORS Middleware

- Install CORS to prevent CORS errors:

```
npm i cors
```

- Import CORS in your `index.js` file:

```
const cors = require("cors");
```

- Use CORS middleware:

```
app.use(cors());
```

## 7. Querying in MongoDB

### Example Query

- Query a list of users aged 20 to 30 and return their `userName` and `age` in descending order of age:

```
userModel
.find({})
.where("age")
.gt(20)
.lt(30)
.sort({ age: -1 })
.select("userName age")
.exec()
.then((result) => res.send(result))
.catch((err) => res.send(err));
```

### Example Reference Schema

- Create a reference from another schema:

```
const userSchema = new mongoose.Schema({
    userName: { type: String, required: true, unique: true },
    role: { type: mongoose.Schema.Types.ObjectId, ref: "Role", default: "SOME_ROLE_ID"}  // we copy the id from the role we created in the role schema
});

const roleSchema = new mongoose.Schema({ typeOfUser: { type: String, required: true, unique: true }, });
```
