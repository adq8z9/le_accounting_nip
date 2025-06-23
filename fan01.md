# fan 01 

## Accounting area

Represents entity / area of a pubkey for which the accounting is to be done. And specifies meta-data of this entity / area.

## Format of accounting area event

~~~yaml
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37701, // as defined in NIP-01 a addresssable kind-number is used for this event-type
  "tags": [
    ["d", <identifier for the accounting area>],
    ["name", <name of accounting area>],
    ["description", <optional further description of the accounting area>],
    //... plus maybe further useful tags
  ],
  "content": <optional accounting area description/comment>,
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

## Accounting unit set

A set as specified in NIP-51, with **kind:37702** and tag values **\["a", \<adressable reference to accounting area event (kind 37701)\>]** for accounting areas of a group, **\["A", \<adressable reference to accounting area event (kind 37701)\>]** for parent accounting area on which is consolidated.
