This is a multi-module Maven project that contains the source code for a movie rating and
recommendation service, MovieAdvisor.

Planned features:

- Top-N recommended movies
- Predicted rating for a single movie
- Explanation of why we recommend a movie?

TODOs:
- normalize ratings by item mean or user mean?
- what to use for M?  (M = number of item similarities to keep for each item)
- what to use for K?  (K = number of items similar to the item in question that a user has rated to
  try to predict rating for item in question)
- Note that M should be much greater than K (need to balance memory with accuracy)
- Should be bulk-loading into HFiles (or something else for Cassandra)
- Use typed pipes everywhere in Express code and use case classes for pipe types
- Possibly implement some batch recommenders in Java to show how to use KijiMR and KijiSchema
- Calculate global best movies to use as default recommendations?
- Set up a model repository and scoring server?
- Test for importing ratings!

Integration with what Amit wrote for SOLR:
- Eventually want to use the MovieLens 10M dataset
- Only additional information in that dataset is tags
- The input file format also changes, so we'll need different bulk importers

- Address cold start problem by seeding each new user with the globally most-popular movies?
- Or run the scorer in batch to provide initial recommendations for each user?

Show users why they were recommended a particular movie
- During scoring, keep track of the movies whose weight contributed to a given recommendation
- Then add that to the recommendation Avro object

