Place the prepared datasets here:

```text
datasets/
  lastfm/ratings.txt
  lastfm/trusts.txt
  yelp/ratings.txt
  yelp/trusts.txt
  douban/ratings.txt
  douban/trusts.txt
```

Ratings have three whitespace-, tab-, or comma-separated columns:
`user_id item_id rating`. Social edges have two columns: `source_user destination_user`.
An optional third social column is accepted and ignored, matching the source configurations' first-two-column selection.
Use nonnegative integer user IDs and unique user/item interaction pairs. No header is expected.
User IDs must have one unambiguous numeric spelling. Item IDs may be arbitrary nonempty strings.
Blank lines and full-line `#` comments are ignored. Retained ratings are converted to 1.
Social edges are directed; their weight is 1, matching the original two-column input.

