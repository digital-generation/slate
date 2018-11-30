---
title: Gen-Recruitment-config

language_tabs: # must be one of https://git.io/vQNgJ


toc_footers:

includes:
  - errors

search: true
---

# Introduction

Welcome to the gen-recruitment-config! 

This repository has been created to simplify  the way you can get or update the current configuration of the recruitment process.

https://github.com/digital-generation/gen-recruitment-config


# Our recruitment Process Concepts

A recruitment process is the flow where the applicants and recruiters make different actions such as filling forms, answer exams, interviews, grades, etc. As an output of the recruitment process we know which applicants are more elegible to participate in Generation.

This library is helpful to configure some actions that the system do during this process:
- Communicate with our applicants (email).
- Let the students know their current status on the recruitment process (status explanaitions).
- Establish rules to identify what students are not/probably elegible to be part of Generation. (Demographic rules, TypeformQuestions, TypeformAnswers).

## Emails
We use a configuration that let us send emails to applicants when they reach an status on an specific stage. 
For example let's say that we want to invite applicants to fill a form when they have finished the registration form an specific program of Kenya. The email rule looks like this:

````json
{
  "type": "program",
  "programId": 191,
  "text": "Thank you for completing the registration form.Your next step is to complete the application.",
  "status": "passed",
  "stage": "registrationForm",
  "scheduled": []
}
````
## Status Explanaitions
Every time the student finish an stage we provide them an status explanaition in our platform. It's very similar to the email rule but with different communication channel.

Here's an example of the status explanation rule.

````json
{
  "type": "program",
  "programId": 1,
  "text": "Gracias por completar el formulario de Generation Spain. ¡Enhorabuena, has superado la primera fase del proceso! A continuación, te invitamos a realizar el resto del proceso que consta de los siguientes pasos: formulario detallado, subida de documentos acreditativos, tests básicos de selección y video entrevista. ¡Te esperamos! Generation Spain.",
  "status": "passed",
  "stage": "demographicForm"
},
````
## Demographic rules
This rules are to determine the applicant status based on his answers. 

For example let's suppose that Generation has the following rules to give a training course: 
- Age between 18 and 30. (yes, they are good candidates)
- Age equals 17. (they are good candidates too, but we prefer to wait until 18 years old, please flagged them to decide if they are going to pass).
- Age Above 30 (reject them). 

Also in case they are flagged or rejected Generation wants to give a message.
This "rules" can be persisted in the following way:

````json
{
  "key": "birthDate",
  "type": "location",
  "programId": 222,
  "locationId": 121,
  "flagValues": {
      "equals": 17
  },
  "flaggedExplanation": "We have noted that you are not in our target age range, but under special circumstances you may still be eligible for our program. We will reach out to you to discuss!",
  "killerValues": {
      "outsideExclusive": [
          18,
          30
      ]
  },
  "rejectedExplanation": "Unfortunately, we can only accept applicants between the ages of 18 and 29. We wish you the best on your journey."
},
````
## TypeformQuestions
As you know Generation has presence in many countries that speaks many languages, so we use typeformQuestions rules to know which field is referenced with the question. So we can ask "When did you born?", "Date of birth", ... but with typeformQuestions we know we are asking birthdate.

````json
{
  "type": "db",
  "countryId": 1,
  "text": "Date of birth",
  "fieldName": "birthDate"
},
{
  "type": "db",
  "countryId": 1,
  "text": "When did you born?",
  "fieldName": "birthDate"
},
````

## Typeform Answers
As you know Generation has presence in many countries that speaks many languages, so we use typeformAnswer rules to know when the answer is equivalent. So we can receive an answer to Gender "Male", "Men", ... but with typeformAnswers we know that all of that answers are refering to Male and that we store those answers as "M"

````json
{
  "countryId": 1,
  "field": "gender",
  "values": [
      {
          "answer": "Male",
          "value": "M"
      },
      {
          "answer": "Men",
          "value": "M"
      },
  ]
}
````

# Getting started

For install our api client use this command.

````shell
$ npm install gen-recruitment-config
````

# Configure
## NOSQL endpoint.

To set the url of the no-sql services that store the rules(just once):

````javascript
const recruitmentConfig = require('gen-recruitment-config');

recruitmentConfig.setUrl('localhost:3000');
````
# Get Whole Configuration

You can use the following methods to get the whole configuration of an specific kind of rule. The result of each one of the following methods will be a promise that resolves an array of rules.
- getDemographicRules

````javascript
let rulesArray = await recruitmentConfig.getDemographicRules()
````
- getEmailMessages

````javascript
let rulesArray = await recruitmentConfig.getEmailMessages()
````
- getStatusExplanations

````javascript
let rulesArray = await recruitmentConfig.getStatusExplanations()
````
- getTypeformAnswers

````javascript
let rulesArray = await recruitmentConfig.getTypeformAnswers()
````
- getTypeformQuestions

````javascript
let rulesArray = await recruitmentConfig.getTypeformQuestions()
````

See Demographic, StatusExplanation, TypeformAnswers, TypeformQuestions sections to get field detail.

# Set Rules
A set operation needs an scope(country, program or location), because all the rules that match that scope will be deleted and then all the new rules will be added.
## Understanding scopes
An scope is the area that affects a rules. There are two types of scope:
- program: This kind of scope needs to specify programId and indicates that all the recruitment process that belongs to a program are affected by this rule.
- location: This kind of scope needs to specify programId and locationId. Indicates that all the recruitment process that belongs to this program and location are affected.

In case that two rules affect a recruitment process the location has greater priority than the program.

## Code example.
You can set the rules for an specific scope for the following rules:

- setDemographicRules

````javascript
let response = await recruitmentConfig.setDemographicRules()
````
- setEmailRules

````javascript
let response = await recruitmentConfig.setEmailRules()
````
- setStatusExplanationRules

````javascript
let response = await recruitmentConfig.setStatusExplanationRules()
````

As an output of each of those methods you will receive a promise that resolves this:

````javascript
//success response
{
  ok: true
}
// error response
{
  ok: false,
  errors: []
}
````

# Add rules

An add operation needs an scope(country, program or location or may be more info),
because if there's a rule that already contains a rule then the rules will be merged.

## Code example.

You can add rules for an specific scope for the following rules:

- addTypeformAnswerRules

````javascript
let response = await recruitmentConfig.addTypeformAnswerRules()
````
- addTypeformQuestionRules
````javascript

let response = await recruitmentConfig.addTypeformQuestionRules()
````

As an output of each of those methods you will receive a promise that resolves this:

```javascript
//success response
{
  ok: true
}
// error response
{
  ok: false,
  errors: []
}