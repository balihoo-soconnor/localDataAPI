# Local Data API
This describes the javascript client for interfacing with the local data api, allowing affiliate locations to access data.

## Initial Setup
The purpose of the api is to allow locations to be able to access data pertinent to themselves. The library consists of a javascript object. In order to be able to use the object, the brand must a call to obtain a client api key and client id for each location it wants to grant access to. This typically occurs at the brand server level, and the client id and client id and api key are then injected into a dynamic page (via php or some other mechanism) so that it is accessible by the javascript object.

To make the call the brand sends the following get request:

https://bac.balihoo-cloud.com/v1.0/genClientAPIKey

It requires the following query parameters (all are required):

-apiKey (String): This is the brand specific api key provided by Balihoo to the brand.
-brandKey (string): This is the brand specific indentifier, provided by Balihoo to the brand.
-locationId (String): This is the brand specific unique location identifier that the client api key and id are being generated for.
-groupId (String): This is brand specific group identifier. It is used to provide access to different levels of the api. Currently this field is not used internally
-userId (String): This is the user id of the requestor of the client api key. It is uses for audit purposes. Currently this field is not used internally.
