# cloud-pub-sub
A message broker, simply put, is an intermediary software that relays messages from one or more services to other services. An example of such software is Cloud Pub/Sub.

Cloud Pub/Sub is a fully managed message broker by Google which relays information passed from a service/services to other subscribing services using the publisher-subscriber model. Pub/Sub makes it easy for teams to easily set up communication channels between services and not have to worry about individual service uptime, retry policies, and idempotency.

In this repository, we will be taking a look at how we can implement inter-service communications using Cloud Pub/Sub in Node.js.

Prerequisites
This tutorial uses the following:

Basic knowledge of Node.js
Yarn or NPM installed (we’ll be using yarn)
Git installed
Ngrok installed
A Google account
A development machine configured to run Node.js
Installation
Run the following commands in any terminal of choice to initialize the project directory:

$ git clone https://github.com/abako220/Pangaea_take_home_test.git

$ cd cloud-pub-sub-tut && yarn

Packages installed:

express — A lightweight Node.js web framework for spinning up RESTful APIs. We will use this to handle routing in our backend API
nodemon — This package will help us automatically restart our development server when we make code changes/edits
body-parser — A middleware to parse incoming request inputs into our req.body object
morgan — HTTP request logger middleware for Node.js. This will help us debug our API while in development
helmet — This is a collection of middlewares for which our express-based server by setting HTTP headers which conforms to best security practices
cors — This package will help enable cross-origin resource sharing on our server
dotenv — This package will enable us to have access to the environment defined in a .env file from our Node application via the process.env object
google-cloud/pubsub — This is the Node.js client for Cloud Pub/Sub. We will be using this to publish messages and subscribe to topics defined in our pub/sub console
Setting up a service account for Pub/Sub on GCP
To initialize Pub/Sub in our Node app, we will need to get a service account configured with Pub/Sub access on our GCP (Google Cloud Platform) console. We will do so in the following steps:

Log in to the Google Cloud Console and select a project or follow the prompt to create one if it’s your first time
Navigate to the Pub/Sub Section to enable the API for your project
Head over to the Service Accounts section to select a project and create a service account 

Definition of terms
In this section, we will define a couple of terms that will be used throughout this article. We will also take a look at a typical message flow from the publisher to subscriber.

Message — This is the data entity which is relayed to subscribing services on a particular topic
Topic — As with every conversation, there’s is a theme of communication. A topic is a theme which represents a feed of messages
Subscription — This is a coalition of subscriber entities which receives published messages on a particular topic
Subscriber — This is an entity which is set on course to receive and acknowledge messages from a particular topic either by push or pull
Publisher — This is an entity which creates and broadcasts a message to subscribing services on a topic.


#Process 
1. The publisher application (e.g an orders service) sends a message (orders object) to a topic (orders_topic).

2. Pub/Sub ensures messages are retained in storage until services subscribing to the said topic (orders_topic) acknowledges message receipt.

3. Pub/Sub then fans-out the message to all subscribing entities in the orders_topic.

4. A subscriber then receives the message from Pub/Sub. This is either done by pushing to a subscriber configured endpoint or by the subscriber pulling the said message from Pub/Sub.

5. The subscriber sends an acknowledgment to Pub/Sub for each message received and then removes the acknowledged message from the message queue.

# Running our services
To demonstrate three micro-services we created three entry-points (orders.js, notifications-sub.js, and delivery-sub.js) in our project directory as opposed to having just on app.js file.

Before we run our application, we need to create our .env file and copy in our service account key into our project directory. The .env file should look something like this:

MAIN_PORT=8001
PORT_1=8002
PORT_2=8003

GCP_PROJ_ID=PROJECT_ID
GOOGLE_APPLICATION_CREDENTIALS=FILENAME.json

Where PROJECT_ID = GCP project id and FILENAME = service account file name as both created in the service account setup section.

Now that this is out of the way, let’s set up six terminals to run our three services concurrently. On a Mac machine using iTerm2, you can create six terminals by splitting a single-window into horizontal halves using CMD+SHIFT+D. Then split each horizontal halve into three places vertically by using CMD+D twice on each horizontal halve.

Next up we will run our services locally in the upper half of the terminal by running the following commands on each section like so:

//upper terminal 1 (order service)
$ yarn start:main
//upper terminal 2 (delivery service)
$ yarn start:delivery
//upper terminal 3 (notification service)
$ yarn start:notification

Then in the lower half, we will provision public URLs for our localhost servers using ngrok by running the following commands in each section like so:

//upper terminal 1 (order service)
$ ngrok http 8001
//upper terminal 2 (delivery service)
$ ngrok http 8002
//upper terminal 3 (notification service)
$ ngrok http 8003

