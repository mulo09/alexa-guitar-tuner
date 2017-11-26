# Alexa Guitar Tuner

Guitar Tuner skill for Amazon Alexa

## App structure
An Alexa skill is made of two distinct parts:
- the skill itself, which lives on the Alexa platform. It contains the interaction model, and the skill config.
- the skill service, which is cloud based and acts as the logic for the skill.

This repo contains the code for the skill service hosted on AWS lambda.

The source code lives in the `src` directory and gets built to the `lambda` directory.

## Development
### Getting Started
Clone this repository && `npm install` dependencies

### Developing using bst *(preferred)*
The `bespoken-tools` (`bst`) npm package provides a range of tools that make developing Alexa skills easier, including a lambda proxy service, and an Alexa emulator.
Both of these can be used from the CLI or in a Node environment, which is great for E2E testing.

`npm run dev` will build the lambda using babel in watch mode, and also deploy the lambda to bespoken's proxy server, also in watch mode.
You can interact with with proxy server using the `bst speak` command.

`bst speak fizz buzz {5}` will send the same request to the proxy service as if you had said *'Alexa, fizz buzz five'* to an actual Alexa device. Note that slot values go in curly braces.

`npm run test` will build the lambda using babel, and then run the tests. The `__tests__` directory at the project root contains end-to-end tests that make use of the lambda proxy and Alexa simulator.

N.B. the build generated by `npm run test` and `npm run dev` use environment variables from the `.env` file.
N.B. the bst Alexa simulator uses the raw intent schema and sample utterances in the speechAssets file. These files are for local development/testing only.

## Building
- `npm run build:production` will compile the `src` directory into commonjs in the `lambda` directory and create a `package.json` for the lambda code.

## Deployment
- Zip the `lambda` directory and deploy using the AWS CLI

## Alexa Concepts
### Interaction Model
The interaction model maps the users spoken input to a request that gets sent to the skill service.
It is made up of *Intents*, *Slots Types*, *Sample Utterances* and, optionally a *Dialog Model*.

#### Intents
An intent represents an action that fulfills a user’s spoken request. Intents can optionally have arguments called *slots*.

#### Slots
A slot is to an intent what an argument is to a JavaScript function.
Like function arguments, slots have types.
The Alexa platform provides a number of built in slot types, such as numbers and US cities.
Some of these slots types, like US Cities can be extended to include custom examples.
You can also provide custom slot types, such as pizza toppings.
When defining custom slot types, it is recommended to provide example values, along with synonyms.

#### Sample Utterances
An utterance is used to map what Alexa hears to an intent.
You can provide a list of sample utterances to help with this mapping - the more samples provided for each intent, the more likely it is that Alexa will understand the users request.
Sample utterances can contain slots.

## Alexa Overview

When the user speaks to Alexa, Alexa uses the provided *interaction model* to figure out what the users intention was, and sends this to the service, along with any slots.

In the service code, each intent has its own handler function, which gets executed on receipt of a request with that intent type.

For example, if the interaction model defines the following intent:
- type: `FizzBuzzIntent`
- slot:
  - name: `number`
  - type: `AMAZON.NUMBER`
- sample utterance: `fizz buzz {number}`

When the skill in invoked with command *'fizzbuzz fifteen'*, Alexa will use the sample utterance and `AMAZON.NUMBER` slot type to figure out that the correct intent to use is `FizzBuzzIntent`, so will send a request to the service with an intent type of `FizzBuzzIntent`, and a `number` slot with the value `15`.

When the request is received by the service, the `FizzBuzzIntent` handler function will be invoked, and will eventually return a response to the Alexa platform.
