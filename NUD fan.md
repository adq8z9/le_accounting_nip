# NUD fan

(Financial) accounting on nostr.

## Pre Notes

This implementation proposal introduces an on nostr events based form for exchanging (financial) accounting data.  
The target is financial accounting, but the data structure is more general and could be also used for other forms of accounting.

In doubt and for everything not covered here one should refer to the other nostr-protocol specifications and the main-NIPs. 

### Basic flow

An entity wants to do accounting work. It creates an accounting ledger / journal event (kind 37701) for the specific accounting work. The ledger / journal event specifies the context for the specific accounting work to be done.

The kind 37701 is fetched by, or submitted to, the accountants (could also be bots or similar) who have the assigned accounting roles in the 37701-event. They can create accounting ledger / journal entry events (kind 7701) based on these.

When the work is done, or just inbetween, the entity (or someone else) can create a accounting report (kind 7702) based on a reporting template event (kind 37703) (or freely) and the ledger event as well as ledger entry events. The kind 37703 can either be created by someone other (e.g. a standard setter, etc.), or individually designed by the entity. The accounting report (kind 7702) can be used for external or internal reporting purposes and should be auditable against the underlying event data. Data visualisations and/or ad-hoc reports could also be implemented by clients without a report event, e.g. just for monitoring purposes.

All events can be either kept private or be individually published publicly according to preference.

### The following new kinds and tags are introduced in this NUD

| kind          | description                           | 
| ------------- | ------------------------------------- | 
| `7701`        | accounting ledger entry               | 
| `7702`        | accounting report                     | 
| `37701`       | accounting ledger / journal           | 
| `37703`       | accounting report template            | 

| tag name              | value                                                                                            | other parameters                                                                                                      |
| --------------------- | ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------- | 
| `acc_le_debit_lacc`   | ledger account id for debit ledger entry                                                         | --                                                                                                                    | 
| `acc_le_credit_lacc`  | ledger account id for credit ledger entry                                                        | --                                                                                                                    | 
| `acc_amount`          | integer amount for accounting value (accounting unit and scale seperately specified)             | --                                                                                                                    | 
| `acc_unit_scale`      | Integer n of the factor 10^n for which holds (acc_amount / 10^n = amount in 'acc_unit' base)     | --                                                                                                                    | 
| `acc_unit`            | Unit code for the acc_amount, e.g. ISO 4217-like codes for currency ('BTC'), but also sth like 'kg', or 'CO2Equ' might be thinkable | --                                                                                 | 
| `acc_le_mvt_type`     | ledger movement id for le entry                                                                  | --                                                                                                                    | 
| `acc_le_partner_category`      | accounting partner id for le entry                                                               | --                                                                                                                    | 


The other used kinds and tags are already defined in the main-NIPs and should be used with the values / parameters as defined there.

## Accounting ledger / journal entry

This describe the structure for a debit / credit ledger entry.  

There is just one transfer per event possible.  
Multi-debit-muli-credit-transfers should be handled, if not divisible in single events, over control-accounts / clearing-accounts. 

**Format of accounting ledger entry event:**
~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the posting creator>,
  //the "created_at"-value should be used as timestamp of the posting period / time to which the posting should belong, rather than the real creation timestamp of the event, for better filtering
  "created_at": <unix timestamp in seconds>,
  "kind": 7701, // as defined in NIP-01 a regular kind-number is used for this event-type
  "tags": [
    ["acc_le_debit_lacc", <ledger account id from below specified accounting ledger / journal event reference>],
    ["acc_le_credit_lacc", <ledger account id from below specified accounting ledger / journal event reference>],
    ["acc_amount", <Integer amount of the transfer in the unit and scale provided below>],
    ["acc_unit_scale", <Integer n of the factor 10^n for which holds (acc_amount-field / 10^n = amount in 'acc_unit' base)],
    ["acc_unit", <Unit code for the transfer; The allowed unit codes are specified in the accounting ledger event referenced below>],
    ["acc_le_lmvt_type", <ledger movement type id from below specified accounting ledger / journal event reference>],
    ["acc_le_partner_category", <id of accounting partner category of transaction>],
    ["A", <adressable reference to accounting ledger / journal event (kind 37701), for which is booked>],
    ["published_at", <unix timestamp in seconds>], // timestamp at which the event was created / published
    //in the following optional references to supporting documents for the entry can be included
    ["i", <external content IDs which are in reference to the posting according to NIP-73>],
    ["e", <reference to related events e.g. transfer from other sub-ledger, etc.>],
    ["imeta", <imeta tag according to NIP-92>],
    ["e", <reference to a kind 1063 media event>],
  ]
  "content":"{
      "description":<description of the posting>,
      <more individually needed Meta-Data could be included (e.g. as specified in a kind 37702 accounting ledger / journal structure event). Format in the content field as a whole in a stringified JSON object (e.g. add new data element: "xy":<zz>,)>,
  }",
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

The structure ensures that debit and credits equal, if valid 'acc_le_debit_lacc' and 'acc_le_credit_lacc' are specified.  

The movement-flow of the transfer can be normally interpreted as going from the 'acc_credit_lacc' (negative entry) to the 'acc_debit_lacc' (positive entry) account.  

The following fields are used as described in the "content"-field:  
`"description"`. 

## Accounting ledger / journal

Represents ledger / journal for which the accounting is to be done. 

When updated backwards compatibility has to be ensured by the issuer.

**Format of accounting ledger / journal event:**
~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37701, // as defined in NIP-01 a addressable kind-number is used for this event-type
  "tags": [
      ["d", <identifier for accounting ledger / journal>],
      ["r", <relay for accounting work>],
      ...,
      ["r", <relay for accounting work>],
      ["server", <reference to blossom file storage server or NIP 96 file storage server for accounting work>],
      ...
      ["server", <reference to blossom file storage server or NIP 96 file storage server for accounting work>],
      ["p", <pubkeys of below referenced allowed accountants for the ledger>],
      ...
      ["p", <pubkeys of below referenced allowed accountants for the ledger>]
  ],
  "content": "{
      "name":<name of accounting ledger / journal>,
      "description":<optional further description of the accounting ledger / journal>,
      "acc_units":[<Allowed unit code for transfers / entries, e.g. ISO 4217-like codes for currency ('BTC'), but also sth like 'kg', or 'CO2Equ' might be thinkable>, <allowed unit>, ...],
      "acc_laccount_categories":[{"id":<ledger account category id>, "name":<string of ledger account category name>, "description":<description of ledger account category>, "parent_id":[<ledger account category id of parent category>, ...]}, {...}, ...],
      "acc_laccounts":[{"id":<ledger account id>, "name":<string of ledger account name>, "description":<description of ledger account>, "parent_id":[<ledger account category id of parent category>, ...]}, {...}, ...],
      "acc_lmvt_type_categories":[{"id":<ledger movement type category id>, "name":<string of ledger movement type category name>, "description":<description of ledger movement type category>, "parent_id":[<ledger movement type category id of parent category>, ...]}, {...}, ...],
      "acc_lmvt_types":[{"id":<ledger movement type id>, "name":<string of ledger movement type name>, "description":<description of ledger movement type>, "parent_id":[<ledger movement type category id of parent category>, ...]}, {...}, ...],
      "acc_partner_categories":[{"id":<ledger accounting partner category id>, "name":<string of ledger accounting partner category name>, "description":<description of ledger accounting partner category>, "parent_id":[<ledger accounting partner category id of parent category>, ...]}, {...}, ...],
      "acc_partners":[{"id":<ledger accounting partner id>, "name":<string of ledger accounting partner name>, "description":<description of ledger accounting partner>, "parent_id":[<ledger accounting partner category id of parent category>, ...]}, {...}, ...],
      "acc_roles":[{"id":<ledger accounting role id>, "name":<string of ledger accounting role name>, "description":<description of ledger accounting role>, "Allowed_acc":[<allowed laccounts ids to book on for acc_role>, ...], "Allowed_mvt":[<allowed lmvt_types ids to book on for acc_role>, ..], "Allowed_par":[<allowed acc partner ids to book on for acc role>,...]}, {...}, ...],
      "accountants":[[<pubkey>, <accounting role id>], [...], ...],
  }",
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

The following fields are introduced and used as described in the "content"-field:  
`"name"`, `"description"`, `"acc_units"`, `"acc_laccount_categories"`, `"acc_laccounts"`, `"acc_lmvt_type_categories"`, `"acc_lmvt_types"`, `"acc_partner_categories"`, `"acc_partners"`, `"acc_roles"`, `"accountants"`. 


## Accounting report

Represents an accounting report based on the accounting data events from above.

A accounting report event could be timestamped and covered by an attestation event.

**Format of accounting report event:**
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
      //report file
      ["IMETA", <imeta tag according to NIP-92>, "report"], // or
      ["E", <reference to a kind 1063 media event>, "report"],
      //optional backup files references with which the report should be reproducable
      ["x", <hash of used data with which report is reproproducable>, "data"], // or
      ["imeta", <imeta tag according to NIP-92>, "data"], // or
      ["e", <reference to a kind 1063 media event which could be published public or kept private>, "data"],
      //... plus maybe further useful tags as used in other NIPs/NUDs if fitting
  ],
  "content": <tringified JSON object with more data if needed>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

**Format of accounting report template event:**
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
      ["a", <adressable reference to accounting ledger / journal structure event (kind 37702) as specified in this NUD>],
      ["m", <Mime type of result of the report>],
      ["e", <optional reference to kind 1337 code snippet with implementations for pseudo code>],
      ...
      ["e", <optional reference to kind 1337 code snippet with implementations for pseudo code>],
      //... plus maybe further useful tags as used in other NIPs/NUDs if fitting
  ],
  "content": <pseudo-code how to generate report result mime-type file (should be a function defining input parameters and return mime-type element)>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

## More implementation considerations

Even if full public accounting on nostr probably doesn't make sense in most cases, there might be some other use cases.
Also this NUD might have use cases in combination with other NIPs/NUDs.
E.g. 
- Relaying batch transations between different sub-systems over nostr relays (as interface) (if needed in a gift wrap) (?)
- Accounting in a NIP-29 relay-based private group, or in general on a private relay (?)
- An accounting report event could be timestamped and covered by an attestation event
- (...?)

## License

This 'NUD fan' is public domain.
