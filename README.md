# ![LOGO](logo.png) BulkSMS JSON **flow**ground Connector

## Description

A generated **flow**ground connector for the BulkSMS JSON API (version 1.0.0).

Generated from: https://api.apis.guru/v2/specs/bulksms.com/1.0.0/swagger.json<br/>
Generated at: 2019-05-07T17:39:50+03:00

## API Description

## Overview

The JSON REST API allows you to submit and receive [BulkSMS](https://www.bulksms.com/) messages. You can also get access to past messages and see your account profile.

The base URL to use for this service is `https://api.bulksms.com/v1`.  The base URL cannot be used on its own; you must append a path that identifies an operation and you may have to specify some path parameters as well.

[Click here](/) to go to the main BulkSMS developer site.

In order to give you an idea on how the API can be used, some JSON snippets are provided below.  Have a look at the [messages section](#tag/Message) for more information.

Probably the most simple example

```
{
    "to": "+27001234567",
    "body": "Hello World!"
}
```


You can send unicode automatically using the `auto-unicode` query parameter. 
Alternatively, you can specify UNICODE in the request body. Here is an example that sets the `encoding` explicitly

```
{
  "to": "+27001234567",
  "body": "Dobrá práce! Jak se máš?",
  "encoding": "UNICODE"
}
```

You can also specify a from number

```
{
    "from": "+27007654321",
    "to": "+27001234567",
    "body": "Hello World!"
}
```

Similar to above, but repliable

```
{
    "from": { "type": "REPLIABLE" },
    "to": "+27001234567",
    "body": "Hello World!"
}
```

A message to a group called Everyone

```
{
    "to": { "type": "GROUP", "name": "Everyone" },
    "body": "Hello World!"
}
```

A message to multiple recipients

```
{
    "to": ["+27001234567", "+27002345678", "+27003456789"],
    "body": "Happy Holidays!"
}
```

Sending more than one message in the same request

```
[
    {
        "to": "+27001234567",
        "body": "Hello World!"
    },
    {
        "to": "+27002345678",
        "body": "Hello Universe!"
    }
]
```

**The insecure base URL `http://api.bulksms.com/v1` is deprecated** and may in future result in a `301` redirect response, or insecure requests may be rejected outright. Please use the secure (`https`) URI above.

### HTTP Content Type

All API methods expect requests to supply a `Content-Type` header with the value `application/json`. All responses will have the `Content-Type` header set to `application/json`.

### JSON Formatting

You are advised to format your JSON resources according to strict JSON format rules. While the API does attempt to parse strictly invalid JSON documents, doing so may lead to incorrect interpretation and unexpected results.

Good JSON libraries will produce valid JSON suitable for submission, but if you are manually generating the JSON text, be careful to follow the JSON format. This include correct escaping of control characters and double quoting of property names.

See the [JSON specification](https://tools.ietf.org/html/rfc4627) for further information.

### Date Formatting

Dates are formatted according to ISO-8601, such as `1970-01-01T10:00:00+01:00` for 1st January 1970, 10AM UTC+1.

See the [Wikipedia ISO 8601 reference](https://en.wikipedia.org/wiki/ISO_8601) for further information.

Specifically, calendar dates are formatted with the 'extended' format `YYYY-MM-DD`. Basic format, week dates and ordinal dates are not supported. Times are also formatted in the 'extended' format `hh:mm:ss`. Hours, minutes and seconds are mandatory. Offset from UTC must be provided; this is to ensure that there is no misunderstanding regarding times provided to the API.

The format we look for is `yyyy-MM-ddThh:mm:ss[Z|[+-]hh:mm]`

Examples of valid date/times are`2011-12-31T12:00:00Z` `2011-12-31T12:00:00+02:00`

### Entity Format Modifications

It is expected that over time some changes will be made to the request and response formats of various methods available in the API.
Where possible, these will be implemented in a backwards compatible way.
To make this possible you are required to ignore unknown properties.
This enables the addition of information in response documents while maintaining compatibility with older clients.

### Optional Request Entity Properties

There are many instances where requests can be made without having to specify every single property allowable in the request format.
Any such optional properties are noted as such in the documentation and their default value is noted.


## Authorization

Supported authorization schemes:
- Basic Authentication

## Actions

### Retrieve Messages

> Retrieve the messages you have sent or received.<br/>
> <br/>
> All the parameters are optional.  If a value is not supplied for `filter`, the messages are not filtered.<br/>
> <br/>
> Messages can be filtered by supplying query clauses in the `filter` parameter. Each clause has the form `name=value` where `name` is the name of a filter field and `value` is a valid value for that field.  A value for a field is optional. Include a clause for a field in the filter only when there is a need to fetch messages that match some value for that field.<br/>
> For a numeric filter field, you can also use the less than operator (`<`).<br/>
> <br/>
> If present, the filter value must have at least one clause, but it can contain a combination of clauses. Multiple clauses are separated with the `&` symbol.  Semantically, multiple clauses form a [logical conjunction](https://en.wikipedia.org/wiki/Logical_conjunction).<br/>
> <br/>
> For example, if you want to list all messages that were sent as part of a particular submission, your filter contains two clauses and will look something like this<br/>
> ```<br/>
> type%3DSENT&submission.id%3D1-00000000000522347562<br/>
> ```<br/>
> Because `filter` is a request parameter, it is important to note that the value for this parameter must be *URL encoded*. In particular, the `=` encodes to `%3D` and the `&` encodes to `%26`.  Note that you do not have to encode the `<` character.<br/>
> <br/>
> Using the previous example to illustrate; after encoding and encasing it, the clauses are transformed into a request that looks like this<br/>
> ```<br/>
> GET /v1/messages?filter=type%3DSENT%26submission.id%3D1-00000000000522347562<br/>
> ```<br/>
> If the field name or the field value of a clause is not valid, a [bad_request error](errors#bad-request) is returned instead of the usual result.  The `detail` field of this error provides more information about the problem.<br/>
> <br/>
> The table below lists the fields available for filtering<br/>
> <br/>
> | Field | Type   | Values | Note and example |<br/>
> |-------|------|--------------------|------|<br/>
> | id            | Integer  | Positive integer  | Use the `id` field with `<` (or with `>`) to fetch messages that are older (or newer) than those that are already fetched. <br/>`filter=id<123456` |<br/>
> | type          | String  | SENT, RECEIVED  | SENT are Mobile Terminating (MT) SMSs; RECEIVED are Mobile Originating (MO) SMSs.<br/>`filter=type%3DSENT` |<br/>
> | submission.id | String  |  | `filter=submission.id%3D1-00000000000522347562` |<br/>
> | status.type   | String  | ACCEPTED, SCHEDULED, SENT, DELIVERED, FAILED  | See the message `status.type` field for more information. <br/>`filter=status.type%3DDELIVERED` |<br/>
> | status.id| String  |  | See the message `status.id` field for more information. `filter=status.id%3DFAILED.EXPIRED`|<br/>
> | submission.date | String | Formatted Date | A fully specified date (e.g. 2017-01-01T10:00:00+01:00).  Use this field with `<=`, `<`, `>` or `>=` to limit the values. <br/>`filter=submission.date%3E%3D2017-01-01T10%3A00%3A00%2B01%3A00` |<br/>
> | userSuppliedId  | String | | Use a string value you specified in the `userSuppliedId` property when you sent the message. Only `SENT` messages will be retrieved. <br/>`filter=userSuppliedId%3Dacc009876` |

*Tags:* `Message`

#### Input Parameters
* `limit` - _optional_ - The maximum number of messages that are returned.  The default is 1000.
The value of `limit` is not a guarantee that a specific number of messages will be in the response, even if there are more messages available.  Consider the case where you have 150 messages and you specify `limit=50`.  It is possible that only 49 messages will be returned.  The  way to make sure that there are no more messages is to submit a new call using the `id` filter field with the `<` operator (described below).
* `filter` - _optional_ - See the message filtering for more information.
* `sortOrder` - _optional_ - The default value is DESCENDING

If the `sortOrder` is DESCENDING, the newest messages be first in the result.  ASCENDING places the oldest messages on top of the response.

    Possible values: ASCENDING.

### Send Messages

> Send messages to one or more recipients.<br/>
> <br/>
> #### Repliability<br/>
> <br/>
> When a sent message is _repliable_,  the BulkSMS system can process an SMS response sent by your recipient.<br/>
> <br/>
> The message sent by your customer is called a mobile originating (MO) message and would be available under `RECEIVED` messages. <br/>
> You can obtain a list of MOs using the [retrieve messages API call](#tag/Message%2Fpaths%2F~1messages%2Fget).<br/>
> In addition you can also get a list of the MOs that are associated with a specific sent message (see the [list related messages API call](#tag/Message%2Fpaths%2F~1messages~1%7Bid%7D~1relatedReceivedMessages%2Fget)).<br/>
> <br/>
> If you use a specific _sender id_ in the `from` property of the send message, the message will not be repliable.<br/>
> If you want a message to be repliable, you need to specify `REPLIABLE` in the `from.type` property.<br/>
> <br/>
> If you do not set the `from` property, your account settings are considered to determine whether or not the message is repliable.<br/>
> If the _default repliable_ setting on your account is _yes_ then the message will be repliable. <br/>
> If this setting is _no_, the message will not be repliable.<br/>
> <br/>
> <br/>
> #### Body templates<br/>
> <br/>
> When sending a message you can use template fields to customise the message text.<br/>
> <br/>
> *Field based templates* allow you to create a message with place-holders for custom fields.  Fields are identified by a zero based index; the first field is `F0`, the second is `F1` and so on.  <br/>
> <br/>
> For example, let's say you want to send a daily SMS message to all your clients that tell them what their current balance is.  The `body` of the message could look something like this <br/>
> <br/>
> ```<br/>
> Good morning {F0######}, your balance is {F1######}<br/>
> ```<br/>
> <br/>
> In this message, the first field, `F0`, is the name  of the customer and he second field `F1` is the balance for that customer.  The `#` used to specify the maximum length  of the field.  Note that the maximum length allowed for the value includes the space taken by the braces, template name and hash symbol.  For example, the value `{F0#}` specifies a maximum length of `5`.  If the data is longer than this length, the data will be truncated when the message body is constructed.<br/>
> <br/>
> The data fields are provided in the property named `fields` in the `to` element.  Here is a complete example of how this might look<br/>
> <br/>
> ```<br/>
> {<br/>
>   "body": "Good morning {F0######}, your balance is {F1######}",<br/>
>   "to":  [<br/>
>       {"address": "27456789","fields": ["Harry", "$1345.23"] },<br/>
>       {"address": "27456785","fields": ["Sally", "$2345.58"] }<br/>
>   ]<br/>
> }<br/>
> ```<br/>
> <br/>
> If you are sending to contacts (or to groups) in your phonebook, you can use the *Phonebook based templates*.  These are similar to the templates described above, but they have specific names. The template for the contact's first name is identified by `fn` and the template for the contact's surname is identified by `sn`.  Below in an example that will work if the numbers are registered in your phonebook. <br/>
> <br/>
> ```<br/>
> {<br/>
>   "body": "Hi {fn######} {sn######}, have a great day!",<br/>
>   "to":  [<br/>
>       {"address": "27456789" },<br/>
>       {"address": "27456785" }<br/>
>   ]<br/>
> }<br/>
> ```

*Tags:* `Message`

#### Input Parameters
* `deduplication-id` - _optional_ - Safeguards against the possibility of sending the same messages more than once.

If a communication failure occurs during a submission, you cannot be sure that the submission was processed; therefore you would have to submit it again. When you post the retry, you must use the `deduplication-id` of the original post. The BulkSMS system uses this ID to check that the request was not previously processed. (If it was previously processed, the submission will succeed, and the behaviour will be indistinguishable to you from a non-duplicated submission). The ID expires after about 12 hours.

* `auto-unicode` - _optional_ - Specifies how to deal with message text that contains characters not present in the GSM 03.38 character set.

Messages that contain only GSM 03.38 characters are not affected by this setting. If the value is `true` then a message containing non-GSM 03.38 characters will be transmitted as a Unicode SMS (which is shorter, and could require extra SMSs to transmit). If the value is `false`, then non-GSM 03.38 characters will be replaced by the `?` character.

* `schedule-date` - _optional_ - Allows you to send a message in the future.

An example value is `2019-02-18T13:00:00+02:00`.  It encodes to `2019-02-18T13%3A00%3A00%2B02%3A00`.
Credits are deducted from your account immediately. Once submitted, scheduled messages cannot be changed or cancelled.
The date can be a maximum of two years in the future. If the value is in the past, the message will be sent immediately.
The date format requires you to supply an offset from UTC. You can decide to use the offset of your timezone, or maybe the zone of the recipient's location is more appropriate.
If the destination is a group, the group members are determined at the time that you submit the message; not the time the message is scheduled to be sent.

* `schedule-description` - _optional_ - A note that is stored together with a scheduled submission, which could be used to more easily identify the scheduled submission at a later date.

The value of this field is ignored if the `schedule-date` is not provided.
A value that is longer than 256 characters is truncated.


### Send message using GET

> A really simple interface for people who require a GET mechanism to submit a message.<br/>
> <br/>
> The URI is interpreted as UTF-8.<br/>
> <br/>
> __Note__ BulkSMS recommends that you use the more flexible Send Messages Operation when submitting SMS messages from your application.<br/>
> <br/>
> Here is an example of a GET<br/>
> ```http<br/>
> GET /v1/messages/send?to=%2b270000000&body=Hello%20World<br/>
> ```<br/>
> <br/>
> You can also use the same parameters to POST form encoded fields to `/messages`.<br/>
> Here is an example of a POST<br/>
> ```http<br/>
> POST /v1/messages<br/>
> Content-Type: application/x-www-form-urlencoded<br/>
> <br/>
> to=%2b27000000000&body=Hello+World<br/>
> ```

*Tags:* `Message`

#### Input Parameters
* `to` - _required_ - The phone number of the recipient.
* `body` - _required_ - The text you want to send.
* `deduplication-id` - _optional_ - Refer to the `deduplication-id` parameter.

### Show Message

> Get a the message by `id`.<br/>
> ```http<br/>
> GET /v1/messages/4023457654<br/>
> ```

*Tags:* `Message`

#### Input Parameters
* `id` - _required_ - The `id` of the message you want to retrieve

### List Related Messages

> Get the messages related to a sent message identified by `id`.<br/>
> <br/>
> For more information how this work, see the `relatedSentMessageId` field in the message.

*Tags:* `Message`

#### Input Parameters
* `id` - _required_ - The `id` of the sent message

### Get profile

> Returns information about your user profile

*Tags:* `Profile`

### List webhooks

> Contains a list of your webhooks

*Tags:* `Webhooks`

### Create a webhook

> A webhook is an URL that you can register if you want the BulkSMS system to notify you about your messages.<br/>
> You can create multiple webhooks, and each one will be called.<br/>
> If you want to be notified of `SENT` messages and `RECEIVED` messages you need to create two webhooks.<br/>
> <br/>
> __Important:__ The Webhooks methods do not work with an API Token, a username and password is required.<br/>
> <br/>
> ### Implementing your webhook<br/>
> <br/>
> When you implement your webhook, there are a few rules to be aware of:<br/>
> - Your webhook must process `POST` requests that contains an array of messages in the post body.  This input given to your webhook has the same structure as the output produced when you call [Retrieve Messages](#tag/Message%2Fpaths%2F~1messages%2Fget).<br/>
> - When you register or update your webhook, the URL will be tested by invoking it with an empty array (`[]`).<br/>
> - It is possible for your webhook to receive multiple updates for the same message and status. It happens from time to time that the mobile network duplicates status updates.<br/>
> - The order by which the webhook is invoked can be unexpected.  For example, if sender A replies before sender B, your webhook might get the reply from B first.<br/>
> - The webhook is expected to comply with good practices with regard to the status code it responds with.<br/>
>   - A status code in the `1xx` and `2xx` range is taken as an acknowledgement that the invocation was received and that the webhook host is ready to receive another.<br/>
>   - A status code in the `4xx` range is taken as a permanent problem and indicates that the webhook cannot process the message. The specific message that caused the error will be discarded, but your webhook will be invoked again when another message becomes available.<br/>
>   - Any other status code will be taken as a temporary problem; and indicates that the BulkSMS system should retry. The specific message that caused the error will not be discarded and your webhook will be invoked again with this message (see the subsequent section for more details on retry processing).<br/>
> - Your webhook has to respond within `30` seconds.  If no response is given in this time, the invocation will be retried.<br/>
> - It is good practise to add a secret to your URL in order to make it more secure. Here is an example:<br/>
> `https://www.example.com/hook.php?secret=pass763265word`<br/>
> - You can also use a non-standard port if necessary, for example: `https://www.example.com:8321/hook.php?secret=pass763265word`<br/>
> <br/>
> <br/>
> ### The retry process<br/>
> <br/>
> The process the BulkSMS systems follow to handle retries is roughly the following:<br/>
> - The first retry is scheduled for 90 seconds into the future.<br/>
> - After the first retry, subsequent failures will have longer delays, following this sequence - 3 minutes, 6 minutes, 12 minutes, 24 minutes, 48 minutes, 96 minutes, 3 hours, 6 hours, 12 hours, 24 hours.<br/>
> - When a failure occurs after the last delay in the sequence, the message is discarded.<br/>
> <br/>
> ### Problem reports via email<br/>
> <br/>
> It is strongly suggested that you provide an email address when you configure your webhook.<br/>
> An email will be sent to this address to keep you in the loop whenever there are problems with your webhook.<br/>
> In order to prevent your mailbox from being flooded, the BulksSMS system reports an observed error code no more than once in a 24 hour period.<br/>
> <br/>
> The following emails can be expected<br/>
>  - A __message retrying__ email is sent after an invocation has failed with a retry-able error.  This email is an early warning, allowing you to investigate your systems.<br/>
>  - A __message discarded__ email is sent after failure email is send when a message is discarded as a consequence of a non-retry-able error; or when the system decides to stop retrying.

*Tags:* `Webhooks`

### Delete a webhook

*Tags:* `Webhooks`

#### Input Parameters
* `id` - _required_ - The `id` of the webhook

### Read a webhook

*Tags:* `Webhooks`

#### Input Parameters
* `id` - _required_ - The `id` of the webhook

### Update a webhook

*Tags:* `Webhooks`

#### Input Parameters
* `id` - _required_ - The `id` of the webhook

## License

**flow**ground :- Telekom iPaaS / bulksms-com-connector<br/>
Copyright © 2019, [Deutsche Telekom AG](https://www.telekom.de)<br/>
contact: flowground@telekom.de

All files of this connector are licensed under the Apache 2.0 License. For details
see the file LICENSE on the toplevel directory.
