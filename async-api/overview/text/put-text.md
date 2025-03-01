---
id: put-text
title: PUT Text API
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


The PUT Async Text API allows you to process any text payload to append the transcription of the previous conversation.

It can be useful in cases where you have new information like chats, emails or messages which keep generating for single entity. Using this API, you can add all the new entity data to the existing Conversation ID (`conversationId`).

:::info
If there are multiple requests are submitted for the same Conversation ID, all the requests will be processed synchronously in order to maintain the order of the requests for the conversation.
:::

### HTTP REQUEST

`PUT https://api.symbl.ai/v1/process/text/:conversationId`

### Example API Call

:::info
Before using the Async Text API you must get the authentication token (`AUTH_TOKEN`) from [our authentication process](/docs/developer-tools/authentication).
:::

<Tabs
  defaultValue="cURL"
  values={[
    { label: 'cURL', value: 'cURL', },
    { label: 'Native Javascript', value: 'javascript', },
    { label: 'Node.js', value: 'nodejs', },
    { label: 'Python', value: 'python' }
  ]
}>
<TabItem value="cURL">

```shell
curl --location --request PUT "https://api.symbl.ai/v1/process/text/$CONVERSATION_ID" \
--header "Authorization: Bearer $AUTH_TOKEN" \
--header 'Content-Type: application/json' \
--data-raw '{
  "customEntities": [{"customType": "Hiring Process", "text": "internships"}],
  "detectPhrases": "True",
  "messages": [
    {
      "payload": {
        "content": "Hi Mike, Natalia here. Hope you don’t mind me reaching out. Who would be the best possible person to discuss internships and student recruitment at ABC Corp? Would you mind pointing me toward the right person and the best way to reach them? Thanks in advance for your help, I really appreciate it!"
      },
      "from": {
        "userId": "natalia@example.com",
        "name": "Natalia"
      },
      "duration":{
        "startTime":"2020-07-21T16:02:19.01Z",
        "endTime":"2020-07-21T16:04:19.99Z"
      }
    },
    {
      "payload": {
        "content": "Hey Natalia, thanks for reaching out. I am connecting you with Steve who handles recruitements for us."
      },
      "from": {
        "userId": "mike@abccorp.com",
        "name": "Mike"
      },
      "duration":{
        "startTime":"2020-07-21T16:04:19.99Z",
        "endTime":"2020-07-21T16:04:20.99Z"
      }
    }
  ]
}'
```
</TabItem>

<TabItem value="javascript">

```js
const authToken = AUTH_TOKEN;
const conversationId = CONVERSATION_ID;

const payload = {
  "customEntities": [{"customType": "Hiring Process", "text": "internships"}],
  "detectPhrases":true,
  "messages": [
    {
      "payload": {
        "content": "Hi Mike, Natalia here. Hope you don’t mind me reaching out. Who would be the best possible person to discuss internships and student recruitment at ABC Corp? Would you mind pointing me toward the right person and the best way to reach them? Thanks in advance for your help, I really appreciate it!"
      },
      "from": {
        "userId": "natalia@example.com",
        "name": "Natalia"
      },
      "duration":{
        "startTime":"2020-07-21T16:02:19.01Z",
        "endTime":"2020-07-21T16:04:19.99Z"
      }
    },
    {
      "payload": {
        "content": "Hey Natalia, thanks for reaching out. I am connecting you with Steve who handles recruitements for us."
      },
      "from": {
        "userId": "mike@abccorp.com",
        "name": "Mike"
      },
      "duration":{
        "startTime":"2020-07-21T16:04:19.99Z",
        "endTime":"2020-07-21T16:04:20.99Z"
      }
    }
  ],
  "confidenceThreshold": 0.5
}

const responses = {
  400: 'Bad Request! Please refer docs for correct input fields.',
  401: 'Unauthorized. Please generate a new access token.',
  404: 'The conversation and/or it\'s metadata you asked could not be found, please check the input provided',
  429: 'Maximum number of concurrent jobs reached. Please wait for some requests to complete.',
  500: 'Something went wrong! Please contact support@symbl.ai'
}

const fetchData = {
  method: "PUT",
  headers: {
    'Authorization': `Bearer ${authToken}`,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify(payload),
}

fetch(`https://api.symbl.ai/v1/process/text/${conversationId}`, fetchData).then(response => {
  if (response.ok) {
    return response.json();
  } else {
    throw new Error(responses[response.status]);
  }
}).then(response => {
  console.log('response', response);
}).catch(error => {
  console.error(error);
});
```

</TabItem>

<TabItem value="nodejs">

```js
const request = require('request');
const conversationId = CONVERSATION_ID;
const authToken = AUTH_TOKEN;

const options = {
  'method': 'PUT',
  'url': `https://api.symbl.ai/v1/process/text/${conversationId}`,
  'headers': {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${authToken}`
  },
  'body': JSON.stringify({
    "customEntities": [{"customType": "Hiring Process", "text": "internships"}],
    "detectPhrases":true,
    "messages": [
      {
        "payload": {
          "content": "Hi Mike, Natalia here. Hope you don’t mind me reaching out. Who would be the best possible person to discuss internships and student recruitment at ABC Corp? Would you mind pointing me toward the right person and the best way to reach them? Thanks in advance for your help, I really appreciate it!"
        },
        "from": {
          "userId": "natalia@example.com",
          "name": "Natalia"
        },
        "duration":{
          "startTime":"2020-07-21T16:02:19.01Z",
          "endTime":"2020-07-21T16:04:19.99Z"
        }
      },
      {
        "payload": {
          "content": "Hey Natalia, thanks for reaching out. I am connecting you with Steve who handles recruitements for us."
        },
        "from": {
          "userId": "mike@abccorp.com",
          "name": "Mike"
        },
        "duration":{
          "startTime":"2020-07-21T16:04:19.99Z",
          "endTime":"2020-07-21T16:04:20.99Z"
        }
      }
    ],
    "confidenceThreshold": 0.5
  })
};

request(options, function (error, response) {
  if (err) throw new Error(error);
  console.log(response.body);
});
```

</TabItem>
<TabItem value="python">

```py
import requests
import json

baseUrl = "https://api.symbl.ai/v1/process/text/"
conversationId = 'your_conversation_id'  # Generated using Submit text end point

url = baseUrl + conversationId

payload = {
    "name": "Business Meeting",  # <Optional,String| your_meeting_name by default conversationId>

    "confidenceThreshold": 0.6,  # <Optional,double| Minimum required confidence for the insight to be recognized. Value ranges between 0.0 to 1.0. Default value is 0.5.>

    "detectPhrases": True,  # <Optional,boolean| It shows Actionable Phrases in each sentence of conversation. These sentences can be found using the Conversation's Messages API. Default value is false.>
    "messages": [
        {
            "payload": {
                "content": "Hi Mike, Natalia here. Hope you don’t mind me reaching out. Who would be the best possible person to discuss internships and student recruitment at ABC Corp? Would you mind pointing me toward the right person and the best way to reach them? Thanks in advance for your help, I really appreciate it!"
            },
            "from": {
                "userId": "natalia@example.com",
                "name": "Natalia"
            },
            "duration": {
                "startTime": "2020-07-21T16:02:19.01Z",
                "endTime": "2020-07-21T16:04:19.99Z"
            }
        },
        {
            "payload": {
                "content": "Hey Natalia, thanks for reaching out. I am connecting you with Steve who handles recruitments for us."
            },
            "from": {
                "userId": "mike@abccorp.com",
                "name": "Mike"
            },
            "duration": {
                "startTime": "2020-07-21T16:04:19.99Z",
                "endTime": "2020-07-21T16:04:20.99Z"
            }
        }
    ]
}

# set your access token here. See https://docs.symbl.ai/docs/developer-tools/authentication
access_token = 'your_access_token'

headers = {
    'Authorization': 'Bearer ' + access_token,
    'Content-Type': 'application/json'
}

# webhookUrl = "https://yourdomain.com/jobs/callback",  #<Optional, string| your_webhook_url| Webhook url on which job updates to be sent. (This should be post API)>
# if webhookUrl is not None:
#   url += "?webhookUrl=" + webhookUrl

responses = {
    400: 'Bad Request! Please refer docs for correct input fields.',
    401: 'Unauthorized. Please generate a new access token.',
    404: 'conversation and/or it\'s metadata you asked could not be found, please check the input provided',
    429: 'Maximum number of concurrent jobs reached. Please wait for some requests to complete.',
    500: 'Something went wrong! Please contact support@symbl.ai'
}

response = requests.request("PUT", url, headers=headers, data=json.dumps(payload))

if response.status_code == 201:
    # Successful API execution
    print("conversationId => " + response.json()['conversationId'])  # ID to be used with Conversation API.
    print("jobId => " + response.json()['jobId'])  # ID to be used with Job API.
elif response.status_code in responses.keys():
    print(responses[response.status_code])  # Expected error occurred
else:
    print("Unexpected error occurred. Please contact support@symbl.ai" + ", Debug Message => " + str(response.text))

exit()
```

</TabItem>
</Tabs>


### Request Headers
Header Name | Required | Value
---------- | ------- | ------- |
```Authorization``` | Mandatory | `Bearer <token>` The token you get from our [authentication process](/docs/developer-tools/authentication).
`Content-Type` | Mandatory | `application/json`
```x-api-key``` | Optional | DEPRECATED. The JWT token you get from our [authentication process](/docs/developer-tools/authentication).

### Path Params

Parameter | Value
---------- | ------- |
`conversationId` | Conversation ID from the [POST Async Text API](/docs/async-api/overview/text/post-text) response.

### Request Body

Field | Required | Type |  Description
---------- | ------- | ------- |  ------- |
```name``` | Optional | string | Your meeting name. Default name set to `conversationId`.
```messages``` | Mandatory | list | Input Messages to look for insights. [See the messages section below for more details.](#messages)
```confidenceThreshold``` | Optional | double | Minimum required confidence for the insight to be recognized. Value ranges from  0.0 to 1.0. Default 0.5.
```detectPhrases```| Optional | boolean | It shows Actionable Phrases in each sentence of conversation. These sentences can be found using the Conversation's  Messages API. Default value is `false`.
```customEntities``` | Optional | *list* |  Input custom entities which can be detected in your conversation using [Entities API](/docs/conversation-api/entities).
```detectEntities``` | Optional | Boolean | Default value is `false`. If not set the [Entities API](/docs/conversation-api/entities) will not return any entities from the conversation.
```trackers``` <font color="orange"> BETA</font> | Optional | String | A list of key words and/or phrases to be tracked using the [Tracker API.](/docs/management-api/trackers/overview)
```enableAllTrackers```<font color="orange"> BETA </font> | Optional | Boolean | Default value is `false`. Setting this parameter to `true` will enable detection of all the Trackers maintained for your account by the Management API.This will allow Symbl to detect all the available Trackers in a specific Conversation. Learn about this parameter [here](/docs/management-api/trackers/overview#step-2-submit-files-using-async-api-with-enablealltrackers-flag).
```enableSummary```<font color="blue"> LABS </font> | Optional | Boolean | Setting this parameter to `true` allows you to generate Summaries using [Summary API (Labs)](/conversation-api/summary). Ensure that you use `https://api-labs.symbl.ai` as the base URL.


##### Code Example

```js
{
  "name": "Afternoon Business Meeting",
  "detectPhrases":true,
  "confidenceThreshold": 0.6,
  "customEntities": [{"entityName": "entityValue"}],
  "messages": []  // See messages section below.
  "trackers": [{
    "name": "COVID-19",
    "vocabulary": [
      "social distancing",
      "cover your face with mask",
      "vaccination"
    ]
  }]
}
```

#### messages

Field | Required | Type | Description
---------- | ------- | ------- |  -------
```payload``` | Mandatory | object | Input Messages to look for insights. [See the payload section below for more details.](#payload)
```from``` | Optional | object | Information about the User information produced the content of this message.
```duration``` | Optional | object | Duration object containing `startTime` and `endTime` for the transcript.

##### Code Example

```js
{ 
  "messages": [{
    "payload": {
      "content": "Hi Mike, Natalia here. Hope you don’t mind me reaching out. Who would be the best possible person to discuss internships and student recruitment at ABC Corp? Would you mind pointing me toward the right person and the best way to reach them? Thanks in advance for your help, I really appreciate it!"
    },
    "from": {
      "userId": "natalia@example.com",
      "name": "Natalia"
    },
    "duration":{
      "startTime":"2020-07-21T16:02:19.01Z",
      "endTime":"2020-07-21T16:04:19.99Z"
    }
  }, {
    "payload": {
      "content": "Hey Natalia, thanks for reaching out. I am connecting you with Steve who handles recruitements for us."
    },
    "from": {
      "userId": "mike@abccorp.com",
      "name": "Mike"
    },
    "duration":{
      "startTime":"2020-07-21T16:04:19.99Z",
      "endTime":"2020-07-21T16:04:20.99Z"
    }
  }]
}
```

#### payload

Field | Required | Type | Default | Description
---------- | ------- | ------- |  ------- | -------
```content``` | Mandatory | string | | The text content that you want the API to parse.


##### Code Example

```js
{
  "payload": {
    "content": "Hi Mike, Natalia here. Hope you don’t mind me reaching out. Who would be the best possible person to discuss internships and student recruitment at ABC Corp? Would you mind pointing me toward the right person and the best way to reach them? Thanks in advance for your help, I really appreciate it!"
  }
}
```

#### from(user)

Field | Required | Type | Description
---------- | ------- | ------- |  -------
```name``` | Optional | string | Name of the user.
```userId``` | Optional | string | A unique identifier of the user. E-mail ID is usually a preferred identifier for the user.

##### Code Example

```js
{
  "from": {
    "userId": "mike@abccorp.com",
    "name": "Mike"
  }
}
```

#### duration

Field | Required | Type | Description
---------- | ------- | ------- |  -------
```StartTime``` | Optional | DateTime | The start time for the particular text content.
```endTime``` | Optional | DateTime | The end time for the particular text content.

##### Code Example

```js
{
  "duration": {
    "startTime":"2020-07-21T16:04:19.99Z",
    "endTime":"2020-07-21T16:04:20.99Z"
  }
}
```

#### Query Params
Parameter | Required | Value
---------- | ------- | -------
```webhookUrl``` | Optional | Webhook url on which job updates to be sent. (This should be post API).

`webhookUrl` will be used to send the status of job created for uploaded audio. Every time the status of the job changes it will be notified on the `webhookUrl`.

#### Webhook Payload
Field | Description
---------- | ------- |
`jobId` | ID to be used with Job API
`status` | Current status of the job. (Valid statuses: [ `scheduled`, `in_progress`, `completed` ])

##### Code Example

```js
{
  "jobId": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d",
  "status": "in_progress"
}
```

### Response

#### Response object

Field | Description
---------- | ------- |
`conversationId` | ID to be used with [Conversation API](/docs/conversation-api/introduction).
`jobId` | ID to be used with Job API

##### Code Example

```js
{
  "conversationId": "5815170693595136",
  "jobId": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"
}
```

### API Limit Error

```js
{
  "statusCode" : 429,
  "message" : "This API has a limit of maximum of `X` number of concurrent jobs per account. If you are looking to scale, and need more concurrent jobs than this limit, please contact us at support@symbl.ai"
}
```

Here value of `X` can be found in [FAQ](/docs/faq). 
