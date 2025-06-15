# fan 01 

## Accounting area

Represents entity / area for which the accounting is to be done. And specifies meta-data of this entity / area.

## Format of accounting area event

~~~yaml
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37701, // as defined in NIP-01 a addresssable kind-number is used for this event-type
  "tags": [
    ["d", <identifier for the accounting area>],
    ["title", <title of accounting area>],
    ["description", <optional description of accounting area>],
    ["a", "37702:<pubkey of used general ledger structure event>:<d tag value of used general ledger structure event>", <recommended relay URL, optional>], //reference to adressable ledger structure event, see for more fan02.md
    ["f", <currency code as defined in NIP-69 or ISO 4217 used as functional currency of the accounting area; for BTC 'XBT' or 'BTC'>],
    ["a", "//possible future reference to another event-kind that specifies posting-rules (e.g. rules for who is allowed to post to which accounts, ...)"]
  ],
  "content": <optional accounting area description/comment>,
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

This note should introduce the "kind" \(37701, "accounting area"\).

~~~yaml
  ["accounting_area", <32-bytes lowercase hex-encoded public key of kind 38001 event creator of accounting_area for which is booked>, <64-bit id of accounting area of specified public key for which is booked; d-tag of corresponding kind 38001 event>],
      ["je_movement_type", <64-bit integer for movement type defined in the further implementation notes>],
      ["subledger_reference", <32-bytes lowercase hex-encoded id of a subledger reference posting>, <kind number of referenced subledger posting>],
      ["je_posting_partner", <32-bytes lowercase hex-encoded public key of kind 38001 event creator of accounting_area against which is booked>, <64-bit id of accounting area of specified public key against which is booked; d-tag of corresponding kind 38001 event>],
      ["je_creation_date", <unix timestamp in seconds>], //this should be the real "creation date" as in comparison to the above "created_at" field used as posting period data field
      ["je_backup", <reference to a backup file for the journal entry, e.g. saved on a blossom server (tbd defined more detailed)>]
      ["journal_entry", <64-bit integer for account number from account data structure from accounting area>, <financial data amount in smallest unit of the general ledger currency from accounting area>],
      ...
      ["journal_entry", <64-bit integer for account number from account data structure from accounting area>, <financial data amount in smallest unit of the general ledger currency from accounting area>]
  ]
~~~
