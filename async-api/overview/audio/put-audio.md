---
id: put-audio
title: PUT Audio API
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

The Async Audio API allows you to process an additional audio file to the previous conversation, append the transcription and get conversational insights for updated conversation.

It can be useful in any use case where you have access to multiple audio files of any type of conversation, and you want to extract the insightful items supported by the [Conversation API](/docs/conversation-api/introduction).


### HTTP REQUEST

`PUT https://api.symbl.ai/v1/process/audio/:conversationId`


### Example API Call

:::info
Before using the Async Audio API you must get the authentication token (`AUTH_TOKEN`) from [our authentication process](/docs/developer-tools/authentication).
:::

<Tabs
  defaultValue="cURL"
  values={[
    { label: 'cURL', value: 'cURL', },
    { label: 'Node.js', value: 'nodejs', },
    { label: 'Python', value: 'python' }
  ]
}>
<TabItem value="cURL">

```shell
# Wave file
curl --location --request PUT "https://api.symbl.ai/v1/process/audio/$CONVERSATION_ID?name=Business%20Meeting&confidenceThreshold=0.6" \
--header 'Content-Type: audio/wav' \
--header "Authorization: Bearer $AUTH_TOKEN" \
--data-binary '@/Users/adamvoliva/Downloads/sample_audio_file.wav'

# MP3 File
curl --location --request PUT "https://api.symbl.ai/v1/process/audio/$CONVERSATION_ID?name=Business%20Meeting&confidenceThreshold=0.6" \
--header 'Content-Type: audio/mpeg' \
--header "Authorization: Bearer $AUTH_TOKEN" \
--data-binary '@/file/location/audio.mp3'
```
</TabItem>

<TabItem value="nodejs">

```js
const request = require('request');
const fs = require('fs');

const accessToken = AUTH_TOKEN;
const audioFileStream = fs.createReadStream('/file/location/audio.wav');

const params = {
  'name': "BusinessMeeting",
  // <Optional, string| your_conversation_name | Your meeting name. Default name set to conversationId.>

  // 'webhookUrl': "https://yourdomain.com/jobs/callback",
  // <Optional, string| your_webhook_url| Webhook url on which job updates to be sent. (This should be post API)>

  // 'customVocabulary': ['Platform', 'Discussion', 'Targets'],
  // <Optional, list| custom_vocabulary_list> |Contains a list of words and phrases that provide hints to the speech recognition task.

  'confidenceThreshold': 0.6,
  // <Optional, double| confidence_threshold | Minimum required confidence for the insight to be recognized.>

  // 'detectPhrases': True,
  // <Optional, boolean| detect_phrases> |Accepted values are true & false. It shows Actionable Phrases in each sentence of conversation. These sentences can be found in the Conversation's Messages API.

  // 'enableSeparateRecognitionPerChannel': True,
  // "<Optional, boolean| enable_separate_recognition_per_channel> |Enables Speaker Separated Channel audio processing. Accepts true or false.

  // 'channelMetadata': [{"channel": 1, "speaker": {"name": "Robert Bartheon", "email": "robertbartheon@gmail.com"}}],
  // ["<Optional, boolean| channel_metadata> |This object parameter contains two variables speaker and channel to specific which speaker corresponds to which channel. This object only works when enableSeparateRecognitionPerChannel query param is set to true."

  // 'languageCode': "en-US"
  // <Optional, boolean| language_code> |code of language of recording.
}

const audioOption = {
  url: 'https://api.symbl.ai/v1/process/audio' + your_conversationId,
  headers: {
    'Authorization': `Bearer ${authToken}`,
    'Content-Type': 'audio/wav'
  },
  qs: params,
  json: true,
};

const responses = {
  400: 'Bad Request! Please refer docs for correct input fields.',
  401: 'Unauthorized. Please generate a new access token.',
  404: 'The conversation and/or it\'s metadata you asked could not be found, please check the input provided',
  429: 'Maximum number of concurrent jobs reached. Please wait for some requests to complete.',
  500: 'Something went wrong! Please contact support@symbl.ai'
}

audioFileStream.pipe(request.put(audioOption, (err, response, body) => {
  const statusCode = response.statusCode;
  if (err || Object.keys(responses).indexOf(statusCode.toString()) !== -1) {
    throw new Error(responses[statusCode]);
  }
  console.log('Status code: ', statusCode);
  console.log('Body', response.body);
}));
```

</TabItem>
<TabItem value="python">

```py
import requests
import json

baseUrl = "https://api.symbl.ai/v1/process/audio/"
conversationId = 'your_conversation_id'  # Generated using Submit text end point

url = baseUrl + conversationId

payload = None
numberOfBytes = 0

try:
    audio_file = open('destination/of/file/file.wav', 'rb')  # use (r"path/to/file") when using windows path
    payload = audio_file.read()
    numberOfBytes = len(payload)
except FileNotFoundError:
    print("Could not read the file provided.")
    exit()

# set your access token here. See https://docs.symbl.ai/docs/developer-tools/authentication
access_token = 'your_access_token'

headers = {
    'Authorization': 'Bearer ' + access_token,
    'Content-Length': str(numberOfBytes),  # This should correctly indicate the length of the request body in bytes.
    'Content-Type': 'audio/wave'
    # This is OPTIONAL field which describes the format and codec of the provided audio data. Accepted values are audio/wav, audio/mpeg, audio/mp3 and audio/wave only. If your audio is in a format other than these, do not use this field.
}

params = {
      'name': "BusinessMeeting",
      # <Optional, string| your_conversation_name | Your meeting name. Default name set to conversationId.>

      # 'webhookUrl': "https://yourdomain.com/jobs/callback",
      # <Optional, string| your_webhook_url| Webhook url on which job updates to be sent. (This should be post API)>

      # 'customVocabulary': ['Platform', 'Discussion', 'Targets'],
      # <Optional, list| custom_vocabulary_list> |Contains a list of words and phrases that provide hints to the speech recognition task.

      'confidenceThreshold': 0.6,
      # <Optional, double| confidence_threshold | Minimum required confidence for the insight to be recognized.>

      # 'detectPhrases': True,
      # <Optional, boolean| detect_phrases> |Accepted values are true & false. It shows Actionable Phrases in each sentence of conversation. These sentences can be found in the Conversation's Messages API.

      # 'enableSeparateRecognitionPerChannel': True,
      # "<Optional, boolean| enable_separate_recognition_per_channel> |Enables Speaker Separated Channel audio processing. Accepts true or false.

      # 'channelMetadata': [{"channel": 1, "speaker": {"name": "Robert Bartheon", "email": "robertbartheon@gmail.com"}}],
      # ["<Optional, boolean| channel_metadata> |This object parameter contains two variables speaker and channel to specific which speaker corresponds to which channel. This object only works when enableSeparateRecognitionPerChannel query param is set to true."

      # 'languageCode': "en-US"
      # <Optional, boolean| language_code> |code of language of recording.
}

responses = {
    400: 'Bad Request! Please refer docs for correct input fields.',
    401: 'Unauthorized. Please generate a new access token.',
    404: 'The conversation and/or it\'s metadata you asked could not be found, please check the input provided',
    429: 'Maximum number of concurrent jobs reached. Please wait for some requests to complete.',
    500: 'Something went wrong! Please contact support@symbl.ai'
}

response = requests.request("PUT", url, headers=headers, data=payload, params=params)

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


:::info
Content-Type: This field is optional.

If you're not sure about audio format, you can omit it since the API will automatically detect the content type. But when it's mentioned, audio format is validated.
:::

Header Name  | Required | Value
---------- | ------- |  ------- |
```Authorization``` | Mandatory | `Bearer <token>` The token you get from our [authentication process](/docs/developer-tools/authentication).
```Content-Type``` | Optional | Describes the format and codec of the provided audio data. Accepted values are `audio/wav`, `audio/mpeg`, `audio/mp3` and `audio/wave`.
```x-api-key``` | Optional | DEPRECATED. The JWT token you get from our [authentication process](/docs/developer-tools/authentication).

### Path Params

Parameter  | Description
---------- | -------
`conversationId` | `conversationId` which is provided by the first request submitted using POST async audio API

### Query Params

Parameters | Required | Type | Description
---------- | ------- | ------- | ------
```name``` | Optional | String | Your meeting name. Default name set to `conversationId`.
```webhookUrl``` | Optional | String | Webhook url on which job updates to be sent. This should be post API.
 ```customVocabulary``` | Optional | String[] | Contains a list of words and phrases that provide hints to the speech recognition task.
```confidenceThreshold``` | Optional | Double | Minimum required confidence for the insight to be recognized. The range is from 0.0 to 1.0. Default value 0.5.
 ```detectPhrases```| Optional | Boolean | Accepted values are `true` & `false`. It shows Actionable Phrases in each sentence of conversation. These sentences can be found in the Conversation's Messages API.
 ```customEntities``` | Optional | Object[] | Input custom entities which can be detected in your conversation using [Entities API](/docs/conversation-api/entities).
 ```detectEntities``` | Optional | Boolean | Default value is `false`. If not set the [Entities API](/docs/conversation-api/entities) will not return any entities from the conversation.
 ```enableSeparateRecognitionPerChannel``` | Optional | Boolean | Enables Speaker Separated Channel audio processing. Accepts `true` or `false`.
 ```channelMetadata``` | Optional | Object[] | This object parameter contains two variables `speaker` and `channel` to specific which speaker corresponds to which channel. This object **only** works when `enableSeparateRecognitionPerChannel` query param is set to `true`.
  ```languageCode```| Optional | String | We accept different languages. Please [check language Code](/docs/async-api/overview/async-api-supported-languages) as per your requirement.
  ``` mode``` | Optional  | String | Accepts `phone` or `default`. `phone` mode is best for audio that is generated from phone call(which is typically recorded at 8khz sampling rate).<br />`default` mode works best for audio generated from video or online meetings(which is typically recorded at 16khz or more sampling rate).<br />When you don't pass this parameter `default` is selected automatically.
  ```trackers```<font color="orange"> BETA</font> | Optional | String | A list of key words and/or phrases to be tracked using the [Tracker API.](/docs/management-api/trackers/overview)
  ```enableAllTrackers```<font color="orange"> BETA </font> | Optional | Boolean | Default value is `false`. Setting this parameter to `true` will enable detection of all the Trackers maintained for your account by the Management API.This will allow Symbl to detect all the available Trackers in a specific Conversation. Learn about this parameter [here](/docs/management-api/trackers/overview#step-2-submit-files-using-async-api-with-enablealltrackers-flag).
 ```enableSummary```<font color="blue"> LABS </font> | Optional | Boolean | Setting this parameter to `true` allows you to generate Summaries using [Summary API (Labs)](/conversation-api/summary). Ensure that you use `https://api-labs.symbl.ai` as the base URL.


### Speaker Separation

The Async Audio & Async Video APIs can detect and separate unique speakers in a single stream of audio & video without the need for separate speaker events.

To enable this capability with either of the APIs the `enableSpeakerDiarization` and `diarizationSpeakerCount` query parameters need to be passed with the request.

The `diarizationSpeakerCount` should be equal to the number of unique speakers in the conversation. If the number varies then this might introduce false positives in the diarized results.

If you’re looking for similar capability in Real-Time APIs, please refer to [Active Speaker Events](/docs/javascript-sdk/code-snippets/active-speaker-events) and Speaker Separation in WebSocket API sections.

#### Query Params

Parameter | Required | Value
--------- | --------- | -------
```enableSpeakerDiarization``` | Mandatory | Whether the diarization should be enabled for this conversation. Pass this as `true` to enable this capability.
```diarizationSpeakerCount``` | Mandatory | The number of unique speakers in this conversation.

### Channel Metadata

`channelMetadata` object has following members:

Field | Required | Type | Description
| ------- | ------- | ------- | --------
```channel``` | Mandatory | Integer | This denotes the channel number in the audio file. Each channel will contain independent speaker's voice data.
```speaker``` | Mandatory | String | This is the wrapper object which defines the speaker for this channel.

`speaker`  has the following members:

Field | Required | Type | Description
| ------- | ------- | ------- | ------
```name``` | Optional | String | Name of the speaker.
```email``` | Optional | String | Email address of the speaker.


##### channelMetadata Object

```js
{
  "channelMetadata": [
    {
      "channel": 1,
      "speaker": {
        "name": "Robert Bartheon",
        "email": "robertbartheon@gmail.com"
      }
    },
    {
      "channel": 2,
      "speaker": {
        "name": "Arya Stark",
        "email": "aryastark@gmail.com"
      }
    }
  ]
}
```

:::caution
Billing for a speaker separated channel audio file happens according to the number of channels present in the audio files. The duration for billing will be calculated according to the below formula:

`totalDuration = duration_of_the_audio_file * total_number_of_channels`

So if you send a 120-second file with 3 speaker separated channels, the total duration for billing would be 360 seconds or 6 minutes.
:::

### Webhook Payload

`webhookUrl` will be used to send the status of job created for uploaded audio url. Every time the status of the job changes it will be notified on the WebhookUrl

Field | Description
| ------- | -------
```jobId``` | ID to be used with Job API.
```status``` |  Current status of the job. (Valid statuses: [ `scheduled`, `in_progress`, `completed`, `failed` ])


##### Code Example

```js
{
  "jobId": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d",
  "status": "in_progress"
}
```

### Response


#### Response Object

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

