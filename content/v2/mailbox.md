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

### Example request

<pre class="terminal">
% curl -XPUT "http://host:8181/rest/v2/domain.tld/user/mailbox/purge?age=2011%2F04%2F16"
</pre>


## Restore <a name="restore"></a>

Restore deleted messages.

**Note:** This method has not been implemented yet.

    PUT /rest/v2/:domain/:user/mailbox/restore

### Parameters

age
: _Optional_ **date** - Purge messages only older than the given age. If not specified, all messages in the queue will be purged. Date can be in any format in the current locale which is understood by the `java.text.DateFormat.parse(String)`, for instance `"2011/04/15"` or `"2011/05/01 14:30"`.

### Response

<%= headers 204 %>


## Scrub counters <a name="scrub_counters"></a>

Mailbox size, number of total and new messages rely on counters implemented in the storage layer. With Cassandra (currently the only supported storage system) there are a number of scenarios when counters may start deviating from the real data.

Use this method to fix corrupted counters. It will read metadata for all messages in the mailbox, recalculate and update counters.

    POST /rest/v2/:domain/:user/mailbox/scrub/counters

### Response

<%= headers 204 %>

### Example request

<pre class="terminal">
% curl -XPOST "http://host:8181/rest/v2/domain.tld/user/mailbox/scrub/counters"
</pre>


## Scrub indexes <a name="scrub_indexes"></a>

In case when label indexes became corrupted, it is possible to rebuild them from message metadata. This command will also recalculate and update counters similar to <a name="scrub_counters">scrub counters</a> method. 

    POST /rest/v2/:domain/:user/mailbox/scrub/indexes

### Response

<%= headers 204 %>

### Example request

<pre class="terminal">
% curl -XPOST "http://host:8181/rest/v2/domain.tld/user/mailbox/scrub/indexes"
</pre>
