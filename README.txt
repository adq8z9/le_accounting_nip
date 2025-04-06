Note:

This implementation proposals aim to be a on nostr events based form for exchanging (financial) accounting data between relays/storage and clients.

The basic structure is aimed to be centered from the statement of financial position and compatible to IFRS. 

It is implementations of nostr events from nostr-protocol. So in doubt and for everything not covered here (e.g. data flow) one should refer to the nostr-protocol specification.

The following new kinds, tags and for this implementation spefic "je_movement_types" are introduced:

Event kinds:
(8001, general ledger entry)

Tags:
(accounting_area, ")
(je_movement_types, ")
(subledger_reference, ")
(je_posting_partner, ")
(je_creation_date, ")
(journal_entries, ")
(je_backup, ")

"Je_movement_types":
(0, general ledger movement)
