---
title: 'Build a REST API with Node, Express & MongoDB!'
date: 2019-07-30T02:14:37.783Z
summary: >-
  In this project we will be creating a way for people to subscribe, update,
  retrieve and delete data on Youtube accounts. We’ll do this by coding a REST
  API that lets users interact with our Youtube database by defining what info
  our database will accept and how it goes about manipulating that data
  depending on what a user would like to do.
tags:
  - javascript
  - REST
  - routing
  - mongodb
---
In this project we will be creating a way for people to subscribe, update, retrieve and delete data on Youtube accounts. We’ll do this by coding a REST API that lets users interact with our Youtube database by defining what info our database will accept and how it goes about manipulating that data depending on what a user would like to do.

A big shoutout to [Web Dev Simplified](https://www.youtube.com/channel/UCFbNIlppjAuEX4znoulh0Cw) for the knowledge he was able to pass along in helping me create this tutorial. If you’d prefer to see a video walkthrough for this then I highly recommend checking out Web Dev Simplified’s tutorial [HERE](https://youtu.be/fgTGADljAeg)


### MY GITHUB REPO

If you get stuck or think you did something wrong and can’t quite figure it out, refer to my Github Repo of the completed application here: https://github.com/Beznet/node-api


## Project and Dependency Setup

**Important Prerequisite:** Make sure you already have MongoDB installed and setup on your machine before starting this tutorial. Here is a link to a guide that MongoDB provides on their website: [MongoDB Installation](https://docs.mongodb.com/manual/administration/install-community/)

First, let’s open our terminal and create a new directory for this project. Now in our terminal type `npm init` to start and just leave all fields blank for the time being by pressing ENTER through each question. Next we want to install [Express](https://expressjs.com/) and [Mongoose](https://mongoosejs.com/):

`npm i express mongoose`

Then once those two are finished being installed we need to also install [`dotenv`](https://www.npmjs.com/package/dotenv) and [`nodemon`](https://nodemon.io/) by entering:

`npm i --save-dev dotenv nodemon`

*Note: --save-dev from above will save development-only dependencies without saving them to production*

`dotenv` will allow us to pull in environment variables from a `.env` file and nodemon will update our local server each time we save

When we ran `npm init` earlier it created a `package.json` file. In this file under the scripts we want to replace that ‘test’ script with our own script that starts our server with `nodemon`:

```javascript
"scripts": {
  "devStart": "nodemon server.js"
}
```


## Setting Up Our Server

Ok now we get to create our first file called `server.js` . Once created, at the top of the file we want to include all of the dependencies that we will need for our project:

```javascript
const express = require('express')
const app = express()
const mongoose = require('mongoose')
```

When we want to test and make sure our server is working, we will run this function to listen on port 3000 and log a string if its successful:

```javascript
app.listen(3000, () => console.log('server started'))
```

At this point our server.js file should look like this:

```javascript
const express = require('express')
const app = express()
const mongoose = require('mongoose')
 
app.listen(3000, () => console.log('server started'))
```


## Connecting to Our Database

This next chunk of code in our `server.js` file starting with `mongoose.connect` is how we connect to the database. I’ll explain it line by line:

```javascript
const express = require('express')
const app = express()
const mongoose = require('mongoose')
 
mongoose.connect(process.env.DATABASE_URL, { useNewUrlParser: true })
const db = mongoose.connection
db.on('error', (error) => console.error(error))
db.once('open', () => console.log('connected to database'))
 
app.listen(3000, () => console.log('server started'))
```

The first line allows us to connect to the database using Mongoose. You may be wondering what `DATABASE_URL` is, this is the location we want for our database which I have defined in our `.env` file. If you haven’t created a `.env` file, now would be a good time and then you can set the `DATABASE_URL` there:

```shell
// .env file
 
DATABASE_URL=mongodb://localhost/subscribers
```

After we have our `DATABASE_URL` defined in our `.env` let's hop back to our `server.js` file. Now to reference variables in our `.env` file we need to require the `dotenv` package at the top of our server.js file:

```javascript
require('dotenv').config()
```

After we get that package required the line of…

```javascript
mongoose.connect(process.env.DATABASE_URL, { useNewUrlParser: true })
```

…will now function appropriately.

Moving on, we have set `db` to `mongoose.connection` as a syntactically easier way to reference our database. Next, we’ve defined what happens when the database encounters an error, which is to log the `error` itself. If there’s no error we want to log the string of `connected to database` to our terminal.

Ok! That’s a lot of stuff. So to sort of put everything together our `server.js` file should now look like this:

```javascript
require('dotenv').config()
 
const express = require('express')
const app = express()
const mongoose = require('mongoose')
 
mongoose.connect(process.env.DATABASE_URL, { useNewUrlParser: true })
const db = mongoose.connection
db.on('error', (error) => console.error(error))
db.once('open', () => console.log('connected to database'))
 
app.listen(3000, () => console.log('server started'))
```


## Let’s Test Our Database Connection!

At this point we can now test out our connection to the database to make sure everything is working smoothly. In our terminal, let’s go ahead and start our database by typing `mongod` and then in another terminal tab let’s start our server with `npm run devStart`. If all went well, we should get these messages after we start our server:

![](https://thepracticaldev.s3.amazonaws.com/i/1eg6v5zu79s73pu5i3sl.png)

Cool! We’ve successfully started our server and our database appears to be operational.


## Setting Up Our Server to Accept JSON

In our same `server.js` file we want to tell Express that it should accept JSON. Lets put this line of code in between our ‘database is open’ line of code and the ‘listen on port 3000’ line:

```javascript
...
db.once('open', () => console.log('connected to database'))
 
app.use(express.json())
 
app.listen(3005, () => console.log('server started'))
...
```

The `.use` is middleware that allows you to run code when the server gets a request but before it gets passed to your routes. So in this instance we’re telling Express to accept JSON as the data format.


## Creating Our Routes Folder & File

Let’s create a new directory for our routes to live in called `routes` and then a file in this directory called `subscribers.js` . Inside of this file is where we will define how the server should handle the data when it receives a GET, POST or PATCH request.

But before we start creating those, lets switch back to our `server.js` file and tell the server that we now have routes that it needs to handle and use. These next two lines of code are actually the last ones we need for our `server.js` file.

```javascript
const subscribersRouter = require('./routes/subscribers')
app.use('/subscribers', subscribersRouter)
```

Here’s the full `server.js` file with the new lines of code added:

```javascript
require('dotenv').config()
 
const express = require('express')
const app = express()
const mongoose = require('mongoose')
 
mongoose.connect(process.env.DATABASE_URL, { useNewUrlParser: true })
const db = mongoose.connection
db.on('error', (error) => console.error(error))
db.once('open', () => console.log('connected to database'))
 
app.use(express.json())
 
const subscribersRouter = require('./routes/subscribers')
app.use('/subscribers', subscribersRouter)
 
app.listen(3005, () => console.log('server started'))
```

If something is missing or incorrect, right now is a great checkpoint to make sure you’ve got everything updated with the above. Give yourself a pat on the back for getting this far! We’re getting onto the fun stuff next…


## Setting Up Our Routes

Now that our server file is all setup, let us now switch gears to our new `subscribers.js` routes file. To start, let us require Express and define the `express.router()` function on a variable called `router` . While we’re at it let’s add our [module export function](https://www.sitepoint.com/understanding-module-exports-exports-node-js/) as well at the very bottom:

```javascript
const express = require('express')
const router = express.Router()
 
module.exports = router
```

Before we get into the code, let’s get an understanding of what we exactly want to do with our routes. Since we’re trying to see and artificially create new subscribers on Youtube, we need this set of criteria:

- Route for getting all subscribers
- Route for getting one subscriber
- Route for creating one subscriber
- Route for updating one subscriber
- Route for deleting one subscriber

Now let’s start each of the bullet points above with the basic framework depending on if its a GET, POST or PATCH request:

```javascript
const express = require('express')
const router = express.Router()
 
// Get all subscribers
router.get('/', (req, res) => {
})
 
// Get one subscriber
router.get('/:id', (req, res) => {
})
 
// Create one subscriber
router.post('/', (req, res) => {
})
 
// Update one subscriber
router.patch('/:id', (req, res) => {
})
 
// Delete one subscriber
router.delete('/:id', (req, res) => {
})
 
module.exports = router
```

The GET, POST and DELETE methods should look familiar but if not here’s a little [refresher on them](https://www.restapitutorial.com/lessons/httpmethods.html) . The one thing that might look weird is the PATCH method. The PATCH method is being used here instead of the more traditional PUT method because we only want to use this to update pieces of the subscriber’s information and not all the information of the subscriber.

You also may have noticed that we include `/:id` in most of our parameters. This is for the routes that are requesting an action to be taken on a single subscriber thus we need that subscriber’s unique ID.


## Testing Out Our API

Let’s take a little break from our routes and test out our API with what we have so far. Inside of our ‘Get all subscribers’ route, let’s send text to our server:

```javascript
// Get all subscribers
 
router.get('/', (req, res) => {
   res.send('Hello World')
})
```

Now for testing our API, I am going to use a Visual Studio Code extension called [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client). If you do not have VS Code then you can use another program called [Postman](https://www.getpostman.com/) that will also aid in testing the API. For this tutorial I will be using just REST Client in VS Code.

Once you have the REST Client extension installed, let’s create a new file in our `routes` folder for us to run our tests and call it `route.rest`. It needs to end in `.rest` or else it will not work correctly with REST Client.

Great so now let’s write a test GET call to our server inside of our `route.rest` file:

```
GET http://localhost:3000/subscribers
```

Make sure you have `/subscribers` after the 3000. Now if you hover above that line you just wrote, you should see ‘Send Request’ appear above it. Click that and watch on the right hand side of your screen for the result. If all went well, it should look something like this:

![](https://thepracticaldev.s3.amazonaws.com/i/kr3wenx3n5o8qjy05t5f.png)


On line 9, we can see that the server sent back ‘hello world’. This means our API is working correctly! Ok so we’ve done a lot so far, let’s take a hydration break and then we’ll get started with models.


## Making the Model

Let’s go ahead and setup our model and the schema inside of our model. A schema is how our app defines what the data looks like and also sets up the document in MongoDB. If that sounds confusing, it’ll make more sense once we see what’s going on.

Let’s first start by creating a new folder called `models`. Inside of this folder, let’s create a new file called `subscriber.js`. Notice how we’re naming this file ‘subscriber’ singular as opposed to our routes file which is ‘subscribers’ plural. The idea is that the model will handle how each and every subscriber, on an individual level, will look inside of our database. The ‘subscribers’ routes handles sometimes multiple subscribers requests such as Get All Subscribers route. It’s an important thing to mention as verbiage is important when naming files in projects.

In this new file, we want to first require Mongoose since we will be using their schema models:

```javascript
const mongoose = require('mongoose')
```

After we require mongoose, we’ll start by defining our schema

```javascript
const mongoose = require('mongoose')
 
const subscriberSchema = new mongoose.Schema({})
```

Inside of this javascript object will be all of the keys for the different properties of our subscriber. These keys will include `name`, `subscribedChannel` and `subscribeDate`. We essentially want to tell our database what to expect from each one of these key’s such as their type, if they’re required and if a default value should be applied.

```javascript
const mongoose = require('mongoose')
 
const subscriberSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  subscribedChannel: {
    type: String,
    required: true
  },
  subscribeDate: {
    type: Date,
    required: true,
    default: Date.now
  }
})
```

The `type` and `required` properties should be pretty self explanatory. These are defining the expected schema type (a String and Date in this case) as well if that key is required upon entering information for a new subscriber.

One thing to note about `subscribeDate`, we set the type to Date instead of String since we will be expecting a date from the user. If no date is provided then we default it to the current date by using `Date.now` .

Moving on, the last line of code we want to write in our schema is the `module.exports`. This will allow us to use and interact with the database using our schema. Mongoose has a special way of exporting models utilizing `mongoose.model()` that takes two properties:

```javascript
// exporting our subscriber schema
 
module.exports = mongoose.model('Subscriber', subscriberSchema)
```

‘Subscriber’ is the name we want to give the model in our database and then the next one is the schema that corresponds to that model which is our `subscriberSchema`.

That’s it for our model! Let’s take a look at the finished schema to make sure we’ve got it all:

```javascript
const mongoose = require('mongoose')
 
const subscriberSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  subscribedChannel: {
    type: String,
    required: true
  },
  subscribeDate: {
    type: Date,
    required: true,
    default: Date.now
  }
})
 
module.exports = mongoose.model('Subscriber', subscriberSchema)
```


## Routes – Create and Get Subscribers

Now that we have our model setup with a schema for our database, let’s require it in our `subscribers.js` route file below where we required `router`

```javascript
const express = require('express')
const router = express.Router()
const Subscriber = require('../models/subscriber')
```

Now we can pick up where we left off with our routes where we coded a shell for each one:

```javascript
// Getting all subscribers
router.get('/', (req, res) => {
   res.send('Hello World')
})
 
// Getting one subscriber
router.get('/:id', (req, res) => {
})
 
// Creating one subscriber
router.post('/', (req, res) => {
})
 
// Updating one subscriber
router.patch('/:id', (req, res) => {
})
 
// Deleting one subscriber
router.delete('/:id', (req, res) => {
})
```

We have our test response from earlier when we sent ‘Hello World’ to the server but we can go ahead and delete that since we want to actually tell that route to Get All Subscribers.

The first thing we’ll need to do, after we get rid of our old `res.send('Hello World')` line, is wrap the function in a promise with a try/catch statement:

```javascript
// Get all subscribers
 
router.get('/', async (req, res) => {
  try {
 
  } catch () {
 
  }
})
```

Inside of our `try` statement we want to get all of the subscribers from our model. So we want to set a new variable called subscribers to that model with a `.find()` method applied to our Subscriber model.

```javascript
// Get all subscribers
 
router.get('/', async (req, res) => {
  try {
    const subscribers = await Subscriber.find()
  } catch () {
 
  }
})
```

As the name implies, the `find()` Mongoose method works by returning all associated subscriber objects that meet its criteria. Since we’re returning all subscribers, we just leave the parentheses blank since we want all the subscribers:

After that line we then want to send a response with the data of our `subscribers` variable we just created in the form of JSON:

```javascript
// Get all subscribers
 
router.get('/', async (req, res) => {
  try {
    const subscribers = await Subscriber.find()
    res.json(subscribers)
  } catch () {
 
  }
})
```

Finally, in our `catch` statement we want to just catch any errors that may occur and have it sent to the user as a 500 error in JSON format:

```javascript
// Get all subscribers
 
router.get('/', async (req, res) => {
  try {
    const subscribers = await Subscriber.find()
    res.json(subscribers)
  } catch (err) {
    res.status(500).json({ message: err.message })
  }
})
```

Now that we have our route to send us all the subscribers in our database, we need to code a way for us to actually add a subscriber into our database. So, lets move onto our Create One Subscriber route so we can enter data about a subscriber:

```javascript
// Create one subscriber
 
router.post('/', async (req, res) => {
  const subscriber = new Subscriber({
    name: req.body.name,
    subscribedChannel: req.body.subscribedChannel
  })
 
  try {
    const newSubscriber = await subscriber.save()
    res.status(201).json(newSubscriber)
  } catch (err) {
    res.status(400).json({ message: err.message })
  }
})
```

You can see its somewhat similar to our Get All Subscribers route except a few important differences. For one, we’re no longer doing a GET call on our database but a POST which will allow us to push data to our database.

On this line:

`const subscriber = new Subscriber({...`

we are creating a variable that will be assigned to a new `Subscriber` from our model that we created earlier. If you recall, we require a `name`, `subscribedChannel` and `subscribeDate` properties for a new subscriber.

These next two lines of code:

`name: req.body.name,
 subscribedChannel: req.body.subscribedChannel`

We’re telling our route to save the request made from a user’s input of a new Subscriber `name` property and `subscribedChannel` property. Our `subscribeDate` doesn’t need to be defined because it will default automatically to the date/time that this database entry is made.

The `try` and `catch` statements should look familiar. We’re instead using a `.save()` Mongoose method instead of `find()` because this is how we will tell the database that we want it to hold the information a user passes to us through this router function.

Lastly:

`   ...
    res.status(201).json(newSubscriber)
  } catch (err) {
    res.status(400).json({ message: err.message })
  }
`

We’re sending the user a response with a [success status of 201](https://httpstatuses.com/201) and to pass our new subscriber back to us as JSON. The catch is just like our Get All Subscribers route except we pass a [400 error](https://httpstatuses.com/400) since this would be a user error for passing us bad data.


## Let’s Create and Get Our First Subscriber!

At this point we’ve created our Model and two Routes that will allow us to create a subscriber and then call that subscriber’s information from our database. Let’s switch gears back to our `route.rest` file and create our first user:

```
GET http://localhost:3000/subscribers
 
###
 
POST http://localhost:3000/subscribers
Content-Type: application/json
 
{
  "name": "Robert",
  "subscribedChannel": "Bennetts Channel"
}
```

With REST Client, we need to separate our different test requests with those three hashtags you see in between the two statements on line 3.

Our first request should be the exact same as before so the only different one here is our new POST request that allows us to create a new subscriber with their name (Robert in my case) and the channel they subscribed to ([mine!](https://www.youtube.com/channel/UCDP-Mkz_lRoFSiP9_p1L3xQ)). So lets run our POST request and see what we get:

![](https://thepracticaldev.s3.amazonaws.com/i/n2095ojafy3fksfc78ex.png)

If everything went well, our response when we click ‘Send Request’ should look like the above. We can see that we received our 201 success status at the very top along with our Subscriber object at the bottom with all the information we just plugged in.

Again, `subscribeDate` is set automatically to the date of creation so we don’t need to manually set it. Now lets use our Get All Subscribers request and make sure that it returns us Robert’s information:

![](https://thepracticaldev.s3.amazonaws.com/i/51zg5x6qaawdvzh5ea1z.png)

Our response will look similar to our Create Subscriber response since we only have a single user in our database but if you create multiple subscribers then this will populate all of them for you.


## The Middle-what? The Middleware!

We’re in the final stretch! The last things few things we need to do is complete our Delete, Update and Get One Subscriber routes and our REST API is complete!

One thing in common that all 3 of the last routes share is getting the ID of a specific user. Instead of writing out that part of the code 3 times over we can just put that piece of code in its own function and call it as [middleware](https://mongoosejs.com/docs/middleware.html) in the Mongoose GET/PATCH/POST statements. Lets place this middleware function called `getSubscriber` right before our `module.exports = router` line:

```javascript
async function getSubscriber(req, res, next) {
  try {
    subscriber = await Subscriber.findById(req.params.id)
    if (subscriber == null) {
      return res.status(404).json({ message: 'Cant find subscriber'})
    }
  } catch(err){
    return res.status(500).json({ message: err.message })
  }
 
  res.subscriber = subscriber
  next()
}
```

There’s a lot going on here so let’s break it down. The first line should look pretty familiar except that new property called `next` . Basically, when `next` is called its telling the function execution to move onto the next section of our code, which is the route function this `getSubscriber` function will be added to.

Our `try` and `catch` statements are opened up as with our other routes. We then define subscriber on line 3 equal to our Subscriber model object which we will then use the `findById` method to find our subscriber that correlates to the ID the user passes in from the parent route. This will set the `subscriber` to an object equal to that id.

*Side Note:* This part can be a little confusing but hang on until we get to the Update/Delete/Get One routes below. I’ll make more sense when we see it in action.

Once we have `subscriber` set to our model’s object, we check with an `if` statement to see if that subscriber even exists with a `null` check. If the subscriber is nonexistent then we want to throw a 404 error. Then our `catch` is doing the familiar error check and sending the user back a 500 status code if that

Finally we’re left with our last two lines of code from our middleware function:

`res.subscriber = subscriber
next()`


The `res.subscriber` is setting a variable on the response object which is equal to our subscriber object. This is useful so we don’t have to write that same line of code, we can just reference `res.subscriber` from this function. Lastly, we use the `next()` function after everything else is finished executing to tell the `getSubscriber` function to move onto the actual request.

Now that we’ve got our middleware function completed, let’s add it to our Delete, Update and Get One routes:

```javascript
// Get one subscriber
router.get('/:id', getSubscriber, (req, res) => {
})
 
// Update one subscriber
router.patch('/:id', getSubscriber, async (req, res) => {
})
 
// Delete one subscriber
router.delete('/:id', getSubscriber, async (req, res) => {
})
```


## Route – Get One Subscriber

We did most of the heavy lifting for these last 3 routes with our middleware so horray for that. Let’s start off with our Get One route:

```javascript
// Get One Subscriber
 
router.get('/:id', getSubscriber, (req, res) => {
  res.json(res.subscriber)
})
```

Look how ridiculously simple that looks. All we have to do is send the user a response in JSON with the `res.subscriber` that we defined in our middleware function which returns that specific subscriber’s info. We can test this new route real quick to make sure our `getSubscriber` middleware function and this new route we created actually works.

Let’s hop back over to our `route.rest` file and create a new subscriber with our Create Subscriber request so we can get that subscriber’s ID:

![](https://thepracticaldev.s3.amazonaws.com/i/8cew3a9fonj1pzyyor2o.png)

Alright so we created a new subscriber and named him Bob and we can see he has a long ID associated with his object right above his name. I want to keep that ID so when we write our new GET route I can call Bob specifically by his ID. We can put this below our Get All Subscribers request:

```
GET http://localhost:3000/subscribers
 
###
 
GET http://localhost:3000/subscribers/5d3ce4ef1b5de0b79d3443b9
 
###
 
POST http://localhost:3000/subscribers
Content-Type: application/json
 
{
  "name": "bob",
  "subscribedChannel": "Bennetts Channel"
}
```

On line 5 is where we’ll create the new test request, so put in your newly created user’s ID (yours will be different than mine above) and click ‘Send Request’:

![](https://thepracticaldev.s3.amazonaws.com/i/yuk16o3zjex5n6xuh322.png)

If everything went well, we shouldn’t see a change on our response to the right because we’re just calling the same user that we just created. Give yourself a pat on the back, your middleware function and Get One route works!


## Route – Delete Subscriber

Now that we know our middleware is doing its job, lets switch back on over to our `subscribers.js` routes file and finish our last two routes starting with Delete:

```javascript
// Delete one subscriber
 
router.delete('/:id', getSubscriber, async (req, res) => {
  try {
    await res.subscriber.remove()
    res.json({ message: 'Deleted This Subscriber' })
  } catch(err) {
    res.status(500).json({ message: err.message })
  }
})
```

We open up our `try` statement and then we plug in our `await` operator right before we set our `res.subscriber` to then use the `remove()` method to delete the subscriber that the `res.subscriber` object was set to. We then want to tell the user that we successfully deleted the subscriber by passing the response message in JSON:

`res.json({ message: 'Deleted This Subscriber' })`

As always, we want our `catch` to send the user the appropriate error if one did occur. That’s it for delete!


## Route – Update Subscriber

Our Update Subscriber route is the very last thing we need to write for this application to be fully functional! Ok so before we get into the code lets get a general idea of how updating will work in this case:

- User updates just the name
- User updates just the channel
- User updates both name and channel
- Or they mess up and it throws an error

Our requirements need for us to essentially check and see if any changes were made and if so, update them appropriately. Now onto the code:

```javascript
// Update Subscriber
 
router.patch('/:id', getSubscriber, async (req, res) => {
  if (req.body.name != null) {
    res.subscriber.name = req.body.name
  }
 
  if (req.body.subscribedChannel != null) {
    res.subscriber.subscribedChannel = req.body.subscribedChannel
  }
  try {
    const updatedSubscriber = await res.subscriber.save()
    res.json(updatedSubscriber)
  } catch {
    res.status(400).json({ message: err.message })
  }
 
})
```

We start our function off with a PATCH method, which we discussed when initially defining the shell of our routes. Now you can see we’ve added two `if` statements to our function. The first if statement is checking to see if the name coming from the `body` of the user’s request is not equal to `null`. This is an important check because if it is equal to `null` then that means the user did not pass any data about name through our route function. If they did then we move onto this line:

`res.subscriber.name = req.body.name`

Where we’re setting our subscriber’s name from `res.subscriber` and setting the name now equal to the new name that the user passed in from their request.

The same logic follows here:

`res.subscriber.subscribedChannel = req.body.subscribedChannel`

Where we’re checking to see if the user updated the `subscribedChannel` and if so, we then perform the same sequence of changing the current `subscribedChannel` to the new one from the user’s request.

After we’ve made these `if` statement checks we then want to tell the function to then save these new changes into our database. This is easily done within our `try` statement where we take the `res.subscriber` object with our new name and/or channel and then add the `save()` method onto it within a new variable called `updatedSubscriber`. We then want to pass this new `updatedSubscriber` object to our user in the form of JSON:

`  try {
    const updatedSubscriber = await res.subscriber.save()
    res.json(updatedSubscriber)
   }
`

That’s literally everything we need in our `subscribers.js` route file so let’s take a pause here and make sure we’ve got everything up to this point. Go to my [github repo](https://github.com/Beznet/node-api) for this project and make sure your code accurately mirrors what I have before moving onto the final tests below.


## The Final Test
We’ve completed our last two routes, let’s go into our `route.rest` file and test these bad boys out and make sure they work correctly.

Our Delete request is just as simple as our Get One Subscriber route except instead of using the GET request we’re naturally going to use the DELETE request instead. So get the ID of one of the subscribers in your database and let’s create that test route:

```
###
 
DELETE http://localhost:3000/subscribers/5d3e0db7cb4be0bfc4c25ff9
```

Now let’s click ‘Send Request’ and see if our user is successfully deleted:

![](https://thepracticaldev.s3.amazonaws.com/i/l2do2uh9ivqlren4gp95.png)


Nice! So we can see we got a message of ‘Deleted This Subscriber’ on the right hand side which means we were successful. Now if you take that same ID that you just deleted and try to request it using our Get One Subscriber request, it should tell us that it cannot find that subscriber since they no longer exist in our database:

![](https://thepracticaldev.s3.amazonaws.com/i/duat7pwxz9cmd6yvdjsv.png)

Our last request will be to update an existing subscriber’s information in our database with our Update route. Since we just deleted our last subscriber, go ahead and create a new subscriber and lets write our Update request using PATCH:

```
###
 
PATCH http://localhost:3000/subscribers/5d3e144ecb4be0bfc4c25ffa
Content-Type: application/json 
 
{
  "name": "Tommy"
}
```

I just created a new subscriber before running the above PATCH request and named them Lizzy:

![](https://thepracticaldev.s3.amazonaws.com/i/f86mz37ip99ylei0nauz.png)


Alright so if we run the above PATCH request we should see Lizzy’s name change to Tommy:

![](https://thepracticaldev.s3.amazonaws.com/i/nj7cl7qtkto45nyikidq.png)

If everything went smoothly then we just were able to successfully update Lizzy’s name to Tommy.


# Conclusion

Guys, we made it. I really hope this tutorial was helpful to some of you. We went over a lot of stuff so if you’re feeling overwhelmed then thats totally understandable. But realize we just made a pretty awesome backend piece of code that translates into so many different real-world applications. So big props to you for making it through it all!

If you ever got stuck or found something worth mentioning, go ahead and leave me a comment below. Also feel free to follow me on [Twitter](https://twitter.com/beznet) and [Youtube](https://www.youtube.com/channel/UCDP-Mkz_lRoFSiP9_p1L3xQ) as I post regularly on both. Happy coding everyone!

_Originally published [here](https://www.bennettdungan.com/2019/07/29/build-a-rest-api-with-node-express-mongodb/)_
