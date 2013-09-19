---
title: Label | ElasticInbox API
---

# Label

ElasticInbox uses labels as a replacement for the classic mailbox folders. Each label has ID, name and custom attributes. Nested labels (like subfolders) are also supported.

Each label can have a set of custom attributes such as _color_, _order_, _icon_, etc.

Each account has a set of reserved labels which are created automatically when account initiated.

For more information please refer to [Labels and Markers](https://github.com/elasticinbox/elasticinbox/wiki/Labels-and-Markers) wiki page.

## Get all labels <a name="get"></a>

Returns all labels, their IDs, and optionally metadata (number of total and unread messages in the label).

    GET /rest/v2/:domain/:user/mailbox/

### Parameters

metadata
: _Optional_ **boolean** - Returns total and unread message count. Also returns total bytes for the `all` label which is effectively mailbox size. Default value is `false`.

### Response without metadata

<%= headers 200 %>
<%= json \
  :'0' => 'all',
  :'1' => 'inbox',
  :'2' => 'drafts',
  :'3' => 'sent',
  :'4' => 'trash',
  :'5' => 'spam',
  :'6' => 'starred',
  :'7' => 'important',
  :'8' => 'notifications',
  :'9' => 'attachments',
  :'10' => 'pop3',
  :'340' => 'MyLabel',
  :'1334' => 'MyLabel^NestedLabel'
%>

### Response with metadata

<%= headers 200 %>
<%= json \
  :'0' => {
    :name => 'all',
    :size => 2193085,
    :total => 230,
    :unread => 13
  },
  :'1' => {
    :name => 'inbox',
    :total => 190,
    :unread => 12
  },
  :'2' => {
    :name => 'drafts',
    :total => 3,
    :unread => 0
  },
  :'1232' => {
    :name => 'MyBlueLabel',
    :total => 8,
    :unread => 2,
    :attributes => {
      :order => '6',
      :color => 'blue'
    }
  },
  :'...' => '...'
%>

### Example request

<pre class="terminal">
% curl -XGET "http://host:8181/rest/v2/domain.tld/user/mailbox?metadata=true"
</pre>


## Add label <a name="add"></a>

Each account can have custom labels (similar to conventional folders). Unique label ID is automatically generated for each new label. Each account can have up to 10.000 labels. 

**Note:** Label names are case insensitive, but will preserve letter cases.

    POST /rest/v2/:domain/:user/mailbox/label

### Parameters

name
: _Required_ **string** - New label name. Should not contain `^` character.

### Input

Label name can also be specified in the request body. Custom attributes should be specified in the request body. 

**Note:** Request content type should be `application/json`

**Note:** `:` character is not allowed in attribute names

<%= json \
  :name => 'MyStarLabel',
  :attributes => {
    :icon => 'star',
    :color => 'yellow',
    :'...' => '...'
  }
%>

### Response

<%= headers 201, :Location =>
            'http://host:8181/rest/v2/domain.tld/user/mailbox/label/306' %>
<%= json :id => 306 %>

### Response if label name disallowed or reserved

<%= headers 400 %>
<%= json :message => 'Error message' %>

### Response if label with the given name already exists

<%= headers 409 %>
<%= json :message => 'Error message' %>

### Example request

<pre class="terminal">
% curl -XPOST \
  "http://host:8181/rest/v2/domain.tld/user/mailbox/label" \
   -d "{\"name\" : \"MyStarLabel\", \"attributes\" : { \"color\" : \"yellow\" } }" \
   -H "Content-Type: application/json"
</pre>


## Update label <a name="update"></a>

Rename label, update and remove custom attributes. To remove custom attribute set it to empty string or null.

    PUT /rest/v2/:domain/:user/mailbox/label/:id

### Parameters

name
: _Required_ **string** - New label name. May not contain `^` character.

### Input 

**Note:** Request content type should be `application/json`

**Note:** `:` character is not allowed in attribute names

<%= json \
  :name => 'RenameMyStar',
  :attributes => {
    :icon => 'yellow-star',
    :color => ''
  }
%>

### Response

<%= headers 204 %>

### Response if label with the given name already exists

<%= headers 409 %>
<%= json :message => 'Error message' %>

### Example request

<pre class="terminal">
% curl -XPUT \
  "http://host:8181/rest/v2/domain.tld/user/mailbox/label/306" \
   -d "{\"name\" : \"RenameMyStar\", \"attributes\" : { \"color\" : \"\" } }" \
   -H "Content-Type: application/json"
</pre>


## Delete label <a name="delete"></a>

This operation deletes label and removes label from all associated messages.

    DELETE /rest/v2/:domain/:user/mailbox/label/:id

### Response

<%= headers 204 %>

### Response if label is reserved

<%= headers 400 %>
<%= json :message => 'Error message' %>

### Example request

<pre class="terminal">
% curl -XDELETE "http://host:8181/rest/v2/domain.tld/user/mailbox/label/306"
</pre>
