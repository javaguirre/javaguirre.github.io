---
title: 'Testing chaincode on Hyperledger Fabric'
date: 2019-01-21 22:21:00 +0000
tags: [blockchain,hyperledger]
cover: '/assets/images/chains.jpeg'
logo: '/assets/images/profile.png'
classes: wide
---

[Hyperledger](https://www.hyperledger.org/) is the Blockchain family of tools and frameworks for permissioned networks. At [The Neon Project]() we've been
working with solutions built with [Hyperledger Fabric](https://www.hyperledger.org/projects/fabric) for a while now.

Hyperledger Fabric is a complex system to deploy and it’s important to be careful when we configure certificates, open ports or assign roles, among other things. Luckily there are some good documentation articles from Hyperledger you could read to extend your knowledge if you are interested, such as [how to build your first network](https://hyperledger-fabric.readthedocs.io/en/latest/build_network.html) or [writing your first application](https://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

At The Neon Project, we take pride in making real stuff with Blockchain, working on smart contracts and doing CI/CD on a BDD environment for those contracts.

Our workflow integrates different platforms in our pipeline: It starts on Github, goes to Gitlab CI and finally deploys the smart contract on the IBM Blockchain platform.

![](/assets/images/gitlab_ci_pipeline.png)

Today we want to talk about the first part that makes our development faster, the unit, integration and acceptance tests for the chaincode.

## Test all the things!

At The Neon Project, we have automated testing at our core. [We apply Continuous Integration and Continuous Delivery](https://en.wikipedia.org/wiki/CI/CD) in all our projects,
so it made sense for us to create a more robust test suite for our chaincode.

We decided to go with Go, static typing, Go structs and the [examples in the Hyperledger Fabric official repositories](https://github.com/hyperledger/fabric-test) made testing easy and straightforward.

![](https://media.giphy.com/media/LFmxDLUTQH7dS/giphy.gif)

To make Hyperledger Fabric chaincode easier to test, the developers have created a [MockStub](https://godoc.org/github.com/hyperledger/fabric/core/chaincode/shim#NewMockStub) struct. A mock is a service simulating functionality so you don’t call the real one while testing. For example, if we use a service to send SMS, we don’t want our tests to send SMS every time we run them, that’s why we usually use a mock, to simulate we call the method and its response. There are still several commands that [aren’t implemented in the mock needed for some of the MockStub](https://github.com/hyperledger/fabric/blob/release-1.4/core/chaincode/shim/mockstub.go#L282-L293).

## Run the tests

To run the tests locally, we use a *Dockerfile* that builds our environment. The **Dockerfile** would be something like this:

{% gist 41037c3555a82ef7767d9e8a781070f0 %}

We’re using **Go v1.10** and copying our project code to **/go/src/app**.

We need to install the dependencies, we’ll use [hyperledger/fabric-sdk-go](https://github.com/hyperledger/fabric-sdk-go), [testify/assert](https://godoc.org/github.com/pgpst/pgpst/internal/github.com/stretchr/testify/assert) to have a nice way of asserting in our tests and optionally if we do acceptance tests, [cmd/godog](https://github.com/DATA-DOG/godog).

We’ll need **libltdl-dev** and install in the Fabric library manually in the **$GOPATH**, it didn’t work for us at the time, but this might be easier with the new version of Fabric.

We define the path where our chaincode lives (*$GOPATH/src/gitlab.com/TheNeonProject/mychaincode*) and run the tests with go test -v ..

To run this locally we use two commands, one to build the Docker container, and another one to run the tests.

{% gist 97caa93ef2503173697f7919fccbf077 %}

We’ll get a response like this if everything went nicely.

![](/assets/images/test_passing.png)

# Code Example

But talk is cheap, so we’ll show you a code example. :-)

![](https://media.giphy.com/media/r056JQBFkE8QE/giphy.gif)

Here you have a chaincode example with a method `getAsset`. Read through to see how it looks and we’ll explain right afterwards.

{% gist 53ae0acc97f4cf8d73a45645fe6ed666 %}

This code is for a smart contract with a method getAsset.

The **Invoke** method is the entry point when calling our smart contract via *RPC*; if the method is not supported it will return an error.
That would be a very good initial test to check if our test environment is correctly configured. :-)

The **getObjectAsBytes** method is just used to return a saved object as a response in the method call.

So going back to the **getAsset** method, it receives a parameter, which is the ID of the resource and, if it exists, it returns the result as JSON.

Now you know what the code does, let’s create some tests for this beast, because, as great chefs do, we always need to taste the recipe before serving.

![](https://media.giphy.com/media/l2YWuhILzuqDcexaw/giphy.gif)

# Tests

We’re going to **create a test suite with two main test cases**:

- Test when the ID sent doesn’t exist, we’ll assert the error.
- Test when the ID sent does exist, we’ll assert the response is good and an attribute of the object (the name) is present.

{% gist 3d6c88ea463259b5582950dada0eee69 %}

We follow [AAA (Arrange Act Asset)](http://wiki.c2.com/?ArrangeActAssert) principle to see the different parts of the test cases easily. In the different part of the test cases we can see:

In this case, the **Arrange** would need to initialize the smart contract “connection” and, in case of a valid id, **create the Asset with its name**.

In the **Act** part of the test, we’ll do mostly the same, use the method name, the ID, and in case of creation or append(edit), the JSON object
with the payload.

In the **Assert** we’ll check if the response is *OK* or *Error*, and we can also check with the variables **res.Message** or **res.Payload** if the content
of the response is what the right one.

That’s it for now! In following articles we’ll talk about how to do BDD for the chaincode, data format manipulation for chaincode methods and Continuous Integration / Continuous Deployment for our chaincode on an Hyperledger Fabric network.

![](https://media.giphy.com/media/ch1Z4rUWBZBnO/giphy.gif)

This post was written on our official [blog](https://medium.com/theneonproject/testing-chaincode-on-hyperledger-fabric-22b50a3c1af1).
