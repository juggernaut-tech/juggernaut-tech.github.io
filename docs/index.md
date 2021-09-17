# Welcome to Juggernaut Tech Pages for Developers

## Explaining the Juggernaut Ecosystem

![juggernaut-ecosystem](https://user-images.githubusercontent.com/84578353/133796401-23f4074a-dbcf-44c7-a894-f14ad6bad59d.jpeg)

## Prerequisites for Running and Developing the Juggernaut Ecosystem

There are two java libraries that must be built and installed on your local maven repository.

The core eventing models provide the event classes that are serialized across the Juggernaut eventing subsystem

```markdown
Start a Git Bash 
cd to your develop directory
clone the [Core Eventing Models Git Repo](https://github.com/juggernaut-tech/core-eventing-models.git), or update existing repo if already cloned
build the artifact (can be imported into IntelliJ as new project or built on command line)
```
![Core-eventing-models-build](https://user-images.githubusercontent.com/84578353/133824313-d60f6caf-3200-4e4f-9f83-79ec2792bc6e.PNG)

The core eventing fabric provides helper classes for eventing producers and consumers
```markdown
Start a Git Bash 
cd to your develop directory
clone the [Core Eventing Fabric Git Repo](https://github.com/juggernaut-tech/core-eventing-fabric.git), or update existing repo if already cloned
build the artifact (can be imported into IntelliJ as new project or built on command line)
```
![image](https://user-images.githubusercontent.com/84578353/133824448-7c963870-2773-4ed8-ac8d-0b593092cb4c.png)

## Running the Juggernaut Ecosystem

The event hub component connects the clients to the Juggernaut services using kafka topics.  The event hub is scalable and fault tolerant.  Multiple instances of the event hub can be started to provide both scalability and automatic failover.  The services may be standard services or bespoke services developed by clients and/or third parties.  For example, the User Service is a standard service that is provided as part of the Juggernaut ecosystem and, a Picking service could be developed by the client or third-party and plugged into the ecosystem easily.  The Data Layer will consist of one or more microservices that provide that data abstraction and persistence for the Juggernaut application.  The data microservices are best developed as REST services and are called only by the Juggernaut services using a request/reply paradigm.

**IMPORTANT: TO START SERVICES IN THE ORDER LISTED BELOW**

### Starting the Kafka Subsystem

Kafka is the backbone of the Juggernaut ecosystem.  It is used for client integration with Juggernaut services via the event hub.

```markdown
Start Command Prompt
Change Directory to your kafka 2.13 scala 2.7.1 distribution
Start the zookeeper instance
```
![Zookeeper-start-cmd](https://user-images.githubusercontent.com/84578353/133798968-c0de0528-b615-46d2-bf93-7fd51346ebe3.PNG)

```markdown
Start Command Prompt
Change Directory to your kafka 2.13 scala 2.7.1 distribution
Start the kafka server instance
```
![Kafka-start-cmd](https://user-images.githubusercontent.com/84578353/133799244-68887290-df44-4928-9fe6-5fe69568588c.PNG)

**Check the output from both commands for failure**

### Starting the Juggernaut Data Layer

The data layer is the last fundamental component in the Juggernaut application.  It should be started and in place before any services and the event hub are started.

```markdown
T.B.D. When data microservices are available
```
### Starting the Juggernaut Eventing Services

There may be many services that comprise your Juggernaut ecosystem and, all of them should be started and in place before the event hub is started.

Currently there is one standard service - [User Service](https://github.com/juggernaut-tech/user-service)
```markdown
Start a Git Bash 
cd to your develop directory
clone the [User Service Git Repo](https://github.com/juggernaut-tech/user-service), or update existing repo if already cloned
build the artifact
run the service
test the service is alive
```
![User-service-build-and-run](https://user-images.githubusercontent.com/84578353/133808273-a7f20214-31e0-4669-9930-acc4992b480e.PNG)
![User-service-client-test](https://user-images.githubusercontent.com/84578353/133810246-b4e3228f-ebe6-4987-a304-791d32ba99be.PNG)

**Repeat the steps above to start all other services in your Juggernaut application**

### Starting the Juggernaut Event Hub

This is the final piece of the Juggernaut ecosystem that binds all layers together and provides client access to their Juggernaut applicaztion.

```markdown
Start a Git Bash 
cd to your develop directory
clone the [Event Hub Git Repo](https://github.com/juggernaut-tech/event-hub.git), or update existing repo if already cloned
build the artifact
run the hub
test the hub is alive
```
![Event-hub-build-and-run](https://user-images.githubusercontent.com/84578353/133813941-5b741f67-105e-4890-a0a5-677bd3b0d5ee.PNG)
![Event-hub-client-test](https://user-images.githubusercontent.com/84578353/133816796-6f7c1589-5d27-405f-bcaa-81014bc8e37a.PNG)

### Testing the Event Hub and User Service

The demo_rest_client component provides an easy way to test integration of the event hub and services.

```markdown
Start a Git Bash 
cd to your develop directory
clone the [Demo Rest Client Git Repo](https://github.com/juggernaut-tech/demo-rest-client.git), or update existing repo if already cloned
build the artifact
run the client
test the client is alive
```
![Demo-rest-client-build-and-run](https://user-images.githubusercontent.com/84578353/133819217-e6c9158a-abf6-4ff2-ae21-fb1490713e25.PNG)
![Demo-rest-client-client-test](https://user-images.githubusercontent.com/84578353/133819487-d3378c2d-2276-4eac-a778-42cef55f8c88.PNG)

The application.yml file in the demo rest client resources defines the events available as the client:
![Demo-rest-client-event-mappings](https://user-images.githubusercontent.com/84578353/133819005-7358abca-9406-48e3-8801-1ea8418b9843.PNG)

It is possible to send an event request to the demo rest client via its REST API, which will then forward onto the event hub for service delegation.
![Demo-rest-client-fire-event-login](https://user-images.githubusercontent.com/84578353/133819685-9b947341-fce2-48a9-84d4-e3b9abe34740.PNG)

The events can now be traced through the ecosystem:
1. the demo rest client produces the event from itself when REST API is called using the EventHubClient from core-eventing-fabric:

2. the event hub receives the event:
![Demo-rest-client-integration-test-1](https://user-images.githubusercontent.com/84578353/133820077-d0490718-ad7b-43a0-88dd-d636340fb35e.PNG)

3. the event hub has a mapping for the event and forwards it to the user service:
![Demo-rest-client-integration-test-2](https://user-images.githubusercontent.com/84578353/133820268-ad8a955c-e030-40ac-b625-2b96858b1c93.PNG)

4. the user service receives the event, processes it and sends the response back to the reverse channel topic, i.e. the demo rest client:
![Demo-rest-client-integration-test-3](https://user-images.githubusercontent.com/84578353/133821788-6ab10799-ce25-49d2-87b0-5a9f3155f153.PNG)

5. the demo rest client receives the response from the user service back from its request:
![Demo-rest-client-integration-test-0](https://user-images.githubusercontent.com/84578353/133821073-0f1122fc-1df2-4a76-9c68-87cb9d5e3802.PNG)

That's it - end to end eventing!

Next up is "how to add a new event to an existing service"

```markup
W.I.P.
```

Next up is "how to create a new custom or client service"

```markup
W.I.P.
```

