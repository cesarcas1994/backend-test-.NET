# backend-test-.NET
 It is an api built in .NET in order to perform from VS code, store it in azure web app, use (CI-CD) in Azure Dev Ops and perform the corresponding unit tests

## Instructions

1 - Write an HTTP endpoint for users to be able to create/read/edit/delete a delfometrics json_input for an order.

2 - Write an HTTP endpoint for users to be able to create/read/edit/delete a delfometrics json_output for an order.

The API should follow typical RESTful API design pattern.
The data should be saved in the DB.
Provide proper API documentation.
Proper error handling should be used.

### Bonus

1. Provide proper unit tests.
2. Use Redis Cache.
3. API Swagger documentation

### Azure Logic app -> azure .net core azure web api

** Pendiente explicar porque se hace este cambio de arquitectura y beneficios de la nuvea arquitectura**


*Cambiarlo a "algo util" como la api node js que sustituya a logic app en delfometrics-alpha prod
** Pasarlo como comit

## System overview and key concepts

** Insertar imagen de la arquitectura **

*** Explicar diseño ***

### Key concepts

** Insertar imagen de la arquitectura **

*** Explicar Key concepts ej : CRUD, Azure Redis Cache***

## Detail Design

### Web API

** Diseño mockeup del CRUD **

A set of RESTful endpoints will be exposed from the FeedbackService using ASP.NET Core, all of them requesting Basic Authentication and the userId (the customer who owns the order) in the headers.
* **OrderFeedback**: Used to rate existing orders. Methods:
  * **[GET]** /api/OrderFeedback/{orderId}: Gets the feedback of an existing order that matches the orderId parameter. The response is a JSON representing the Feedback (See data representation below).
    * If the order does not exist or has not been rated, or if the userId provided in the headers does not exist or does not own an order that matches the orderId, the action is aborted. Error messages displayed:
      * Order with Id {0} has not been rated. There is no feedback to retrieve.
      * Unable to retrieve order with orderId {0}
      * User {0} does not own an order with Id {1}

  * **[GET]** /api/OrderFeedback/GetLatest/{rating}:Asynchronously gets the last 20 order's feedback left by users filtered by the given rating numeric value, 
    in descendant order based on its creation time. If no rating is provided, the last 20 orders' feedback will be retrieved without filtering. 
    The response is a list of Feedback objects.
      * If the rating is not between 1 and 5, the action is aborted. Error message displayed:
        * Invalid rating. The rating must be between 1 to 5.

  * **[POST]** /api/OrderFeedback/{orderId}: Asynchronously posts feedback on an existing order (must match orderId). The order must haven't been rated yet.
  The response is a Feedback object.
    * If the orderId used refers to an order that has already been rated (i.e. feedback exists) or if the user does not own the order, the action is aborted. Error messages displayed:
      * The order you are trying to rate has already been rated. Try modifying its feedback instead.
  
  * **[PUT]** /api/OrderFeedback/{orderId}: Asynchronously updates an order's feedback by the given orderId. The response is a Feedback object with the updated values.
    * If the order does not exist or has not been rated, or if the userId provided in the headers does not exist or does not own an order that matches the orderId, the action is aborted. 
      Also checks for valid rating. Error messages displayed:
      * Unable to retrieve order with orderId {0}
      * User {0} does not own an order with Id {1}
      
  * **[DELETE]** /api/OrderFeedback/{orderId}: Asynchronously deletes an order's feedback for an order that matches the given orderId. The response is a message notifying that the feedback was successfully deleted.
    * If the order does not exist or has not been rated, or if the userId provided in the headers does not exist or does not own an order that matches the orderId, the action is aborted. Error messages displayed:
      * The feedback you are trying to delete does not exists.

### Caching Manager

*** Explicar las caracteristicas de cache implementedas ***

To reduce the number of calls made to the database for entity information, a caching manager will be implemented within the .NET Core application. This implementation will be a generic approach where, 
as a standard practice, the caching manager will have the following characteristics:
  * To periodically refresh to retrieve entity information (10 mins default). This should allow for enough usage by the web api without frequent hits on the databases.
  * The cache expiry value shall be a configuration value found within the .NET Core application configuration file for the specific objects which are leveraging the cache.
  * Calls to the database will be limited when the entities cache object is retrieved for the first time or after the expiry has expired, and there is a need for entity data.

The caching manager is provided by a shared library called CachingManager. It will provide all the necessary interfaces for managing distributed caching mechanisms (Redis, In-Memory, SQL server) for each required entity. 
A distributed cache instance is injected into the cache using a built-in IoC container with DI supported through the lifetime of the client. 

### Data Access

*** Insertar imagen de database ***

** Explicar conceptos y por que se uso esa en especial **

## API Documentation

#### Swagger

*** imagen Swagger y direccion url ***

#### Postman

*** collection de Postman ***
