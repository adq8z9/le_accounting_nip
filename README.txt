Note:

This implementation proposals aim to be a on nostr events based form for exchanging (financial) accounting data between relays/storage and clients.

The basic structure is aimed to be centered from the statement of financial position and compatible to IFRS. 

It is implementations of nostr events from nostr-protocol. So in doubt and for everything not covered here (e.g. data flow) one should refer to the nostr-protocol specification.

The following new kinds, tags and for this implementation specific "je_movement_types" are introduced:

Event kinds:
- from 0 -
(8001, general ledger entry)
- from 1 -
(38001, accounting area event)

Tags:
- from 0 -
(accounting_area, ")
(je_movement_types, ")
(subledger_reference, ")
(je_posting_partner, ")
(je_creation_date, ")
(journal_entries, ")
(je_backup, ")
- from 1 -
(account_structure, ")
("allowed_posters", ")

"Je_movement_types":
- from 0 -
(0, general ledger movement)
