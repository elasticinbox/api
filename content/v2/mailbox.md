---
title: Mailbox | ElasticInbox API
---

# Mailbox

Each mailbox contains labels and messages.

## Purge mailbox <a name="purge"></a>

Purges all deleted messages in the account older than the given date.

    PUT /rest/v2/:domain/:user/mailbox/purge

### Parameters

age
: _Optional_ **date** - Purge messages only older than the given age. If not specified, all messages in the queue will be purged. Date can be in any format in the current locale which is understood by the `java.text.DateFormat.parse(String)`, for instance `"2011/04/15"` or `"2011/05/01 14:30"`.

### Response

<%= headers 204 %>

## Restore <a name="restore"></a>

Restore deleted messages.

**Note:** This method has not been implemented yet.

    PUT /rest/v2/:domain/:user/mailbox/restore

### Parameters

age
: _Optional_ **date** - Purge messages only older than the given age. If not specified, all messages in the queue will be purged. Date can be in any format in the current locale which is understood by the `java.text.DateFormat.parse(String)`, for instance `"2011/04/15"` or `"2011/05/01 14:30"`.

### Response

<%= headers 204 %>
