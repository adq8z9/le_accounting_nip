# fan 00

## Accounting ledger / journal entry

This note aims to describe the structure for a debit / credit ledger entry.  

There is just one transfer per event possible.  
Multi-debit-muli-credit-transfers should be handled, if not divisible in single events, over control-accounts / clearing-accounts. 

## Format of accounting ledger entry event

~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the posting creator>,
  "created_at": <unix timestamp in seconds>, //this should be used as timestamp of the posting period / time to which the posting should belong, rather than the real creation timestamp of the event, for better filtering
  "kind": 7701, // as defined in NIP-01 a regular kind-number is used for this event-type
  "tags": [
    ["acc_debit_lacc", <ledger account id from below specified accounting ledger / journal event reference>],
    ["acc_credit_lacc", <ledger account id from below specified accounting ledger / journal event reference>],
    ["acc_amount", <Integer amount of the transfer in the unit and scale provided below>],
    ["acc_unit_scale", <Integer n of the factor 10^n for which holds (le_amount-field / 10^n = amount in 'acc_unit' base)],
    ["acc_unit", <Unit code for the transfer; The allowed unit codes are specified in the accounting ledger event referenced below>],
    ["acc_lmvt_type", <ledger movement type id from below specified accounting ledger / journal event reference>],
    ["acc_partner", <id of accounting partner of transaction>],
    ["A", <adressable reference to accounting ledger / journal event (kind 37701), for which is booked>],
    ["published_at", <unix timestamp in seconds>], // timestamp at which the event was created / published
    //in the following optional references to supporting documents for the entry can be included
    ["i", <external content IDs which are in reference to the posting according to NIP-73>],
    ["e", <reference to related events e.g. transfer from other sub-ledger, etc.>],
    ["imeta", <imeta tag according to NIP-92>],
    ["a", <reference to a kind 1063 media event>],
    //... plus maybe further useful tags
  ]
  "content": <optional posting description/comment; more individually needed Meta-Data could be included>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

The structure ensures that debit and credits equal, if valid 'acc_debit_lacc' and 'acc_credit_lacc' are specified.  
  
The movement-flow of the transfer can be normally interpreted as going from the 'acc_credit_lacc' (negative entry) to the 'acc_debit_lacc' (positive entry) account.  
