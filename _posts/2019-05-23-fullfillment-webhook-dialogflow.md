---
title: Dialogflow fullfillment AND webhook together
date: 2019-05-22 22:21:00 +0000
tags: git
---

```javascript
// See https://github.com/dialogflow/dialogflow-fulfillment-nodejs
// for Dialogflow fulfillment library docs, samples, and to report issues
"use strict";

const functions = require("firebase-functions");
const { WebhookClient } = require("dialogflow-fulfillment");
const { Card, Suggestion } = require("dialogflow-fulfillment");
const requestClient = require("request-promise-native");

const URL = "https://tnp.eu.ngrok.io/hook?token=token";

process.env.DEBUG = "dialogflow:debug"; // enables lib debugging statements

exports.dialogflowFirebaseFulfillment = functions.https.onRequest(
  (request, response) => {
    const agent = new WebhookClient({ request, response });
    console.log(
      "Dialogflow Request headers: " + JSON.stringify(request.headers)
    );
    console.log("Dialogflow Request body: " + JSON.stringify(request.body));

    function welcome(agent) {
      agent.add(`Welcome to my agent!`);
    }

    function fallback(agent) {
      agent.add(`I didn't understand`);
      agent.add(`I'm sorry, can you try again?`);
    }

    function gameFunctionHandler(agent) {
      var options = {
        method: "POST",
        uri: URL,
        body: request.body,
        json: true // Automatically stringifies the body to JSON
      };

      return requestClient(options)
        .then(function(parsedBody) {
          agent.add(parsedBody.fulfillmentText);
          const contexts = parsedBody.outputContexts;
          let context;

          for (var i in contexts) {
            context = contexts[i].name.split("/");
            const context_name = context[context.length - 1];
            agent.setContext({
              name: context_name,
              lifespan: contexts[i].lifespanCount,
              parameters: contexts[i].parameters
            });
          }

          return Promise.resolve(agent);
        })
        .catch(function(err) {
          console.log(err);
        });
    }

    // Run the proper function handler based on the matched Dialogflow intent name
    let intentMap = new Map();
    intentMap.set("Default Welcome Intent", welcome);
    intentMap.set("Default Fallback Intent", fallback);
    intentMap.set("game", gameFunctionHandler);
    intentMap.set("game-fallback", gameFunctionHandler);
    agent.handleRequest(intentMap);
  }
);
```

```package.json
{
  "name": "dialogflowFirebaseFulfillment",
  "description": "This is the default fulfillment for a Dialogflow agents using Cloud Functions for Firebase",
  "version": "0.0.1",
  "private": true,
  "license": "Apache Version 2.0",
  "author": "Google Inc.",
  "engines": {
    "node": "8"
  },
  "scripts": {
    "start": "firebase serve --only functions:dialogflowFirebaseFulfillment",
    "deploy": "firebase deploy --only functions:dialogflowFirebaseFulfillment"
  },
  "dependencies": {
    "actions-on-google": "^2.2.0",
    "firebase-admin": "^5.13.1",
    "firebase-functions": "^2.0.2",
    "dialogflow": "^0.6.0",
    "dialogflow-fulfillment": "^0.5.0",
    "request-promise-native": "^1.0.5",
    "request": "^2.88"
  }
}
```
