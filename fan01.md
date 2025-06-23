# fan 01

## Accounting ledger / journal

Represents ledger / journal for which the accounting is to be done. 

When updated backwards compatibility has to be ensured by the issuer.

## Format of accounting ledger / journal event

~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37701, // as defined in NIP-01 a addressable kind-number is used for this event-type
  "tags": [
      ["d", <identifier for accounting ledger / journal>],
      ["name", <name of accounting ledger / journal],
      ["description", <optional further description of the accounting ledger / journal>],
      ["a", <adressable reference to accounting ledger / journal structure event (kind 37702) as specified below>],
      //in the following rules for who can do postings, according to accounting roles from accounting ledger / journal structure event
      ["p", <pubkey>, <accounting role id described in the accounting ledger / journal structure event referenced above>],
      ...
      ["p", <pubkey>, <accounting role id described in the accounting ledger / journal structure event referenced above>],
      //in the following rules for accounting partner are specified, according to accounting partner categories from accounting ledger / journal structure event
      ["acc_partner", <id of accounting partner>, <string of accounting partner name>, <optional further description of accounting partner>, <accounting partner category id described in the accounting ledger / journal structure event referenced above>],
      ...
      ["acc_partner", <id of accounting partner>, <string of accounting partner name>, <optional further description of accounting partner>, <accounting partner category id described in the accounting ledger / journal structure event referenced above>],
      //... plus maybe further useful tags
  ],
  "content": <optional ledger description/comment>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~

## Format of accounting ledger / journal structure event

~~~
{
  "id": <32-bytes lowercase hex-encoded sha256 of the serialized posting data>,
  "pubkey": <32-bytes lowercase hex-encoded public key of the event creator>,
  "created_at": <unix timestamp in seconds>,
  "kind": 37702, // as defined in NIP-01 a addressable kind-number is used for this event-type
  "tags": [
      ["d", <identifier for accounting ledger / journal structure>],
      ["name", <name of accounting ledger / journal structure>],
      ["description", <optional further description of the accounting ledger / journal structure>],
      //in following accounting units that are allowed to be used in the accounting ledger structure
      ["acc_unit", <Allowed unit code for transfers / entries>],
      ...
      ["acc_unit", <Allowed unit code for transfers / entries>],
       //in following the ledger accounts that are allowed to be used in the accounting ledger structure
      ["acc_laccount", <ledger account id>, <string of ledger account name>, <further description of ledger account, e.g. define additional tags for content field of accounting ledger entry event>],
      ...
      ["acc_laccount", <ledger account id>, <string of ledger account name>, <further description of ledger account, e.g. define additional tags for content field of accounting ledger entry event>],
      //in following the ledger movement types that are allowed to be used in the accounting ledger structure
      ["acc_lmvt_type", <ledger movement type id>, <string of ledger movement type id>, <further description of ledger movement type, e.g. define additional tags for content field of accounting ledger entry event>],
      ...
      ["acc_lmvt_type", <ledger movement type id>, <string of ledger movement type id>, <further description of ledger movement type, e.g. define additional tags for content field of accounting ledger entry event>],
      //in following the accounting roles that are allowed to be used in the accounting ledger structure
      ["acc_role", <account role id>, <string of accounting role name>, <further description of accounting role, e.g. define additional restrictions for accounting role>, <allowed laccounts to book on, listed with lacc1:lacc2:... (empty when all allowed)>, <allowed lmvt_types to book, listed with lmvtt1:lmvtt2:... (empty when all allowed)>],
      ...
      ["acc_role", <account role id>, <string of accounting role name>, <further description of accounting role, e.g. define additional restrictions for accounting role>, <allowed laccounts to book on, listed with lacc1:lacc2:... (empty when all allowed)>, <allowed lmvt_types to book, listed with lmvtt1:lmvtt2:... (empty when all allowed)>],
      //in following the accounting partner categories that are allowed to be used in the accounting ledger structure
      ["acc_partner_cat", <id of accounting partner category>, <string of accounting partner category name>, <optional further description of accounting partner category>],
      ...
      ["acc_partner_cat", <id of accounting partner category>, <string of accounting partner category name>, <optional further description of accounting partner category>],
      //... plus maybe further useful tags
  ],
  "content": <optional ledger description/comment>
  "sig": <64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field>
}
~~~
