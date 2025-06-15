# fan 00

## Ledger entry

This note aims to describe the structure for the a debit / credit ledger entry.

## Format of ledger entry event

~~~yaml
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the posting creator>,
  "created_at": <unix timestamp in seconds>, //this should be used as timestamp of the posting period to which the posting should belong, rather than the real creation timestamp for better filtering
  "kind": 8001, //
  "tags": [
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
  "content": <optional posting description/comment>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

In a general ledger entry the amounts in the "journal_entry" tags should overall sum up to zero.  
Positive amount is a debit entry, a negative amount a credit entry (or interpreted like amount goes from (negative amount accounts) to (positive amount accounts).  
  
This note should introduce the "je_movement_type" (0, "general ledger movement"), "kind" (8001, "general ledger entry").
