# Local Data API
This describes the javascript client for interfacing with the local data api, allowing affiliate locations to access data.

## Initial Setup
The purpose of the api is to allow locations to be able to access data pertinent to themselves. The library consists of a javascript object. In order to be able to use the object, the brand must a call to obtain a client api key and client id for each location it wants to grant access to. This typically occurs at the brand server level, and the client id and client id and api key are then injected into a dynamic page (via php or some other mechanism) so that it is accessible by the javascript object.

To make the call the brand sends the following get request:

https://bac.balihoo-cloud.com/v1.0/genClientAPIKey

It requires the following query parameters (all are required):

- apiKey (String): This is the brand specific api key provided by Balihoo to the brand.
- brandKey (string): This is the brand specific indentifier, provided by Balihoo to the brand.
- locationId (String): This is the brand specific unique location identifier that the client api key and id are being generated for.
- groupId (String): This is brand specific group identifier. It is used to provide access to different levels of the api. Currently this field is not used internally
- userId (String): This is the user id of the requestor of the client api key. It is uses for audit purposes. Currently this field is not used internally.

Returns a javascript object:
```
{clientId: xxxxxxxxx, clientApiKey: xxxxxxxxx}
```

## API
In order to use the API the javascript object was must be included in the code.
```
<script src='bc.balihoo-cloud.com/assets/localDataAPI/localDataAPI.js' type='text/javascript'></script>
```
Once the library has been included, the library is initialized by creating a new local connection object, this is done by passing in the clientId, and clientApiKey from the above step into the LocalConnection method. The method is referenced from the 'balihoo' global variable that will be created when the library runs.

```
var connection = new balihoo.LocalConnection(clientId,clientApiKey)
```
Once a local connection object is obtained, the following method calls can be used.
NOTE: All methods return a promise.

## Methods
###connection.getAllCampaigns()
This method returns a promise that when fulfilled will return a json representation of all campaigns that reference the current location.
The returned json is as follows:
```
[
  {
    "id": 34,
    "title": "Test Campaign",
    "description": "a campaign that is an example",
    "start" : "2014-05-02",
    "end": "2014-06-05",
    "status": "active"
  },
  {
    "id": 23,
    "title": "Another Campaign",
    "description": "the other example campaign",
    "start" : "2014-01-02",
    "end": "2014-01-28",
    "status": "active"
  }
]
```

###connection.getAllTactics(campaignId: Integer)
This method takes the id of a given campaign, and then will return a promise that when fulfilled will contain a json array of all the tactics that the current location is referenced in for the given campaign.
A sample json return is as follows:
```
{
  "campaignId": 12,
  "tactics": [
    {
      "id": 12,
      "title": "Tactic Name",
      "start": "2014-05-02",
      "end": "2014-06-05",
      "channel": "Email",
      "description": "A tactic description would go here if it exists",
      "creative": "http://url.to/creative/image/or/html"
    },
    {
      "id": 24,
      "title": "Another Tactic Name",
      "start": "2014-01-02",
      "end": "2014-01-12",
      "channel": "Display",
      "description": "Another tactic description would go here if it exists",
      "creative": "http://url.to/creative/image/or/html"
    }
  ]
}
```

###connection.getAllCampaignsAndTactics()
This method will return a promise that when it fulfills combines the above two api calls into one. That is it will return a json array of all campaigns the current location is used in. In addition each campaign will contain a json array of all the tactics that the current location was used in for that campaign.

Sample json appears below:
```
[
  {
    "id": 34,
    "title": "Test Campaign",
    "description": "a campaign that is an example",
    "start" : "2014-01-02",
    "end": "2014-06-05",
    "status": "active",
    "tactics":  [
      {
        "id": 12,
        "title": "Tactic Name",
        "start" : "2014-05-02",
        "end": "2014-06-05",
        "channel": "Email",
        "description": "A tactic description would go here if it exists",
        "creative": "http://url.to/creative/image/or/html"
      },
      {
        "id": 24,
        "title": "Another Tactic Name",
        "start" : "2014-01-02",
        "end": "2014-01-12",
        "channel": "Display",
        "description": "Another tactic description would go here if it exists",
        "creative": "http://url.to/creative/image/or/html"
      }
    ]
  },
  {
    "id": 23,
    "title": "Another Campaign",
    "description": "the other example campaign",
    "start" : "2014-01-02",
    "end": "2014-01-28",
    "status": "active",
    "tactics": []
  }
]
```

###connection.getMetricsForTactic(tacticId: Integer)
This method takes the id of a given tactic, and returns a promise that when it fulfills contains a json object with metric information. The json object returned depends on the channel of the tactic. 
Example json responses appear below:
####Email
```
{
  "campaignId": 34,
  "tacticId": 2,
  "channel": "Email",
  "sends": 45,
  "opens": 12,
  "clicks": 12
}
```
####Paid Search
```
{
  "campaignId": 34,
  "tacticId": 3,
  "channel": "Paid Search",
  "clicks": 45,
  "spend": 125.12
}
```
####Display
```
{
  "campaignId": 34,
  "tacticId": 1,
  "channel": "Display",
  "impressions": 95,
  "spend": 125.12
}
```

###connection.getWebsiteMetrics()
This method will return a promise, that once resolved will contain a json object with website metrics for the current location's local website. In order for any data to be returned the current location must have a matching local website, or empty data will be returned. 
Example json appears below:
```
{
  "campaignId": 34,
  "localSiteUrl": "http://www.balihoo.com",
  "visits": {
    "total": 125,
    "organic": 45,
    "direct": 12,
    "referral": 34,
    "paid": 12,
    "newVisitsPercent": 0.43
  },
  "leads": {
    "total": 123,
    "totalWeb": 34,
    "totalPhone": 12,
    "organicWeb": 4,
    "paidWeb": 30,
    "organicPhone": 4,
    "paidPhone": 8
  }
}
```
