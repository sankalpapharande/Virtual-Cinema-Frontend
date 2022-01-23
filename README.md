# Frontend of Cloud Computing and Big Data Project

Developed a web application with scalable AWS cloud services & socket to provide an interactive video watching experience

# Use App
## Create the virtual environment:
`python3 -m venv ~/.vcinema`

## Activate the virtual env:

`. ~/.vcinema/bin/activate`

## Run the server 

`node server/app.js`

## Run the clients

In other terminal window, run for each client:

`yarn install`

`yarn start`

# Implementation

## Messaging

- frontend: socket.io
- backend: ASIS in Architecture Diagram

- Concerns:
  - Whatsapp approach: store message in local storage
  - Telegram approach: store message in server

```json
{
    messageID: UUIDV4,
    timestamp: ISOTIME,
    userID: UUIDV4,
    user: {
        name: STRING,
    },
    groupID: UUIDV4,
    message: String,
    type: image | text | emoji,
}
```

## Events

1. Event Detail (filter by `eventID`)
```json
{
    eventID: UUIDV4,
    user: [{
        userName: STRING,
        startTime: ISOTIME,
        endTime: ISOTIME
    }],
    videoURL: STRING_URL,
    startEventTime: ISOTIME,
    endEventTime: ISOTIME,
    eventInvitationLink: STRING_URL
}
```
2. Event List (filter by `userID`)
```json
[
    {
    eventID: UUIDV4,
    user: [{
        userName: STRING,
        startTime: ISOTIME,
        endTime: ISOTIME
    }],
    videoURL: STRING_URL,
    startEventTime: ISOTIME,
    endEventTime: ISOTIME,
    eventInvitationLink: STRING_URL
},{
    eventID: UUIDV4,
    user: [{
        userName: STRING,
        startTime: ISOTIME,
        endTime: ISOTIME
    }],
    videoURL: STRING_URL,
    startEventTime: ISOTIME,
    endEventTime: ISOTIME,
    eventInvitationLink: STRING_URL
}
]

```

`https://S3_FRONTEND_URL/watchVideo?eventID={eventID}`

### Invitation Logic:
1. User could invite friends via 1) email or by 2) copy and sharing link
2. Friends join by clicking link
3. Frontend will:
    - redirect users to the Watch Video page
    - pass along the invitation link
    - frontend parses the invitation link to extract eventID
    - frontend calls backend for connection details (i.e. event object)

```python
if len(events['user']) > 4:
    return Exception()
else:
    events['user'].append(User(username='hojae', startTime=time, endTime=time))
````

## History

Get Event List based on participant information (`userID`)

View Log >
`https://S3_FRONTEND_URL/history?eventID={eventID}`

1. Event Detail - Event API
2. History of Messages

```json
{
    eventID: UUIDV4,
    timeStart: ISOTIME,
    timeEnd: ISOTIME
}
```

```json
{
    eventID: UUIDV4,
    videoURL: 
}
```

## Authentication
Token Based:
Header > Authorization > Bearer {jwtToken}

TODO: find out how JWT authentication works in AWS Cognito

```python
import boto3
def get_user_info(event):
    jwt_token = event['headers']['jwtToken']
    cognito_client = boto3.client("cognito-idp", region_name="us-east-1")
    response = cognito_client.get_user(AccessToken=jwt_token)
    user_id = response['UserAttributes'][0]['Value']
    name = response['UserAttributes'][2]['Value']
    email = response['UserAttributes'][3]['Value']
    return user_id, name, email
```

