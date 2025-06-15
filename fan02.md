# fan 02

## Ledger structure

Represents ledger structure for which the accounting is to be done. And specifies meta-data of this ledger structure.

## Format of ledger structure event

~~~yaml
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37702, // as defined in NIP-01 a addressable kind-number is used for this event-type
  "tags": [
      ["d", <identifier for ledger structure>],
      ["laccount", <ledger account id>, <string of ledger account name>, <optional further description of ledger account>]
      ...
      ["laccount", <ledger account id>, <string of ledger account name>, <optional further description of ledger account>]
  ]
  "content": <optional accounts structure description/comment>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

This note should introduce the "kind" (37702, "Ledger structure") and the new "tag" ('laccount', "Ledger account id", "Ledger account name", "Optiional further ledger account description").
