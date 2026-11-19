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

The Lastfm and Yelp templates retain ratings >= 1; Douban retains ratings >= 4.
Keep the original row order: cross-validation assigns retained interaction row `i`
to test fold `i % folds + 1`. Do not pre-shuffle a dataset to reproduce that protocol.

The reference user counts and mean degrees in the configurations reproduce the
source method's injection/budget constants, rather than recomputing statistics
after filtering. For a new dataset, specify these statistics deliberately.

Real datasets are not bundled. Obtain them from their original providers and
apply your documented preprocessing. The synthetic fixture in `examples/toy`
is supplied only to exercise the code and does not represent benchmark results.
