---
title: "chatMessages: delta"
description: "Retrieve the list of messages (without the replies) in a channel of a team. By using delta query, you can get new or updated messages in a channel."
localization_priority: Priority
doc_type: apiPageType
author: "clearab"
ms.prod: "microsoft-teams"
---

# chatMessages: delta

[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]

Retrieve the list of [messages](../resources/chatmessage.md) (without the replies) in a [channel](../resources/channel.md) of a [team](../resources/team.md). By using delta query, you can get new or updated messages in a channel.

Delta query supports both full synchronization that retrieves all the messages in the specified channel, and incremental synchronization that retrieves those messages that have been added or changed in the channel since the last synchronization. Typically, you would do an initial full synchronization, and then get incremental changes to that calendar view periodically.

To get the replies for a message, use the [list message replies](channel-get-messagereply.md) or the [get message reply](channel-list-messagereplies.md) operation.

A GET request with the delta function returns either:

- A `nextLink` (that contains a URL with a **delta** function call and a `skipToken`), or
- A `deltaLink` (that contains a URL with a **delta** function call and `deltaToken`).

State tokens are completely opaque to the client. To proceed with a round of change tracking, simply copy and apply the `nextLink` or `deltaLink` URL returned from the last GET request to the next delta function call for that same calendar view. A `deltaLink` returned in a response signifies that the current round of change tracking is complete. You can save and use the `deltaLink` URL when you begin the next round.

For more information, see the [delta query](/graph/delta-query-overview.md) documentation.

## Permissions

One of the following permissions is required to call this API. To learn more, including how to choose permissions, see [Permissions](/graph/permissions-reference.md).

|Permission Type                        |Permissions (from least to most privileged)  |
|---------------------------------------|---------------------------------------------|
|Delegated (work or school account)     |Group.Read.All, Group.ReadWrite.All          |
|Delegated (personal Microsoft account) |Not Supported                                |
|Application                            |Group.Read.All, Group.ReadWrite.All          |

## HTTP request
<!-- { "blockType": "ignored" } -->
```http
GET /teams/{id}/channels/{id}/messages/delta
```

## Query parameters

Tracking changes in channel messages incurs a round of one or more **delta** function calls. If you use any query parameter (other than `$deltatoken` and `$skiptoken`), you must specify it in the initial **delta** request. Microsoft Graph automatically encodes any specified parameters into the token portion of the `nextLink` or `deltaLink` URL provided in the response.

You only need to specify any query parameters once upfront.

In subsequent requests, copy and apply the `nextLink` or `deltaLink` URL from the previous response, as that URL already includes the encoded parameters.

| Query parameter	   | Type	|Description|
|:---------------|:--------|:----------|
| `$deltatoken` | string | A [state token](/graph/delta-query-overview) returned in the `deltaLink` URL of the previous **delta** function call, indicating the completion of that round of change tracking. Save and apply the entire `deltaLink` URL including this token in the first request of the next round of change tracking for that collection.|
| `$skiptoken` | string | A [state token](/graph/delta-query-overview) returned in the `nextLink` URL of the previous **delta** function call, indicating that there are further changes to be tracked. |

### Optional OData query parameters

The following [OData query parameters](/graph/query-parameters) are supported by this API:
- `$top`, represents maximum number of messages to fetch in a call. The upper limit is **50**.
- `$skip`, represents how many messages to skip at the beginning of the list.
- `$filter` allows returning messages that meet a certain criteria. The only property that supports filtering is `lastModifiedDateTime`, and only the **gt** and **ge** operators are supported. For example, `../messages/delta?$filter=lastModifiedDateTime ge 2019-02-27T07:13:28.000z` will fetch any messages created or changed after the specified date time.

## Request headers
| Header        | Value                     |
|---------------|---------------------------|
| Authorization | Bearer {token}. Required. |
| Content-Type  | application/json          |

## Request Body

Do not supply a request body for this method.

## Response

If successful, this method returns a `200 OK` response code and a collection of [chatMessage](https://docs.microsoft.com/en-us/graph/api/resources/chatmessage?view=graph-rest-beta) objects in the response body. The response also includes a `nextLink` URL or a `deltaLink` URL.

## Examples

### Example 1: Initial synchronization

The following example shows a series of three requests to synchronize the messages in the given channel. There are five messages in the channel.

- Step 1: [sample initial request](#initial-request) and [response](#initial-request-response).
- Step 2: [sample second request](#second-request) and [response](#second-request-response)
- Step 3: [sample third request](#third-request) and [final response](#third-request-response).

For brevity, the sample responses show only a subset of the properties for an event. In an actual call, most event properties are returned.

See also what you'll do in the [next round](#example-2-retrieving-additional-changes).

#### Initial request

In this example, the channel messages are being synchronized for the first time, so the initial sync request does not include any state token. This round will return all the events in that calendar view.

The request specifies the optional request header, odata.top, returning 2 events at a time.

<!-- {
  "blockType": "request",
  "name": "get_channel_messages_delta"
}-->
```
GET /teams/{id}/channels/{id}/messages/delta?$top=2
```

#### Initial request response

The response includes two messages and a `@odata.nextLink` response header with a `skipToken`. The `nextLink` URL indicates there are more messages in the channel to get.

>**Note:** The response object shown here might be shortened for readability. All the properties will be returned from an actual call.
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.chatMessage",
  "isCollection": true
} -->
```
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "/$metadata#Collection(Microsoft.Teams.GraphSvc.chatMessage)",
    "@odata.nextLink": "/teams('id')/channels('id')/messages/delta?$skiptoken=c3RhcnRUaW1lPTE1NTEyMTUzMjU0NTkmcGFnZVNpemU9MjA%3d",
    "value": [
		{
			"id": "id-value",
			"replyToId": "id-value",
			"from" : {
				"user": { 
					"id": "id-value",
					"displayName": "John Doe"
				}  
			},
			"etag": "id-value",
			"messageType": "message",
			"createdDateTime": "2019-03-06T07:40:20.152Z",
			"lastModifiedDateTime": "2019-03-06T07:40:20.152Z",
			"body": {
				"content": "Hello World",
				"contentType": "Text"
			},
			"attachments": [],
			"mentions": [],
			"importance": "normal",
			"reactions": [],
			"locale": "en-us"
		},
		{
			"id": "id-value",
			"replyToId": "id-value",
			"from" : {
				"user": { 
					"id": "id-value",
					"displayName": "John Doe"
				}  
			},
			"etag": "id-value",
			"messageType": "message",
			"createdDateTime": "2019-03-06T08:40:20.152Z",
			"lastModifiedDateTime": "2019-03-06T08:40:20.152Z",
			"body": {
				"content": "Hello World",
				"contentType": "Text"
			},
			"attachments": [],
			"mentions": [],
			"importance": "normal",
			"reactions": [],
			"locale": "en-us"
		}
	]
}
```

#### Second request

The second request specifies the `nextLink` URL returned from the previous response. Notice that it no longer has to specify the same top parameters as in the initial request, as the `skipToken` in the `nextLink` URL encodes and includes them.

<!-- {
  "blockType": "request",
  "name": "get_channel_messages_delta"
}-->
```
GET /teams/{id}/channels/{id}/messages/delta?$skiptoken=c3RhcnRUaW1lPTE1NTEyMTUzMjU0NTkmcGFnZVNpemU9MjA%3d
```

#### Second request response

The second response returns the next 2 messages and a `@odata.nextLink` response header with a `skipToken`, indicates there are more messages in the channel to get.

>**Note:** The response object shown here might be shortened for readability. All the properties will be returned from an actual call.
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.chatMessage",
  "isCollection": true
} -->
```
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "/$metadata#Collection(Microsoft.Teams.GraphSvc.chatMessage)",
    "@odata.nextLink": "/teams('id')/channels('id')/messages/delta?$skiptoken=c3RhcnRUaW1lPTE1NTEyODcyMzY2NzgmcGFnZVNpemU9MjA%3d",
    "value": [
		{
			"id": "id-value",
			"replyToId": "id-value",
			"from" : {
				"user": { 
					"id": "id-value",
					"displayName": "John Doe"
				}  
			},
			"etag": "id-value",
			"messageType": "message",
			"createdDateTime": "2019-03-06T09:40:20.152Z",
			"lastModifiedDateTime": "2019-03-06T09:40:20.152Z",
			"body": {
				"content": "Hello World",
				"contentType": "Text"
			},
			"attachments": [],
			"mentions": [],
			"importance": "normal",
			"reactions": [],
			"locale": "en-us"
		},
		{
			"id": "id-value",
			"replyToId": "id-value",
			"from" : {
				"user": { 
					"id": "id-value",
					"displayName": "John Doe"
				}  
			},
			"etag": "id-value",
			"messageType": "message",
			"createdDateTime": "2019-03-06T09:50:20.152Z",
			"lastModifiedDateTime": "2019-03-06T09:50:20.152Z",
			"body": {
				"content": "Hello World",
				"contentType": "Text"
			},
			"attachments": [],
			"mentions": [],
			"importance": "normal",
			"reactions": [],
			"locale": "en-us"
		}
	]
}
```

#### Third request

The third request continues to use the latest `nextLink` returned from the last sync request.

<!-- {
  "blockType": "request",
  "name": "get_channel_messages_delta"
}-->
```
GET /teams/{id}/channels/{id}/messages/delta?$skiptoken=c3RhcnRUaW1lPTE1NTEyODcyMzY2NzgmcGFnZVNpemU9MjA%3d
```

#### Third request response

The third response returns the only remaining messages in the channel and a `@odata.deltaLink` response header with a `deltaToken` which indicates that all messages in the channel have been read. Save and use the `deltaLink` URL to query for any new messages starting from this point in the next round.

>**Note:** The response object shown here might be shortened for readability. All the properties will be returned from an actual call.
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.chatMessage",
  "isCollection": true
} -->
```
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "/$metadata#Collection(Microsoft.Teams.GraphSvc.chatMessage)",
    "@odata.deltaLink": "/teams('id')/channels('id')/messages/delta?$deltatoken=c3RhcnRUaW1lPTE1NTEyODc1ODA0OTAmcGFnZVNpemU9MjA%3d",
    "value": [
		{
			"id": "id-value",
			"replyToId": "id-value",
			"from" : {
				"user": { 
					"id": "id-value",
					"displayName": "John Doe"
				}  
			},
			"etag": "id-value",
			"messageType": "message",
			"createdDateTime": "2019-03-06T10:40:20.152Z",
			"lastModifiedDateTime": "2019-03-06T10:40:20.152Z",
			"body": {
				"content": "Hello World",
				"contentType": "Text"
			},
			"attachments": [],
			"mentions": [],
			"importance": "normal",
			"reactions": [],
			"locale": "en-us"
		}
	]
}
```

### Example 2: Retrieving additional changes

Using the `deltaLink` from the last request in the last round, you will be able to get only those messages that have changed (by being added, or updated) in that channel since then. Your request will look like the following, assuming you prefer to keep the same maximum page size in the response:

#### Request

<!-- {
  "blockType": "request",
  "name": "get_channel_messages_delta"
}-->
```
GET /teams/{id}/channels/{id}/messages/delta?$deltatoken=c3RhcnRUaW1lPTE1NTEyODc1ODA0OTAmcGFnZVNpemU9MjA%3d
```

#### Response

>**Note:** The response object shown here might be shortened for readability. All the properties will be returned from an actual call.
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.chatMessage",
  "isCollection": true
} -->
```
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "/$metadata#Collection(Microsoft.Teams.GraphSvc.chatMessage)",
    "@odata.deltaLink": "/teams('id')/channels('id')/messages/delta?$deltatoken=c3RhcnRUaW1l5Ti1NTEyODc1ODB0OTAyXGFdZVNpemU9MjA%3d",
    "value": [
		{
			"id": "id-value",
			"replyToId": "id-value",
			"from" : {
				"user": { 
					"id": "id-value",
					"displayName": "John Doe"
				}  
			},
			"etag": "id-value",
			"messageType": "message",
			"createdDateTime": "2019-03-06T10:40:20.152Z",
			"lastModifiedDateTime": "2019-03-06T10:40:20.152Z",
			"body": {
				"content": "Hello World",
				"contentType": "Text"
			},
			"attachments": [],
			"mentions": [],
			"importance": "normal",
			"reactions": [],
			"locale": "en-us"
		}
	]
}
```

<!-- uuid: 8fcb5dbc-d5aa-4681-8e31-b001d5168d79
2015-10-25 14:57:30 UTC -->
<!--
{
  "type": "#page.annotation",
  "description": "Channel messages: delta",
  "keywords": "",
  "section": "documentation",
  "tocPath": "",
  "suppressions": [
    ]
}
-->
