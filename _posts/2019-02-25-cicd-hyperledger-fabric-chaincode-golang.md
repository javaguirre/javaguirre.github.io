---
layout: post
subclass: 'post tag-test tag-content'
title: 'CI/CD on a Hyperledger Fabric project'
date: 2019-02-25 22:21:00 +0000
tags: blockchain hyperledger
layout: post
cover: '/assets/images/chains.jpeg'
navigation: True
logo: '/assets/images/profile.png'
---

Today we bring you the second post of this series of technical articles regarding development with Hyperledger Fabric.
Last time we [talked about how we test our chaincode](https://medium.com/theneonproject/testing-chaincode-on-hyperledger-fabric-22b50a3c1af1),
today we go one step further and we’ll show you how we deploy the code automatically.

![](https://media.giphy.com/media/26DNdV3b6dqn1jzR6/giphy.gif)

**CI/CD** is the combined practice of automating the testing of our application and the deployment of the application to our servers.

Our workflow integrates different platforms in our pipeline, Github, Gitlab CI and IBM Blockchain. We store the code in Github, when we push code to Github it automatically goes to Gitlab CI where the automated tests are executed. If everything goes well we deploy the code to IBM automatically.

# Continuous Integration

Continuous Integration is a practice that requires developers to **integrate** code into a shared repository several times a day. Every time the code is pushed to the code repository, the automated tests are executed.

There are many cool CI services and we’ve used several of them, right now we’re happy with Gitlab CI, very easy to configure and full of possibilities!

There are two phases on our CI/CD process: the testing and the deploy phase. You can have several phases in parallel, in the test phase we run three processes, backend, frontend, and chaincode testing. When the tests pass and if the rules are met, the application and chaincode are *deployed*. :-)

![](https://media.giphy.com/media/KsCtl2h2RZKso/giphy.gif)

Here we’re gonna show you the configuration for the chaincode only since the rest of the application is well documented everywhere (we use industry standards such as Django, React, etc…).

In the test phase we run the chaincode tests with this configuration, the steps are documented in the code snippet:

{% gist 160428b1ba46b5ba5e4e8cb8a587fd21 %}

# Continuous Delivery

Every change that passes the automated tests is deployed to production automatically, so when the tests pass it’s time to deploy!

![](https://media.giphy.com/media/CDZwopbecAbIc/giphy.gif)

This stage is executed only in two specific cases, for the **staging** and the **production** environment:

- **Staging**: When we push the changes to **master** in Git
- **Production**: When we push the changes with a Git **tag**. We use the following formats for the tags *vYYYY.MM.DD* (Year.Month.Day)

{% gist f3270955476e8f833131afee243e5316 %}

Each deploy consists of two API calls. The first one is used to **install the chaincode in the platform**, the second one to **instantiate the chaincode in one of our channels**.

You can read the [IBM Blockchain API documentation](https://blockchain-starter.eu-gb.bluemix.net/api-docs/#/Channels/instantiateChaincode) to know what can you send on these API calls.

The variables used within Gitlab CI are:

* **NETWORK**_ID: network_id in IBM Network credentials
* **NETWORK**_KEY: key in IBM Network credentials
* **NETWORK**_PASS: secret in IBM Network credentials
* **CHANNEL_{ENVIRONMENT}_ID**: the channel is the name we added when we created the channel for this project (*defaultchannel* by default on IBM Blockchain)

[See the entire example recipe to CI/CD Hyperledger Fabric on IBM Blockchain](https://gist.github.com/javaguirre/24130c063131f7c5a0bee1022b4ebdac).

# Enter IBM Blockchain

![](/assets/images/hl-fabric.png)

The **IBM Blockchain** platform is, by far, the easiest way we used to build a Hyperledger Fabric network, it has also a nice API to interact with it remotely, it saves a lot of time and pain not needing to administer any of the typical components within a Fabric network. There are only some details we need to take into account when using it:

* When we deploy several smart contracts with the same name it might be difficult to know which is the last one deployed, there is no chaincode grouping by name.
* There is no way of archiving old chaincode release, so we end up with many chaincode versions in the chaincode list.
* There is no way of telling IBM Blockchain that my smart contract, built in Go, has an external dependency.
* Logging is not integrated in the platform, it opens a Kibana dashboard for us to see the logs (I’m not sure if this is good or bad, but the experience is weird).

# Conclusion

Once CI/CD is well configured, creating and instantiating chaincode on IBM Blockchain is a breeze, but there are some caveats we need to overcome to get the most of their platform.

![](https://media.giphy.com/media/KINAUcarXNxWE/giphy.gif)

This post was written on our official [blog](https://medium.com/theneonproject/testing-chaincode-on-hyperledger-fabric-22b50a3c1af1).
