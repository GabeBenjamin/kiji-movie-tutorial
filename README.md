This is a multi-module Maven project that contains the source code for a movie rating and
recommendation service, MovieAdvisor.

How to run
==========

Start by building all of the submodules in Maven:
```
cd kiji-movie-tutorial
mvn clean install
```
This should build and install JARs for the following:

- Table layouts (useful to be in a JAR so that they can be on the classpath for testing)
- Avro records for movie ratings, recommendations, etc.
- Express jobs for bulk loading data and computing item similarities
- Score function for calculating top-N next movies for a given user

Next, create a temp directory to run everything and copy your Bento Box tarball there.  Then run the
python script `workflow/manager.py`:
```
python3 workflow/manager.py \
  --movie-advisor-home=../kiji-movie-tutorial \
  install-bento \
  import-user-info \
  create-tables \
  import-ratings \
  import-movie-info \
  train-item-item-cf \
  register-freshener \
  --kill-bento
```

This script will do the following:

- Kill any existing Bento process
- Untar the Bento Box
- Start the Bento Box
- Create the users and movies tables
- Import movie ratings, movie metadata, and user metadata
- Calculate item-item similarity
- Register the score function

> Alternatively, if you already have bento installed you can then run the python script using this and 
> replacing `{BENTO_HOME}` with the location of where your bento is installed:
> ```
> python3 workflow/manager.py \
>   --movie-advisor-home=../kiji-movie-tutorial \
>   --bento-home={BENTO_HOME}
>   import-user-info \
>   create-tables \
>   import-ratings \
>   import-movie-info \
>   train-item-item-cf \
>   register-freshener \
>   --kill-bento
> ```

Now you are ready to start the web app!  To do so, you need to have Clojure and its build system,
Leiningen, installed.  If you are on a Mac, you can do `brew install leiningen` (That will
install Clojure and Leiningen).  Now you can start the website:
```
cd web-app
lein ring server
```
Running this command should automatically open up the site in your web browser.

Enjoy!




Random other notes:
===================

Planned features:

- Top-N recommended movies
- Predicted rating for a single movie
- Explanation of why we recommend a movie?

TODOs
-----

### Back-end code

- Should be bulk-loading into HFiles (or something else for Cassandra)
- Use typed pipes everywhere in Express code and use case classes for pipe types
  - Fix this once we have typed pipes in express
- Possibly implement some batch recommenders in Java to show how to use KijiMR and KijiSchema
- Set up a model repository and scoring server?
  - Likely not right now, these are too early
- Run the scorer in batch to provide initial recommendations for each user?


### Front-end code

- Need a way to create a new user
- Organize routes into different name spaces
- Refactor out code that talks to Kiji as much as possible
- Unit tests!!!!!!!
  - Not sure how to do this with Kiji
    - Can include KijiSchema test JAR and use KijiClientTest (also use separate profile for testing
      in leiningen)
    - Can also use the Clojure `with-redefs` macro to redefine the Kiji interface to do reasonable
      things
- Need to close all of the various table readers and writers after testing


### Recommendation stuff

- Calculate global best movies to use as default recommendations?
- Address cold start problem by seeding each new user with the globally most-popular movies?
- Show users why they were recommended a particular movie
  - Add field for "why" to MovieRecommendation Avro record
  - During scoring, keep track of the movies whose weight contributed to a given recommendation
  - Then add the most significant contributors to the Avro record


### Solr integration

- Personalized search
  - Figure out what genres a given user likes the most
  - Weight search terms by genres
  - Work with MovieLens 10M dataset to use tags



