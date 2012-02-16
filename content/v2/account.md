---
title: Account | ElasticInbox API
---

# Account

A new account should be initiated for each mailbox. Each mailbox, label and message belong to a specific account. It is possible to store accounts for multiple domains on a single ElasticInbox instance.

## Add account <a name="add"></a>

    POST /rest/v2/:domain/:user

### Response

<%= headers 201, :Location =>
            'http://localhost:8080/rest/v2/domain.tld/user/mailbox' %>
<%= json :ok => true %>

## Get account information <a name="get"></a>

**Note:** This method has not been implemented yet.

    GET /rest/v2/:domain/:user

### Response

<%= headers 200 %>
<%= json :attr => "val" %>

## Delete account <a name="delete"></a>

Deletes all account data including messages from BLOB store and metadata.

    DELETE /rest/v2/:domain/:user

### Response

<%= headers 204 %>

