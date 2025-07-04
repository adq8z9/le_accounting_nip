~~~
{
      "name":<name of accounting ledger / journal>,
      "description":<optional further description of the accounting ledger / journal>,
      "acc_units":[<Allowed unit code for transfers / entries, e.g. ISO 4217-like codes for currency ('BTC'), but also sth like 'kg', or 'CO2Equ' might be thinkable>, <allowed unit>, ...],
      "acc_laccount_categories:[[<ledger account category id>, <string of ledger account category name>, <description of ledger account category>, <ledger account category id of parent category(ies))>], [...], ...],
      "acc_laccounts":[[<ledger account id>, <string of ledger account name>, <description of ledger account>, <ledger account category id of parent category>], [...], ...],
      "acc_lmvt_type_categories":[[<ledger movement type category id>, <string of ledger movement type category name>, <description of ledger movement type category>, <ledger movement type category id of parent category>], [...], ...],
      "acc_lmvt_types":[[<ledger movement type id>, <string of ledger movement type name>, <description of ledger movement type>, <ledger movement type category id of parent category>], [...], ...],
      "acc_roles":[[<account role id>, <string of accounting role name>, <description of accounting role>, [<allowed laccounts ids to book on for acc_role>, ...]], [<allowed lmvt_types ids to book on for acc_role>, ..], [...], ...],
      "accountants":[[<pubkey>, <accounting role id>], [...], ...],
      "acc_partner_categories":[[<id of accounting partner category>, <string of accounting partner category name>, <description of accounting partner category>, <accounting partner category id of parent category>], [...], ...],
}
~~~
