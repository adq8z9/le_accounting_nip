# fan 02

## Accounting ledger / journal

Represents ledger structure for which the accounting is to be done. And specifies meta-data of this ledger structure.  

Ledger events can be issued preconfigured by someone and then used / referenced by the accounting entity, or the accounting entity makes its own ledger structure events.  
When updated backwards compatibility has to be ensured by the issuer.

## Format of accounting ledger event

~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37703, // as defined in NIP-01 a addressable kind-number is used for this event-type
  "tags": [
      ["d", <identifier for ledger structure>],
      ["name", <name of ledger],
      ["description", <optional further description of the ledger>],
      ["a", <adressable reference to allowed accounting unit set (kind 37703) as specified below>],
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

A set as specified in NIP-51, with **kind:37704** and tag values **\["acc_unit", \<acc_unit, e.g. 'BTC'\>\]**.
