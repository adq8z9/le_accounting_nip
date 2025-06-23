# Note

This implementation proposals aim to be a on nostr events based form for exchanging (financial) accounting data.  
(The target is financial accounting, but the data structure is more general and could be also used for other forms of accounting).
  
It is implementations of nostr events from nostr-protocol. So in doubt and for everything not covered here (e.g. data flow) one should refer to the nostr-protocol specification. 

## Basic flow

An entity wants to do accounting work. It creates an accounting ledger / journal event for the accounting work (kind 37701). This ledger / journal references a kind 37702 ledger / journal structure event which defines general rules for a ledger / journal. The kind 37702 can either be created by someone other (e.g. a standard setter, etc.), or individually designed by the entity. 

The kind 37701 is fetched by the accountants who have the assigned accounting roles in the 37701-event. They can create accounting ledger / journal entry events (kind 7701) based on these.

When the work is done the entity (or someone else) can create a accounting report (kind 7702) based on a reporting template event (kind 37703) (or freely).

All events can be either kept private or be individually published publicly according to preference.

## Implementation

Even if full public accounting on nostr probably doesn't make sense in most cases, there might be some other use cases.
E.g. 
- Relaying batch transations between different sub-systems over nostr relays (as interface) (if needed in a gift wrap) (?)
- Accounting in a NIP-29 relay-based private group (?)
- (...?)

Another advantage of using fan besides interoperability might be, that you can rely on the further nostr infrastructure (relay implementations, client libraries, etc.).

## The following new kinds and tags are introduced

| kind          | description                     | NIP             |
| ------------- | ------------------------------- | --------------- |
| `7701`        | accounting ledger entry         | fan01           |
| `7702`        | accounting report               | fan03           |
| `2`           | Recommend Relay                 | 01 (deprecated) |

| name              | value                                                                                        | other parameters           | NIP         |
| ----------------- | -------------------------------------------------------------------------------------------- | -------------------------- | ----------- |
| `acc_debit_lacc`  | ledger account id for debit le entry                                                         | --                         | fan01       |
| `acc_credit_lacc` | ledger account id for credit le entry                                                        | --                         | fan01       |
| `acc_amount`      | integer amount of unit and scale seperately specified                                        | --                         | fan01       |
| `acc_unit_scale`  | Integer n of the factor 10^n for which holds (acc_amount / 10^n = amount in 'acc_unit' base) | --                         | fan01       |
| `acc_unit`        | Unit code for the acc_amount, acc_unit_scale tags                                            | --                         | fan01       |
| `acc_lmvt_type`   | ledger movement id for le entry                                                              | --                         | fan01       |
| `acc_partner'     | accounting partner id for le entry                                                           | --                         | fan01       |

