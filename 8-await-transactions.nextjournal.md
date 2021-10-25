# 8: Await with XTDB - Kepra-5 Assignment

![Kepra-5: Await](https://github.com/xtdb/xtdb-tutorial/raw/main/images/8a-await-kepra5-title.png)

# Introduction

This is the `await-tx` installment of the xtdb tutorial.

## Setup

You need to get xtdb running before you can use it.

```edn no-exec id=ffcf0396-b3f9-40e6-a0c2-654401879781
{:deps
 {org.clojure/clojure {:mvn/version "1.10.0"}
  org.clojure/tools.deps.alpha
  {:git/url "https://github.com/clojure/tools.deps.alpha.git"
   :sha "f6c080bd0049211021ea59e516d1785b08302515"}
  com.xtdb/xtdb-core {:mvn/version "dev-SNAPSHOT"}} ;; "RELEASE"

  :mvn/repos
  {"snapshots" {:url "https://s01.oss.sonatype.org/content/repositories/snapshots"}}}
```

```clojure id=35dc65e9-f458-4e32-9a59-1af72cd12a78
(require '[xtdb.api :as xt])
```

# Recap

Last time you opted to leave the solar system on the secret space ship 'Oumuamua on an exciting new adventure. You have been traveling for 25 years at near light speed to reach the star system Gilese 667C. This star system is home to intelligent life far superior to our own. With you on the ship is the captain, Ilex, and your new friend Kaarlang. You were put into cryostasis so you could safely withstand the long journey.

**We begin again in the year 2140.**

# Awakening

You open your eyes after what feels like only a moment, your 25 year slumber broken by a loud hiss from the opening of the cryogenic pod door.

You take a minute to acknowledge the cold surroundings. You realize the ship engines have fallen still and assume that you are now in geostationary orbit.

Captain Ilex's voice comes over the intercom.

```clojure no-exec id=223ddbe8-8eed-4e69-ae1c-57f484971dcb
"Good morning passengers.
And hello new world.
We've reached the planet Kepra-5 of the Gilese 667C star system.

If you could make your way to the https://en.wikipedia.org/wiki/Space_elevator[space elevator] which is waiting to take you to the planet surface, where you will need to go through customs and get a new passport.

We would like to wish you all the best in your future travels."

— Ilex
```

Finding your way to the space elevator dock, you notice that you feel a lot heavier than usual. You wonder if this is your body being weaker than usual from being in cryostasis for so long, or if this new planet has stronger gravity.

## Choose your path:

**You want to test this, and are interested to know how the gravity of the planet below compares to planets back in your home solar system:** *You see it as a good opportunity to refresh yourself with ingestion and queries.*

**You think about testing this, but you're in a rush and want to get your passport as fast as possible:** *Head over to passport control.*

# Gravity comparison

You spin up a new xtdb node and ingest the known data from the solar system.

```clojure id=2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153
(def node (xt/start-node {}))
```

```clojure id=1ffd8986-b9d6-468c-b533-00d7bc13f67c
(def stats
  [{:body "Sun"
    :type "Star"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 109.3
    :volume 1305700
    :mass 33000
    :gravity 27.9
    :xt/id :Sun}
   {:body "Jupiter"
    :type "Gas Giant"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 10.97
    :volume 1321
    :mass 317.83
    :gravity 2.52
    :xt/id :Jupiter}
   {:body "Saturn"
    :type "Gas Giant"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius :volume
    :mass :gravity
    :xt/id :Saturn}
   {:body "Saturn"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 9.14
    :volume 764
    :mass 95.162
    :gravity 1.065
    :type "planet"
    :xt/id :Saturn}
   {:body "Uranus"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 3.981
    :volume 63.1
    :mass 14.536
    :gravity 0.886
    :type "planet"
    :xt/id :Uranus}
   {:body "Neptune"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 3.865
    :volume 57.7
    :mass 17.147
    :gravity 1.137
    :type "planet"
    :xt/id :Neptune}
   {:body "Earth"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 1
    :volume 1
    :mass 1
    :gravity 1
    :type "planet"
    :xt/id :Earth}
   {:body "Venus"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 0.9499
    :volume 0.857
    :mass 0.815
    :gravity 0.905
    :type "planet"
    :xt/id :Venus}
   {:body "Mars"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 0.532
    :volume 0.151
    :mass 0.107
    :gravity 0.379
    :type "planet"
    :xt/id :Mars}
   {:body "Ganymede"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 0.4135
    :volume 0.0704
    :mass 0.0248
    :gravity 0.146
    :type "moon"
    :xt/id :Ganymede}
   {:body "Titan"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 0.4037
    :volume 0.0658
    :mass 0.0225
    :gravity 0.138
    :type "moon"
    :xt/id :Titan}
   {:body "Mercury"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 0.3829
    :volume 0.0562
    :mass 0.0553
    :gravity 0.377
    :type "planet"
    :xt/id :Mercury}])

(xt/submit-tx node (mapv (fn [stat] [::xt/put stat]) stats))
```

The elevator arrives and you drag yourself aboard. As you are carried to the surface, you note the relief as the force of gravity on your body is canceled by the movement of the lift.

As soon as you reach the surface, you waste no time in taking the gravity reading from your iPhone CM. The reading is 1.4g - no wonder you feel sluggish.

You want to check against the data of the other planets on your node, to see how the gravity from this planet compares, so you write a function to add the new planetary data and query it against the other planets:

```clojure id=950de198-0847-4b3b-bd24-1d1300a30158
(xt/submit-tx
 node
 [[::xt/put
   {:body "Kepra-5"
    :units {:radius "Earth Radius"
            :volume "Earth Volume"
            :mass "Earth Mass"
            :gravity "Standard gravity (g)"}
    :radius 0.6729
    :volume 0.4562
    :mass 0.5653
    :gravity 1.4
    :type "planet"
    :xt/id :Kepra-5}]])

(sort
 (xt/q
  (xt/db node)
  '{:find [g planet]
    :where [[planet :gravity g]]}))
```

Nice, you see that Kepra-5 has gravitational forces stronger than Neptune but weaker than Jupiter.

Now you’ve satisfied your curiosity, you head over to passport control.

# Passport Control

You find yourself at passport control where you are told your xtdb experience is needed.

Kaarlang has arrived there first and has been chatting to the manager here. As an advanced civilization, they are quite happily using xtdb with no issues, but still have a problem with human error. Some employees have been handing out passports before putting the travelers information into xtdb. When it gets particularly busy, it’s not uncommon for the employees to forget to go back and put the data in, resulting in unregistered travelers.

Kaarlang has told the manager that you have a background in solving problems using xtdb, so has offered them your skills.

Your task is to make a function that ensures no passport is given before the travelers data is successfully ingested into xtdb.

```clojure id=99b0dd9c-d5cb-4c34-8a77-d71f941e97cd
(defn ingest-and-query
  [traveler-doc]
  (xt/submit-tx node [[::xt/put traveler-doc]])
  (xt/q
   (xt/db node)
   '{:find [n]
     :where [[e :xt/id id]
             [e :passport-number n]]
     :in [id]}
   (:xt/id traveler-doc)))
```

You test out your function.

```clojure id=8a4d7bc7-69f2-47c3-878c-8d60e6986674
(ingest-and-query
 {:xt/id :origin-planet/test-traveler
  :chosen-name "Test"
  :given-name "Test Traveler"
  :passport-number (java.util.UUID/randomUUID)
  :stamps []
  :penalties []})
```

This strikes you as peculiar - you received no errors from your xtdb node upon submitting, but the ingested traveler doc has not returned a passport number.

You are sure your query and ingest syntax is correct, but to check you try running the query again. This time you get the expected result:

```clojure id=392b9eb4-0a7b-4fb3-953d-b0f15cf8c758
(ingest-and-query
 {:xt/id :origin-planet/test-traveler
  :chosen-name "Test"
  :given-name "Test Traveler"
  :passport-number (java.util.UUID/randomUUID)
  :stamps []
  :penalties []})
```

**The plot thickens.**

Confused, you open your trusty xtdb manual, skimming through until you hit the page on `await-tx`:

```custom no-exec id=4ec8e24c-39cf-4080-be97-3de19d78af04
Blocks until the node has indexed a transaction that is at or past the supplied tx. Will throw on timeout. Returns the most recent tx indexed by the node.

- xtdb manual
```

*[Read More](https://xtdb.com/reference/transactions.html#await)*

Of course. Submit operations in xtdb are **asynchronous** - your query did not return the new data as it had not yet been indexed into xtdb. You decide to rewrite your function using `await-tx`:

```clojure id=b22751f2-2aa9-4b26-9a76-08807e4ac6af
(defn ingest-and-query
  "Ingests the given travelers document into xtdb, returns the passport
  number once the transaction is complete."
  [traveler-doc]
  (xt/await-tx node
               (xt/submit-tx node [[::xt/put traveler-doc]]))
  (xt/q
   (xt/db node)
   '{:find [n]
     :where [[e :xt/id id]
             [e :passport-number n]]
     :in [id]}
   (:xt/id traveler-doc)))
```

You run the function again, Changing the traveler-doc so you can see if it’s worked. This time you receive the following:

```clojure id=563c4aa6-5fbd-46c2-b959-18aa6bba7d90
(ingest-and-query
 {:xt/id :origin-planet/new-test-traveler
  :chosen-name "Testy"
  :given-name "Test Traveler"
  :passport-number (java.util.UUID/randomUUID)
  :stamps []
  :penalties []})
```

## Caution

*XTDB is fundamentally asynchronous: you submit a transaction to the central transaction log - then, later, each individual xtdb node reads the transaction from the log and indexes it. If you submit a transaction and then run a query without explicitly waiting for the node to have indexed the transaction, you’re not guaranteed that your query will reflect your recent transaction. On a small use-case, you might get lucky - but, if you want to reliably read your writes, use `await-tx`. If you’re ingesting a large batch of data though, calling `await-tx` after every transaction will slow the process significantly - you only need to await the final transaction to know that all of the preceding transactions are available.*

You show this to the manager at the passport control office. They are happy that this will work.

```clojure id=3109132e-f436-437d-976f-b49857a0bdaa
"Thank you, this will save us so much time in dealing with missing traveler information. As a token of our gratitude, we would like to grant you with free entry to the planet."
```

You graciously accept. For the passport you must provide your origin planet, name and also a chosen name. Many people come to Kepra-5 in order to start fresh, so your chosen name can be anything you wish.

Once chosen you can not easily change it, so you think carefully.

```clojure id=80d2cc45-e470-483d-b7bc-53ea1e6bb7c7
(ingest-and-query
 {:xt/id :earth/ioelena
  :chosen-name "Ioelena"
  :given-name "Johanna"
  :passport-number (java.util.UUID/randomUUID)
  :stamps []
  :penalties []})
```

New name, new you.

Now that you are in the Gilese 667C you must keep your passport up to date wherever you travel. You take a note of your passport number and put it somewhere safe.

Through customs, you are now free to explore the exciting new planet. Taking in your surroundings, you see a hostel nearby. You head over. Although you’ve been in cryostasis for such a long time, the new gravity has made you very tired.

# THE END.

<details id="com.nextjournal.article">
<summary>This notebook was exported from <a href="https://nextjournal.com/a/MPrTfmoyFHctxUufCNRJV?change-id=CwhbKgNeHLxGW2DSrNhzvN">https://nextjournal.com/a/MPrTfmoyFHctxUufCNRJV?change-id=CwhbKgNeHLxGW2DSrNhzvN</a></summary>

```edn nextjournal-metadata
{:article
 {:settings nil,
  :nodes
  {"1ffd8986-b9d6-468c-b533-00d7bc13f67c"
   {:compute-ref #uuid "5571bedd-68ce-4e3a-bedb-56d461f1b52a",
    :exec-duration 311,
    :id "1ffd8986-b9d6-468c-b533-00d7bc13f67c",
    :kind "code",
    :output-log-lines {},
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "223ddbe8-8eed-4e69-ae1c-57f484971dcb"
   {:id "223ddbe8-8eed-4e69-ae1c-57f484971dcb",
    :kind "code-listing",
    :name "Ship Captain"},
   "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153"
   {:compute-ref #uuid "eaa5a883-a18a-4aea-bc8a-ce4646de0264",
    :exec-duration 6130,
    :id "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "3109132e-f436-437d-976f-b49857a0bdaa"
   {:compute-ref #uuid "8ddd3f91-10a5-477e-bbab-a5c64b98954b",
    :exec-duration 71,
    :id "3109132e-f436-437d-976f-b49857a0bdaa",
    :kind "code",
    :name "Passport control manager ",
    :output-log-lines {},
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "35dc65e9-f458-4e32-9a59-1af72cd12a78"
   {:compute-ref #uuid "b15fc45a-d8a2-44e5-8386-00363f367b16",
    :exec-duration 12442,
    :id "35dc65e9-f458-4e32-9a59-1af72cd12a78",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "392b9eb4-0a7b-4fb3-953d-b0f15cf8c758"
   {:compute-ref #uuid "cfd10182-a90f-48e4-88e1-0d3fd88f8403",
    :exec-duration 130,
    :id "392b9eb4-0a7b-4fb3-953d-b0f15cf8c758",
    :kind "code",
    :output-log-lines {},
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "4ec8e24c-39cf-4080-be97-3de19d78af04"
   {:custom-language "txt",
    :id "4ec8e24c-39cf-4080-be97-3de19d78af04",
    :kind "code-listing",
    :name "xtdb Manual"},
   "563c4aa6-5fbd-46c2-b959-18aa6bba7d90"
   {:compute-ref #uuid "ebbb9f56-2f15-46e3-be3e-7cd62f82bcf0",
    :exec-duration 107,
    :id "563c4aa6-5fbd-46c2-b959-18aa6bba7d90",
    :kind "code",
    :output-log-lines {},
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "6e1e3414-7ad4-42fa-ace0-6939985e69e2"
   {:id "6e1e3414-7ad4-42fa-ace0-6939985e69e2",
    :kind "file",
    :layout :normal},
   "80403b0a-1226-48ff-9bcc-624ed02e3635"
   {:environment
    [:environment
     {:article/nextjournal.id
      #uuid "5b45eb52-bad4-413d-9d7f-b2b573a25322",
      :change/nextjournal.id
      #uuid "5cd52af1-7a79-4804-a169-d6ffcdb6eb7a",
      :node/id "0ae15688-6f6a-40e2-a4fa-52d81371f733"}],
    :id "80403b0a-1226-48ff-9bcc-624ed02e3635",
    :kind "runtime",
    :language "clojure",
    :type :nextjournal,
    :runtime/mounts
    [{:src [:node "ffcf0396-b3f9-40e6-a0c2-654401879781"],
      :dest "/deps.edn"}]},
   "80d2cc45-e470-483d-b7bc-53ea1e6bb7c7"
   {:compute-ref #uuid "c59878c7-96fd-462e-909c-39f6bdd31e82",
    :exec-duration 115,
    :id "80d2cc45-e470-483d-b7bc-53ea1e6bb7c7",
    :kind "code",
    :output-log-lines {},
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "8a4d7bc7-69f2-47c3-878c-8d60e6986674"
   {:compute-ref #uuid "101fde2b-5ca3-4a0d-bc27-5256fc731661",
    :exec-duration 385,
    :id "8a4d7bc7-69f2-47c3-878c-8d60e6986674",
    :kind "code",
    :output-log-lines {},
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "950de198-0847-4b3b-bd24-1d1300a30158"
   {:compute-ref #uuid "a26b3bdf-1b06-4d1a-a37f-fa9f0cdc0ced",
    :exec-duration 393,
    :id "950de198-0847-4b3b-bd24-1d1300a30158",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "99b0dd9c-d5cb-4c34-8a77-d71f941e97cd"
   {:compute-ref #uuid "12cf0b81-ebfc-4f4c-8b42-a262956455ff",
    :exec-duration 116,
    :id "99b0dd9c-d5cb-4c34-8a77-d71f941e97cd",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "b22751f2-2aa9-4b26-9a76-08807e4ac6af"
   {:compute-ref #uuid "4b6ef175-809e-4111-9bc2-8b3d322e5011",
    :exec-duration 100,
    :id "b22751f2-2aa9-4b26-9a76-08807e4ac6af",
    :kind "code",
    :output-log-lines {},
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "ffcf0396-b3f9-40e6-a0c2-654401879781"
   {:id "ffcf0396-b3f9-40e6-a0c2-654401879781",
    :kind "code-listing",
    :name "deps.edn"}},
  :nextjournal/id #uuid "02d8f57b-2d5c-4528-b5cc-869f4d531b8e",
  :article/change
  {:nextjournal/id #uuid "60b7b453-96b5-4647-950f-652deb72d68b"}}}

```
</details>
