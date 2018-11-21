---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ


toc_footers:

includes:
  - errors

search: true
---

# Introduction

Welcome to the Gen Eversince Client Doc! You can use this client to communicate with our fantastic [Eversince API](https://github.com/digital-generation/gen-eversince).

Eversince API is an api that we use to register all kinds of events for further consultation.

This example API documentation page was created with [Slate](https://github.com/lord/slate). Feel free to edit it and use it as a base for your own API's documentation.

# Getting started
For install our api client use this command.

```shell
$ npm install gen-eversince-api-client
```
# Configuration
## API endpoint.

To set the url(just once):

```javascript
const eversince = require('gen-eversince-client');

eversince.setUrl('localhost:3000');
```
## Authentication

To set the API KEY (just once):

```javascript
const eversince = require('gen-eversince-client');

eversince.setAuthKey('ThisIsMySecretKey');
```
# Usage
## Register an event
You can register a new Event and Eversince will store it for further consultant. An event can be described as follows.

### Input
As an Input the function receives an Event object that has the following definition.

 Field Name | Type | Description 
-------------- | -------------- | --------------
actor_id | Number | Id of the user that makes the event.
actor_type | String | Role of the user.
meta | Object | Optional parameter. Object to store extra information of an event. See below table to get detail on what properties could be in this object.
name | String | Name of the event.
subject_id | Number | 
subject_type | String |

Meta fields:

 Field Name | Type | Description 
-------------- | -------------- | --------------
country_id | Number | Id of the country according to the global_countries table. 
location_id | Number | Id of the location according to the global_locations table.
program_id | Number | Id of the programs  according to the global_programs table.
actor_name | String | Optional parameter. Name of the actor that makes the action. 
subject_name | String | Optional parameter. Name of the subject that receives the action.

### Code example
```javascript
const eversince = require('gen-eversince-client');

const registerEvent = async () => {
  let eventInstance = {};
  eventInstance.actor_id = 1;
  eventInstance.actor_type = 'Actor_Type';
  eventInstance.meta = {};
  eventInstance.meta.country_id = 11;
  eventInstance.meta.location_id = 1;
  eventInstance.meta.program_id = 121;
  eventInstance.meta.actor_name = 'actor_ name';
  eventInstance.meta.subject_name = 'subject_ name';
  eventInstance.name = 'Name';
  eventInstance.subject_id = 0;
  eventInstance.subject_type = '1';
  let result = await eversince.registerEvent(eventInstance);
  console.log(result);
}

registerEvent();
```
### Output

The above command returns JSON structured like this:

```json
{
  code: 'success',
  data: { id: 'FzE4D3FLJSXToORn1I7I' },
  type: 'EvReg' 
}
```
 Field Name | Description | Possible values
-------------- | -------------- | --------------
code | String indicating the status for the request. | 'success', 'error'
data | In case of success response the id of the new event. Otherwise error info. | <ul><li> success: ``` {id:'someId'}  ```</li><li>error: ``` 'subject_id needs to be a number'  ```</li>
type | A type of error in case is present | EvReg: Success case for registering an Event.See [Errors](#errors) for more types.

## Get events
You can query all the events that have been registered with some specific filters.

### Parameters
As an Input the function receives an object that contains all the filters

 Field Name | Type | Description 
-------------- | -------------- | --------------
field | String | Valid values
start | Timestamp | Start range that apply to the field that you specify on field
end | Timestamp | End range that apply to the field that you specify on field.
from | Timestamp | Very similar to start but the porpouse of this field is to give pagination a pivot to make queries from an specific timestamp
actor_id | Number | Filter by an actor_id
 
### Code example
```javascript
const eversince = require('gen-eversince-client');

const readEvents = async () => {
  let result =await eversince.getEvents({ 'actor_id': 5 });
}
readEvents();
```
### Output

The above command returns JSON structured like this:

```json
{
  code: 'success',
  data: [
    {
      "subject_type": "1",
      "name": "Name",
      "actor_id": 5,
      "actor_type": "Actor_Type",
      "subject_id": 0,
      "created_at": 1540831644925,
      "id": "FzE4D3FLJSXToORn1I7I",
      "meta": {
          "location_id": 1,
          "program_id": 121,
          "created_at": 1540831644925,
          "country_id": 11,
          "actor_name": "actor_name",
          "subject_name": "subject_name",
      }
    }
  ],
  type: 'GetEv' 
}
```
 Field Name | Description | Possible values
-------------- | -------------- | --------------
code | String indicating the status for the request. | 'success', 'error'
data | In case of success response an array of max 100 elements of Events that match the query parameters. Otherwise error info. | 
type | A type of error in case is present | GetEv: Success case for registering an Event. See [Errors](#errors) for more types.