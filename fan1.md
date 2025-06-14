# 1: Accounting area.

Represents entity / area for which the accounting is to be done. And specifies meta-data of this entity / area.

## Format of Accounting Area Event

  \{
    "id": \<32-bytes lowercase hex-encoded sha256 of the serialized posting data\>,
    "pubkey": \<32-bytes lowercase hex-encoded public key of the posting creator\>,
    "created_at": \<unix timestamp in seconds\>,
    "kind": 37701, // as defined in NIP-01 a addresssable kind-number is used for this event-type
    "tags": \[
      \["d", \<identifier for the accounting area\>\],
      \["title", \<title of accounting area\>],
      \["description", \<optional description of accounting area\>],
      \["a", "37702:\<32-bytes lowercase hex of a pubkey of used accounts structure event\>:\<d tag value of used accounts structure event>", \<recommended relay URL, optional\>\], //reference to adressable accounts structure event, see for more fan2.md
      \["f", \<currency code as defined in NIP-69 or ISO 4217 used as functional currency of the accounting area; for BTC, SAT something other/similar has to be used\(?\)\>\],
      \["a", "//possible future reference to another event-kind that specifies posting-rules \(e.g. rules for who is allowed to post to which accounts, ...\)"\]
    \],
    "content": \<optional accounting area description/comment\>,
    "sig": \<64-bytes lowercase hex of the signature of the sha256 hash of the serialized event data, which is the same as the "id" field\>
  \}

This note should introduce the "kind" \(37701, "accounting area"\).
