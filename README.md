# NAB-Solution Design Challenge
## I. High level diagram:
### 1. Architecture diagram
  ![image](https://user-images.githubusercontent.com/24229179/179405022-6f93ba1d-2852-4b0c-8ad6-90fe20285982.png)
  - Because the end-user can access from website or mobile app or the retailers' system. So for the gateway I prefer to Backend For Frontend (BFF) architecture, which could easier for maintain and extend in the future.
  - The end-user must login to the system to check out the cart as well as register their store to the system. So I to use Keycloak as the main IdP, because Keycloak can connect to others Provider like Google or Facebook then the end-user can user their Google account or Facebook account to login. The other thing is Keycloak also provides Oauth2 + OpenIdConnect for authen/author process.
  - For service discovery, we can use application-level service discovery like `Eureka` or platform-provided service discovery like `Kubernetes`
    * For application-level service discovery, we can deploy your microservices to difference platfrom like a part on your local server and use cloud env only for scaling purpose.
    * For platform-provided service discovery, you no need to use any third-party application but the cons now, you must deploy your microservices to a standalone platform
  - For the microservices, I think we will have 4 services:
    * Store: is a service for the retailers to manage their store as well as their products
    * Purchase: is a service to manage the orders of the users
    * Invoice: is a service to manage the invoices and payments
    * Delivery: is a service to manage the status of delivery from the retailers as well as their freight forwarder
  - For DB, I prefer to apply database per service pattern. So could have multi database instance for each microservice or just 1 database instand but use multi-schema and each schema is correcsponding to a microservice.
  - For message broker, I prefer to use Apache Kafka for emitting Events and Async Commands for message brokers. Why Kafka? because it's not just a message broker, it's a stream processing when we can divide a topic into many partitions as well as keep the order between messages for sure that a sequence of related messages could be processed in order. Or we can group consumers together for sure that only one consumer of that group can receive and process the message
  - For Sync Commands, I prefer to use gRPC because it is based on HTTP2.0, which is faster than HTTP 1.1, as well as we can use the .proto file as an API document.
### 2. Package diagram:
  ![image](https://user-images.githubusercontent.com/24229179/179405247-3263884c-c1a4-4383-a1dc-4a1f1049ba35.png)
  - For the implementation of a Microservice, I prefer to user Hexagonal architecture, which could help our microservice easier to maintain.
  - A Microservice project will be divided into many Maven modules:
    * The application module includes:
      + domain package, that contains Entity, Aggregate Root, Value Object (Domain-driven design)
      + port.in package, that contains all of the interfaces for the [use-cases](https://github.com/vlancer37/NAB-Challenge/blob/main/README.md#iv-use-case-diagram-for-store-microservice) that we have
      + service package, that contains all of the implementations from the interfaces declared inside port.in package
      + port.out package, that contains all of the interfaces for the output of our microservice (dependency inversion)
    * adapter.in package contains all of the Maven modules, that implement for:
      + api for REST API calling from UI
      + scheduler for running batch jobs
      + stream for receiving Events or Async Commands from the message broker
      + api4thirdparty for exposing REST API to third-party application
      + gRPC for receiving Sync Commands from another microservice
    * adapter.out package contains all of the Maven modules, that implement for:
      + persistence for CRUD to database
      + cache for retrieving or updating cache data
      + stream for sending Events or Async Commands to the message broker
      + gRPC for calling Sync Commands from another microservice
## II. ERD diagram for store microservice:
  ![image](https://user-images.githubusercontent.com/24229179/179405437-dd9a3a66-bfe5-41f7-893d-80444b3ab006.png)
## III. Sequence diagram:
### 1. Authen/Author:
  ![image](https://user-images.githubusercontent.com/24229179/179405534-8aab50c9-f288-4d85-b5d0-8a31a19ce268.png)
### 2. Add new product sequence:
  ![image](https://user-images.githubusercontent.com/24229179/179418015-d6280931-c638-488c-a799-98c39b64c2c5.png)
## IV. Use-case diagram for store microservice:
  ![image](https://user-images.githubusercontent.com/24229179/179418997-62f5578a-380a-4ff1-8159-d08eea7e4221.png)

