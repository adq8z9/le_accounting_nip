# NUD fan

(Financial) accounting on nostr.

## Pre Note

This implementation proposal introduces a nostr events based form for exchanging (financial) accounting data.  
The target is financial accounting, but the data structure is more general and could be also used for other forms of accounting.

In doubt and for everything not covered here one should refer to the other nostr-protocol specifications and the main-NIPs. 

### Basic flow

An entity wants to do accounting work. It creates an accounting ledger event (kind `37701`) for the specific accounting work. The accounting ledger event specifies the context for the specific accounting work to be done.

The kind `37701`-event is fetched by, or submitted to, the accountants, who have assigned accounting roles in the `37701`-event. They can create accounting ledger entry events (kind `7701`) based on these. The accounting ledger entry events contain the details of transactions.

When the work is done, or interim, an accounting report event (kind `7702`) can be published, based on the kind `7701`-events and `37701`-events. The accounting report event can be used for external or internal reporting purposes and should be auditable against the underlying event data. Data visualisations and/or ad-hoc reports should also be implemented by clients without a report event, e.g. just for monitoring purposes.

### The following new kinds and tags are introduced in this NUD

| kind          | description                           | 
| ------------- | ------------------------------------- | 
| `7701`        | accounting ledger entry               | 
| `7702`        | accounting report                     | 
| `37701`       | accounting ledger                     | 

The other used kinds and tags are already defined in the main-NIPs and should be used with the values / parameters as defined there.  
The relevant new internal data-fields are stored in the content-field of the events.

## Accounting ledger

Represents ledger for which the accounting is to be done. 

When updated backwards compatibility has to be ensured by the issuer.

**Format of accounting ledger event:**
~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37701, // as defined in NIP-01 a addressable kind-number is used for this event-type
  "tags": [
      ["d", <identifier for accounting ledger>],
      // remark for this namespace labels: with it, in the future, the kind number could be potentially used for additional 'ledger-meta-data-events' if needed.
      ["L", "leaccountingnip"],
      ["l", "ledger", "leaccountingnip"],
      ["r", <relay for accounting work>],
      ...,
      ["r", <relay for accounting work>],
      ["server", <reference to blossom file storage server or NIP 96 file storage server for accounting work>],
      ...
      ["server", <reference to blossom file storage server or NIP 96 file storage server for accounting work>],
      ["p", <pubkeys of below referenced allowed accountants for the ledger>],
      ...
      ["p", <pubkeys of below referenced allowed accountants for the ledger>],
      //if needed more of the content field fields could be moved into the tags
  ],
  "content": "{
      "name":<name of accounting ledger>,
      "description":<optional further description of the accounting ledger>,
      "acc_units":[<Allowed unit code for entries, e.g. ISO 4217-like codes for currency ('BTC'), but also sth like 'kg', or 'CO2Equ' might be thinkable>, <allowed unit>, ...],
      "acc_account_categories":[{"id":<ledger account category id>, "name":<string of ledger account category name>, "description":<description of ledger account category>, "parent_id":[<ledger account category id of parent category>, ...]}, {...}, ...],
      "acc_accounts":[{"id":<ledger account id>, "name":<string of ledger account name>, "description":<description of ledger account>, "parent_id":[<ledger account category id of parent category>, ...]}, {...}, ...],
      "acc_mvt_type_categories":[{"id":<ledger movement type category id>, "name":<string of ledger movement type category name>, "description":<description of ledger movement type category>, "parent_id":[<ledger movement type category id of parent category>, ...]}, {...}, ...],
      "acc_mvt_types":[{"id":<ledger movement type id>, "name":<string of ledger movement type name>, "description":<description of ledger movement type>, "parent_id":[<ledger movement type category id of parent category>, ...]}, {...}, ...],
      "acc_partner_categories":[{"id":<ledger accounting partner category id>, "name":<string of ledger accounting partner category name>, "description":<description of ledger accounting partner category>, "parent_id":[<ledger accounting partner category id of parent category>, ...]}, {...}, ...],
      "acc_partners":[{"id":<ledger accounting partner id>, "name":<string of ledger accounting partner name>, "description":<description of ledger accounting partner>, "parent_id":[<ledger accounting partner category id of parent category>, ...]}, {...}, ...],
      "acc_roles":[{"id":<ledger accounting role id>, "name":<string of ledger accounting role name>, "description":<description of ledger accounting role>, }, {...}, ...],
      "acc_accountants":[[<pubkey>, <accounting role id>], [...], ...],
      "acc_rule_categories":[{"id":<ledger accounting rule category id>, "name":<string of ledger accounting rule category name>, "description":<description of ledger accounting rule category>, "parent_id":[<ledger accounting rule category id of parent category>, ...]}, {...}, ...],
      "acc_rule":[{"id":<ledger accounting rule id>, "name":<string of ledger accounting rule name>, "description":<description of ledger accounting rule>, "parent_id":[<ledger accounting rule category id of parent category>, ...], "rules":[<tbd, sth where you could specify, e.g. accountant or accountant role xy can just book on accounts yz, etc.>,...]}, {...}, ...],
      <more individually needed Data could be included (e.g. add new data element: "xy":<zz>,)>,
  }",
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

The following fields are introduced and used as described in the "content"-field:  
`"name"`, `"description"`, `"acc_units"`, `"acc_account_categories"`, `"acc_accounts"`, `"acc_mvt_type_categories"`, `"acc_mvt_types"`, `"acc_partner_categories"`, `"acc_partners"`, `"acc_roles"`, `"accountants"`. 

## Accounting ledger entry

This describe the structure for a debit-credit-ledger-entry.  

There is just one transfer per event possible.  
Multi-debit-muli-credit-transfers should be handled, if not divisible in single events, over clearing-accounts. 

**Format of accounting ledger entry event:**
~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the posting creator>,
  //the "created_at"-value should be used as timestamp of the posting period to which the posting should belong, rather than the real creation timestamp of the event, for better filtering
  "created_at": <unix timestamp in seconds>,
  "kind": 7701, // as defined in NIP-01 a regular kind-number is used for this event-type
  "tags": [
    ["A", <reference to adressable accounting ledger event (kind 37701), for which is booked>],
    // remark for this namespace labels: It may also be feasible/useful, to account for a namespace outside a nostr-ledger-event, e.g. a fixed namespace ("example-url.ledger"), which is not personally fitted. Than accounting without a 37701-event could also be done.
    ["L", <reference to adressable accounting ledger event (kind 37701), for which is booked; alternatively other namespace outside a nostr-ledger-event>],
    ["l", "account:<id of debit account id from content field>", <reference to adressable accounting ledger event (kind 37701), for which is booked; alternatively other namespace outside a nostr-ledger-event>],
    ["l", "account:<id of credit account id from content field>", <reference to adressable accounting ledger event (kind 37701), for which is booked; alternatively other namespace outside a nostr-ledger-event>],
    ["l", "mvt_type:<id of movement type id from content field>", <reference to adressable accounting ledger event (kind 37701), for which is booked; alternatively other namespace outside a nostr-ledger-event>],
    ["l", "partner:<id of acc partner id from content field>", <reference to adressable accounting ledger event (kind 37701), for which is booked; alternatively other namespace outside a nostr-ledger-event>],
    // remark for this namespace labels: with it, in the future, the kind number could be potentially used for additional 'ledger-entry-data-events' if needed.
    ["L", "leaccountingnip"],
    ["l", "ledgerentry", "leaccountingnip"],
    ["published_at", <unix timestamp in seconds>], // timestamp at which the event was published
    //in the following optional references to supporting documents for the entry can be included
    ["i", <external content IDs which are supporting documents for the posting according to NIP-73>],
    ["e", <reference to related events e.g. transfer from other sub-ledger, etc.>],
    ["imeta", <imeta tag according to NIP-92>],
    ["e", <reference to a kind 1063 media event>],
    //if needed more of the content field fields could be moved into the tags
  ]
  "content":"{
    "debit_account": <ledger account id from above specified accounting ledger event reference>,
    "credit_account", <ledger account id from above specified accounting ledger event reference>,
    "acc_amount": [<Integer amount of the transfer in the unit and scale provided in this object>, <Integer n of the factor 10^n for which holds (acc_amount-field / 10^n = amount in 'acc_unit' base)>, <Unit code for the transfer; The allowed unit codes are specified in the accounting ledger event referenced avove>],
    "acc_mvt_type": <ledger movement type id from avove specified accounting ledger event reference>,
    "acc_partner": <id of accounting partner of transaction from above specified accounting ledger event reference>,
    "description": <description of the posting>,
    <more individually needed Data could be included (e.g. add new data element: "xy":<zz>,)>,
  }",
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

The structure ensures that debit and credits equal, if valid 'debit_account' and 'credit_account' are specified.  

The movement-flow of the transfer can be normally interpreted as going from the 'credit_account' (negative entry) to the 'debit_account' (positive entry) account.  

The following fields are used as described in the "content"-field:  
`"debit_account"`, `"credit_account"`, `"acc_amount"`, `"mvt_type"`, `"acc_partner"`, `"description"`. 

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
      ["A", <reference to adressable accounting ledger event (kind 37701), for which is reported>],
      // remark for this namespace labels: with it, in the future, the kind number could be potentially used for additional 'ledger-report-events' if needed.
      ["L", "nostr.leaccountingnip"],
      ["l", "report", "nostr.leaccountingnip"],
      //report file
      ["IMETA", <imeta tag according to NIP-92>, "report"], // or
      ["E", <reference to a kind 1063 media event according to NIP-94>, "report"],
      //optional backup files references with which the report should be reproducable/auditable
      ["x", <hash of used data with which report is reproproducable>, "data"], // or
      ["imeta", <imeta tag according to NIP-92>, "data"], // or
      ["e", <reference to a kind 1063 media event according to NIP-94 which could be published public or kept private>, "data"],
      //... plus maybe further useful tags as used in other NIPs/NUDs if fitting
  ],
  "content": <tringified JSON object with more data if needed>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

## More implementation considerations

Even if full public accounting on nostr probably doesn't make sense in most cases, there might be some other use cases besides.  
Also this NUD might have use cases in combination with other NIPs/NUDs.
E.g. 
- Relaying batch transations between different sub-systems over nostr relays (as interface or aggregator) (if needed in a gift wrap)
- Accounting in a NIP-29 relay-based private group, or in general on a private relay
- Decentralized public accounting (e.g. for public organization or where you would want this level of public accounting)
- Similar app to 'Splitwise', where you split expenses in a group
- For NIP 60 Spending History Event for ecash wallet on nostr
- An accounting report event could be timestamped and covered by an attestation event
- (...?)

## License

This 'NUD' is public domain.
