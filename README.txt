Note:

This implementation proposals aim to be a on nostr events based form for exchanging (financial) accounting data between relays/storage and clients.

The basic structure is aimed to be centered from the statement of financial position and compatible to IFRS. 

It is implementations of nostr events from nostr-protocol. So in doubt and for everything not covered here (e.g. data flow) one should refer to the nostr-protocol specification.

The following new kinds, tags and for this implementation spefic "movement_types" are introduced:

Event kinds:
(8001, general ledger entry)

Tags:
(accounting_area, ")
(movement_types, ")
(subledger_reference, ")
(posting_partner, ")
(creation_date, ")
(journal_entries, ")

"Movement_types":
(0, general ledger movement)
