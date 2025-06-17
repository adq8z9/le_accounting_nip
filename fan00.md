# fan 00

## Accounting ledger / journal entry

This note aims to describe the structure for a debit / credit ledger entry.  

There is just one transfer per event possible.  
Multi-debit-muli-credit-transfers should be handled, if not divisible in single events, over control-accounts / clearing-accounts. 

## Format of accounting ledger entry event

~~~yaml
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the posting creator>,
  "created_at": <unix timestamp in seconds>, //this should be used as timestamp of the posting period / time to which the posting should belong, rather than the real creation timestamp of the event, for better filtering
  "kind": 8711, //
  "tags": [
    ["debit_lacc", <ledger account id from below specified accounting ledger event reference>],
    ["credit_lacc", <ledger account id from below specified accounting ledger event reference>],
    ["acc_amount", <Integer amount of the transfer in the unit and scale provided below>],
    ["acc_unit", <Unit code for the transfer; The allowed unit codes are specified in the accounting ledger event referenced below>],
    ["acc_unit_scale", <Integer n of the factor 10^n for which holds (le_amount-field / 10^n = amount in 'acc_unit' base)],
    ["published_at", <unix timestamp in seconds>], // timestamp at which the event was created / published
    -["a", <>, "accounting area"], //accounting area event, fan01
    ["a", <adressable reference to accounting ledger event (kind 37702) as specified in fan02>]
    -["posting partner", <>]
    ["lmvt_type", <ledger movement type id from above referenced ledger structure event reference>],
    -["sl reference", <>]
    -["external reference", <>]
    -["media reference", <>]
  ]
  "content": <optional posting description/comment>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

The structure ensures that debit and credits equal, if valid 'debit_lacc' and 'credit_lacc' are specified.  
  
The movement-flow of the transfer can be normally interpreted as going from the 'credit_lacc' to the 'debit_lacc' account.  
