Outgoing Mobility Learning Agreement CNR API
============================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This document describes the **Outgoing Mobility Learning Agreement CNR API**.
This API is implemented by the receiving institution if it wants to be notified
whenever learning agreements kept on their partner institutions' servers are changed.

CNR stands for Change Notification Receiver. For a detailed introduction on how
CNR APIs work, please read [this page][cnr-intro].


Request method
--------------

 * Requests MUST be made with HTTP POST method. Servers MAY reject all other
   request methods.


Request parameters
------------------

Parameters MUST be provided in the `application/x-www-form-urlencoded` format.


### `sending_hei_id` (required)

Identifier of the sending HEI - the master of the Outgoing Mobility Learning Agreement objects
which just have been changed.


### `omobility_id` (repeatable, required)

A list of identifiers of Outgoing Mobility (Learning Agreement) objects
(no more than `<max-omobility-ids>` items). These are the mobilities
for which the learning agreement has been recently updated (or created) on the caller's side.
Please note, that the sending institution SHOULD send this notification to you even when it is *you*
who actually initiated the update (e.g. via the `update` endpoint of Outgoing Mobility Learning Agreements API).

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server SHOULD process all of them. Server implementers provide their
own chosen value of `<max-omobility-ids>` via their manifest entry (see
[manifest-entry.xsd](manifest-entry.xsd)). Clients SHOULD parse this value (or
assume it's equal to `1`).

[Here][omobility-las-comparison] you can read about Learning Agreement identifier.


Security
--------

This version of this API uses [standard EWP Authentication and Security, Version 2][sec-v2].
Server implementers choose which security methods they support by declaring them in their Manifest API entry.

This API does not expose any sensitive data, it only notifies the server that
it should reload portions of its data. For this reason, it is RECOMMENDED for
server implementers to not be overly strict on security methods they require
(i.e. it is RECOMMENDED to *not* require extra layers of encryption in requests
and responses - TLS seems more than enough).


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Please note, that receiving unknown `omobility_id` values is NOT an error.
   This can occur when the new Outgoing Mobility Learning Agreement has been created,
   and, for some reason, you did not save the Outgoing Mobility object before.

 * Servers MUST return a valid (HTTP 200) XML response whenever the request has
   been [properly received][bad-cnr-request]. Unless HTTP 200 is received,
   clients are RECOMMENDED to automatically retry the request after some time.


Response
--------

Servers MUST respond with a valid XML document described by the
[response.xsd](response.xsd) schema. See the schema annotations for further information.


Safety measures
---------------

It is NOT guaranteed that all notifications will be delivered to you promptly.
Some notifications may also **not reach you at all**, e.g. due to
implementation errors on the sending institution's server, or the fact that no
Notification Sender daemon has been implemented there (see
`<sends-notifications>` element in [Outgoing Mobility Learning Agreements API][omobility-las-api]'s
`manifest-entry.xsd`).

Therefore, you SHOULD periodically verify if your copies are up-to-date (or,
simply, choose to *not store* these copies). Proper caching techniques and/or
periodical use of `index` endpoint of [Outgoing Mobility Learning Agreements API][omobility-las-api]
can help you with that.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[omobility-las-api]: https://github.com/erasmus-without-paper/ewp-specs-api-omobility-las
[cnr-intro]: https://github.com/erasmus-without-paper/ewp-specs-architecture#cnr
[bad-cnr-request]: https://github.com/erasmus-without-paper/ewp-specs-architecture#bad-cnr-request
[sec-v2]: https://github.com/erasmus-without-paper/ewp-specs-sec-intro/tree/stable-v2
[omobility-las-comparison]: https://github.com/erasmus-without-paper/ewp-specs-api-omobility-las/#outgoing-mobility-learning-agreement-api-vs-outgoing-mobilities-api
