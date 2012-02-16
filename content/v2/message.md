---
title: Message | ElasticInbox API
---

# Message

Each message is identified by UUID - a unique ID based on the message arrival time (Type1 UUID). The most frequently used message headers and optionally pre-parsed message body (text and/or html) are stored in the message metadata.

## List messages <a name="list"></a>

This call will return list of the messages for the given label.

**Note:** Messages are always ordered by time.

    GET /rest/v2/:domain/:user/mailbox/label/:label_id

### Parameters

metadata
: _Optional_ **boolean** - When set to true, message metadata (excl. pre-parsed body) are included in the result. By default only message IDs are returned (`false`).

count
: _Optional_ **integer** - Number of returned messages. By default call returns only 50 messages. Maximum is 500.

start
: _Optional_ **uuid** - UUID of the first message to start listing from. Usually used for pagination or getting messages older/newer than certain date.

reverse
: _Optional_ **boolean** - Defines order of messages. By default messages fetched in reverse order (`true`) in timeline, i.e. newest first. Set to `false` to list messages in ascending time order (i.e. oldest first).

### Response without metadata

<%= headers 200 %>
<%= json \
  [
  'e5595060-f81d-11df-bc91-080027267700',
  'e5590240-f81d-11df-a533-080027267700',
  'e5586600-f81d-11df-8cc2-080027267700',
  '...'
  ]
%>

### Response with metadata

<%= headers 200 %>
<%= json \
  :"e5572d80-f81d-11df-bc91-080027267700" => {
        :labels => [0, 1],
        :size => 1477,
        :date => "2010-11-24T22:55:12.000+0000",
        :subject => "My message subject",
        :uri => 'blob://aws-b1/user@domain.tld:e5572d80-f81d-11df-bc91-080027267700',
        :markers => ["SEEN", "REPLIED"],
        :from => [{
                :address => "john@domain.tld",
                :name => "John Doe"
            }],
        :to => [{
                :address => "user@elasticinbox.com",
                :name => "ElasticInbox User"
            }],
        :cc => [{
                :address => "another@elasticinbox.com",
                :name => "Another User"
            }]
    },
    :"10b88ed0-f5c4-11df-8691-080027267700" => {
        :labels => [0, 1, 3],
        :size => 149490,
        :date => "2010-11-21T23:07:08.000+0000",
        :subject => "FW: Attachments",
        :uri => 'blob://aws-b1/user@domain.tld:10b88ed0-f5c4-11df-8691-080027267700',
        :from => [{
                :address => "rustam@elasticinbox.com",
                :name => "Rustam Aliyev"
            }],
        :to => [{
                :address => "user@elasticinbox.com",
                :name => ""
            }],
        :parts => {
            :'3' => {
                :size => 64736,
                :fileName => "elasticinbox.png",
                :mimeType => "image/png"
            },
            :'2' => {
                :size => 27136,
                :fileName => "Letter.doc",
                :mimeType => "application/msword"
            }
        }
    }
%>

## Get parsed message <a name="get"></a>

Returns message labels, markers, important headers, attachments and body. Message body will be returned in `textBody` and `htmlBody` for the relevant mime types.

**Note:** Pre-parsed message body stored in metadata only if enabled in config.

    GET /rest/v2/:domain/:user/mailbox/message/:uuid

### Parameters

adjacent
: _Optional_ **boolean** - Will fetch previous and next messages by date in the given label. This parameter requires `label` parameter. Note that it will not check if message actually belongs to the given label.

label
: _Optional_ **integer** - Requred by `adjacent` parameter for fetching previous and next IDs.

markseen
: _Optional_ **boolean** - Will mark message as SEEN if `true`. Often message has to be marked as SEEN immediately after reading.

### Response with adjacent messages

<%= headers 200 %>
<%= json \
  :message => {
    :labels => [0, 1],
    :size => 1123,
    :date => "2010-11-24T22:55:12.000+0000",
    :subject => "Hello API!",
    :uri => 'blob://fs-local/user@domain.tld:e5590240-f81d-11df-a533-080027267700',
    :from => [{
      :address =>"test@elasticinbox.com",
      :name => "John Doe" }],
    :to => [{
      :address => "user@elasticinbox.com",
      :name => "ElasticInbox User" }],
    :htmlBody => 'This is sample <u>html</u> message body.<br><br>-- EI<br>'
  },
  :prev => "e550ebf0-f81d-11df-bc91-080027267700",
  :next => "e54d4270-f81d-11df-8cc2-080027267700"
%>

## Get raw message <a name="raw"></a>

Fetches original message source.

    GET /rest/v2/:domain/:user/mailbox/message/:uuid/raw

### Response

<%= headers 200,
    :'Content-Type' => 'text/plain',
    :'Transfer-Encoding' => 'chunked' %>
<pre>
Received: from mx02 (mx02 [127.0.0.1])
        by mx.google.com with ESMTP id tn5si19562066bkb.45.2011.12.05.09.53.49;
        Mon, 05 Dec 2011 09:53:50 -0800 (PST)
Received: by mx02 (Postfix, from userid 48)
	id AB80628CA95; Mon,  5 Dec 2011 21:53:48 +0400 (AZT)
Date: Mon, 5 Dec 2011 21:53:48 +0400
To: =?utf-8?B?w4dhdGTEsXLEsWxtYWzEsSDDnG52YW4=?= <test@elasticinbox.com>
From: =?utf-8?B?UsO8c3TJmW0gxo9saXlldg==?= <rustam@elasticinbox.com>
Subject: =?utf-8?B?VVRGOCBUZXN0IMO8w6fDvG4gbcO2dnp1IGZpa2lsyZnFn23JmW1pxZ/JmW0=?=
Message-ID: <3917ea5a412bc64e0c1f04d81ac2e5c6@elasticinbox.com>
MIME-Version: 1.0
Content-Type: multipart/alternative;
	boundary="b1_3917ea5a412bc64e0c1f04d81ac2e5c6"
...
</pre>

## Get raw message URI <a name="uri"></a>

Fetches BLOB URI. This is useful if you don't want ElasticInbox to act as a proxy and fetch message contents from the object store directly.

    GET /rest/v2/:domain/:user/mailbox/message/:uuid/url

### Response

<%= headers 307,
    :Location => 'blob://aws-b1/user@domain.tld:753eef70-d5fb-14ce-abd4-040cced3bd7a'
%>

## Get message part by Part ID <a name="partid"></a>

This method allows fetching mime part by it's part number.

Each mime part (header, body, attachment) identified by their part number. The part number is a string of integers delimited by period which index into a body part list as per the IMAP4 specification (e.g. `2` or `3.1`).

    GET /rest/v2/:domain/:user/mailbox/message/:uuid/:partid

### Response

<%= headers 200,
    :'Content-Type' => 'application/msword',
    :'Content-Disposition' => 'attachment; filename="Letter.doc"',
    :'Transfer-Encoding' => 'chunked' %>
<pre>binary content follows</pre>

## Get message part by Content ID <a name="contentid"></a>

MIME parts can also be fetched by Content-ID which is usually used for inline attachments. Content-ID string in request MUST be enclosed within angle brackets `<...>` (similar to RFC2392).

    GET /rest/v2/:domain/:user/mailbox/message/:uuid/<:contentid>

### Response

<%= headers 200,
    :'Content-Type' => 'application/msword',
    :'Content-Disposition' => 'attachment; filename="Letter.doc"',
    :'Transfer-Encoding' => 'chunked' %>
<pre>binary content follows</pre>

## Store message <a name="store"></a>

Note: If no label specified, message will be stored only with label `0` (all_mails).

    POST /rest/v2/:domain/:user/mailbox/message

### Parameters

label
: _Optional_ _Multiple_ **integer** - These labels will be automatically applied to the message. Specify multiple times to apply multiple labels.

marker
: _Optional_ _Multiple_ **string** - These markers will be automatically applied to the message. Specify multiple times to apply multiple markers.

### Response

Returns URL for new message as well as javascript with message id.

<%= headers 201,
    :Location => 'http://localhost:8080/rest/v2/domain.tld/user/mailbox/message/e5031a10-f81d-11df-a88e-080027267700'
%>
<%= json \
  :id => 'e5031a10-f81d-11df-a88e-080027267700'
%>

## Update message <a name="update"></a>

This operation is similar to the *store message* but it is handy for updating already existing message. Update process is performed in tree steps:

 1. Store content as new message.
 1. Copy labels and markers from updated message to new message.
 1. Delete updated message.

As a result all information, other than labels and markers, will be replaced with the new content (and removed if does not exist in the new version).

**Note:** Updated message will have new ID.

    POST /rest/v2/:domain/:user/mailbox/message/:uuid

### Response

Returns URL for new message as well as javascript with message id.

<%= headers 201,
    :Location => 'http://localhost:8080/rest/v2/domain.tld/user/mailbox/message/9c0063e0-b131-11e0-a275-080027a49b23'
%>
<%= json \
  :id => '9c0063e0-b131-11e0-a275-080027a49b23'
%>

## Modify labels and markers <a name="modify"></a>

This method allows adding and removing of labels and markers.

**Note:** Label `all_mails` added automatically and can't be removed.

	PUT /rest/v2/:domain/:user/mailbox/message/:uuid

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

### Response

<%= headers 204 %>

## Delete message <a name="delete"></a>

**Note:** This operation only removes message from all indexes (including `all_mails`) and adds to the special PURGE index. It is still possible to accesses message directly by ID after deletion. To completely delete message from metadata and blob stores purge command has to be issued.

	DELETE /rest/v2/:domain/:user/mailbox/message/:uuid

### Response

<%= headers 204 %>
