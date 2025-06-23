# fan 02

## Accounting report

Represents an accounting report based on the accounting data from the other fan.

## Format of accounting report event

~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 7702, // as defined in NIP-01 a regular kind-number is used for this event-type
  "tags": [
      ["name", <name of accounting report>],
      ["description", <optional further description of the accounting report>],
      ["a", <adressable reference to accounting report template event (kind 37703) as specified below>],
      //
      //... plus maybe further useful tags
  ],
  "content": <optional accounting report description/comment>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

## Format of accounting report template event

~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37703, // as defined in NIP-01 a addressable kind-number is used for this event-type
  "tags": [
      ["d", <identifier for accounting report template>],
      ["name", <name of accounting report template>],
      ["description", <optional further description of the accounting report template>],
      ["a", <adressable reference to accounting ledger / journal structure event (kind 37702) as specified below>],
      ["m", <Mime type of result of the report>],
      ["e", <reference to kind 1337 code snippet with implementations for pseudo code>],
      ...
      ["e", <reference to kind 1337 code snippet with implementations for pseudo code>],
      //... plus maybe further useful tags
  ],
  "content": <pseudo-code how to generate report result mime-type file (should be a function defining input parameters and return mime-type element)>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~
