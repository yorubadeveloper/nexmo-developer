---
title: Create your application
description: This topic shows you how to create a Vonage application, users and tokens.
navigation_weight: 1
---

# Create your application, users and tokens

In order to use the Client SDK, there are three things you need to set up before getting started:

* [Vonage Application](/application/overview) - an Application which contains configuration for the app that you are building.

* [Users](/conversation/concepts/user) - Users who are associated with the Vonage Application. It is expected that Users will have a one-to-one mapping with your own authentication system.

* [JSON Web Tokens, JWTs](https://jwt.io/) - Client SDK uses JWTs for authentication. In order for a user to log in and use the SDK functionality, you need to provide a JWT per user. JWTs contain all the information the Vonage platform needs to authenticate requests, as well as information such as the associated Applications, Users and permissions.

All of these may be [created by your backend](/conversation/overview). 
If you wish to get started and experience using the SDK without any implementation of your backend, this tutorial will show you how to do so, using the [Nexmo CLI](https://github.com/Nexmo/nexmo-cli).

## Prerequisites

Make sure you have the following:

* A Vonage account - [sign up](https://dashboard.nexmo.com)
* [Node.JS](https://nodejs.org/en/download/) and NPM installed
* Install the Nexmo CLI.

To install the Nexmo CLI, run the following command in a terminal:

```bash
npm install -g nexmo-cli@beta
```

Set up the Nexmo CLI to use your Vonage API Key and API Secret. You can get these from the [settings page](https://dashboard.nexmo.com/settings) in the Vonage Dashboard.

Run the following command in a terminal, while replacing `api_key` and `api_secret` with your Vonage API key and secret:

```bash
nexmo setup api_key api_secret
```

This adds this authentication information to the `.nexmorc` file in your home directory.

## Create a Vonage Application

You now need to create a Vonage application. In this example you create an application capable of handling both in-app Voice and in-app Messaging use cases.

1) First create your project directory if you've not already done so.

2) Change into the project directory you've now created.

3) Use the following command to create a Vonage application with Voice and WebRTC capabilities. Replace the webhook URLs with your own. If your platform restricts the inbound traffic it can receive using IP address-ranges you'll need to add the [Vonage IP addresses](https://help.nexmo.com/hc/en-us/articles/360035471331) to your allow list. The IP addresses can be fetched programmatically by sending a GET request to `https://api.nexmo.com/ips-v4`.

``` shell
nexmo app:create "My Sample App" --capabilities=voice,rtc --voice-answer-url=https://example.com/webhooks/answer --voice-event-url=https://example.com/webhooks/event --rtc-event-url=https://example.com/webhooks/rtc --keyfile=private.key
```

The application is then created.

The file `.nexmo-app` is created in your project directory. This file contains the Vonage Application ID and the private key. A private key file `private.key` is also created.

Creating an application and application capabilities are covered in detail in the [documentation](/application/overview).

## Create a User

Create a User who will log in to Vonage Client and participate in the SDK functionality: Conversations, Calls and so on.

Run the following command in your terminal to create a user named Alice: 

```bash
nexmo user:create name="Alice"
```

The output with the user ID, is similar to:

```sh
User created: USR-aaaaaaaa-bbbb-cccc-dddd-0123456789ab
```

The user ID is used to perform tasks by the SDK, such as login, starting a call and more.

## Generate a User JWT

[JWTs](https://jwt.io) are used to authenticate a user into the Client SDK.

To generate a JWT for Alice run the following command, remembering to replace the `MY_APP_ID` variable with the value that suits your application:

```bash
nexmo jwt:generate ./private.key exp=$(($(date +%s)+86400)) acl='{"paths":{"/*/users/**":{},"/*/conversations/**":{},"/*/sessions/**":{},"/*/devices/**":{},"/*/image/**":{},"/*/media/**":{},"/*/applications/**":{},"/*/push/**":{},"/*/knocking/**":{},"/*/legs/**":{}}}' sub=Alice application_id=MY_APP_ID
```

The above command sets the expiry of the JWT to one day from now, which is the maximum amount of time. You may change the expiration to a shortened amount of time, or regenerate a JWT for the user after the current JWT has expired.

> **NOTE**: In production apps, it is expected that your backend will expose an endpoint that generates JWT per your client request.

## Further information

* [More about JWTs and ACLs](/conversation/guides/jwt-acl)
* [In-app Voice tutorial](/client-sdk/tutorials/app-to-phone/introduction)
* [In-app Messaging tutorial](/client-sdk/tutorials/in-app-messaging/introduction)
