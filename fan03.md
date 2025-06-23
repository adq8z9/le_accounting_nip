# fan 02

## Accounting report

Represents an accounting report based on the accounting data from the other fan.

## Format of accounting report event

~~~yaml
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37702, // as defined in NIP-01 a addressable kind-number is used for this event-type
  "tags": [
      ["d", <identifier for ledger structure>],
      ["title", <title of ledger],
      ["description", <optional further description of the ledger>],
      ["a", <adressable reference to accounting unit set (kind 37703) as specified below>],
      ["laccount", <ledger account id>, <string of ledger account name>, <optional further description of ledger account>],
      ...
      ["laccount", <ledger account id>, <string of ledger account name>, <optional further description of ledger account>],
      ["lmvt_type", <ledger movement type id>, <string of ledger movement type id>, <optional further description of ledger movement type>],
      ...
      ["lmvt_type", <ledger movement type id>, <string of ledger movement type id>, <optional further description of ledger movement type>],
      //... plus maybe further useful tags
  ],
  "content": <optional ledger description/comment>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

## Accounting unit set

A set as specified in NIP-51, with **kind:37703** and tag values **\["acc_unit", \<acc_unit, e.g. 'BTC'\>\]**.
