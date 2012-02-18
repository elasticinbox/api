---
title: Batch Operations | ElasticInbox API
---

# Batch Operations

## Modify messages  <a name="modify"></a>

Modifies labels and markers for multiple messages.

    PUT /rest/v2/:domain/:user/mailbox/message

### Parameters

addlabel
: _Optional_ _Multiple_ **integer** - Add label to the message.

removelabel
: _Optional_ _Multiple_ **integer** - Remove label from the message.

addmarker
: _Optional_ _Multiple_ **string** - Add marker to the message.

removemarker
: _Optional_ _Multiple_ **string** - Add marker to the message.

Each of the parameters can be used multiple times in order to add/remove multiple labels/markers.

### Input

JSON array of message IDs.

**Note:** Request content type should be `application/json`

<%= json \
  ["e5031a10-f81d-11df-a88e-080027267700", "e54f6550-f81d-11df-a9d1-080027267700", '...']
%>

### Response

<%= headers 204 %>

### Example request

<pre class="terminal">
$ curl -XPUT \
  "http://host:8181/rest/v2/domain.tld/user/mailbox/message?addlabel=3&addlabel=540&removemarker=seen" \
   -d "[\"e5031a10-f81d-11df-a88e-080027267700\", \"e54f6550-f81d-11df-a9d1-080027267700\"]" \
   -H "Content-Type: application/json"
</pre>

## Delete messages <a name="delete"></a>

Delete multiple messages.

    DELETE /rest/v2/:domain/:user/mailbox/message

### Input

JSON array of message IDs.

**Note:** Request content type should be `application/json`

<%= json \
  ["e5031a10-f81d-11df-a88e-080027267700", "e54f6550-f81d-11df-a9d1-080027267700", '...']
%>

### Response

<%= headers 204 %>

### Example request

<pre class="terminal">
% curl -XDELETE "http://host:8181/rest/v2/domain.tld/user/mailbox/message" \
  -d "[\"e5031a10-f81d-11df-a88e-080027267700\", \"e54f6550-f81d-11df-a9d1-080027267700\"]" \
  -H "Content-Type: application/json"
</pre>