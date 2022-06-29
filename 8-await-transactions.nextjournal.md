# 8: Await with XTDB - Kepra-5 Assignment

![Kepra-5: Await](https://github.com/xtdb/xtdb-tutorial/raw/main/images/8a-await-kepra5-title.png)

# Introduction

This is the `await-tx` instalment of the XTDB tutorial.

## Setup

You need to get XTDB running before you can use it.

<!--- Stil want to show the user deps.edn even though it's loaded in the repo. --->
```edn no-exec
{:deps
 {org.clojure/clojure {:mvn/version "1.11.1"}
  org.clojure/tools.deps.alpha {:mvn/version "0.14.1212"}
  com.xtdb/xtdb-core {:mvn/version "dev-SNAPSHOT"}} ;; "RELEASE"

  :mvn/repos
  {"snapshots" {:url "https://s01.oss.sonatype.org/content/repositories/snapshots"}}}
```

```clojure
(require '[xtdb.api :as xt])
```

# Recap

Last time you opted to leave the solar system on the secret spaceship 'Oumuamua on an exciting new adventure.
You have been travelling for 25 years at near light speed to reach the star system Gilese 667C.
This star system is home to intelligent life far superior to our own.
With you on the ship is the captain, Ilex, and your new friend Kaarlang.
You were put into cryostasis so you could safely withstand the long journey.

**We begin again in the year 2140.**

# Awakening

You open your eyes after what feels like only a moment, your 25-year slumber broken by a loud hiss from the opening of the cryogenic pod door.
You take a minute to acknowledge the cold surroundings. You realize the ship engines have fallen still and assume that you are now in geostationary orbit.

Captain Ilex's voice comes over the intercom.

> Good morning passengers.
> And hello new world.
> We've reached the planet Kepra-5 of the Gilese 667C star system.
>
> If you could make your way to the [space elevator](https://en.wikipedia.org/wiki/Space_elevator) which is waiting to take you to the planet surface, where you will need to go through customs and get a new passport.
>
> We would like to wish you all the best in your future travels."
>
> \- Ilex

Finding your way to the space elevator dock, you notice that you feel a lot heavier than usual.
You wonder if this is your body being weaker than usual from being in cryostasis for so long, or if this new planet has stronger gravity.

## Choose your path:


  * **You want to test this, and are interested to know how the gravity of the planet below compares to planets back in your home solar system:**
      * *You see it as a good opportunity to refresh yourself with ingestion and queries.*


  * **You think about testing this, but you're in a rush and want to get your passport as fast as possible:**
      * *Head over to passport control.*

# Gravity comparison

You spin up a new XTDB node and ingest the known data from the solar system.

```clojure
(def node (xt/start-node {}))
```

```clojure
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

(xt/sync node)
```

The elevator arrives and you drag yourself aboard.
As you are carried to the surface, you note the relief as the force of gravity on your body is cancelled by the movement of the lift.

As soon as you reach the surface, you waste no time in taking the gravity reading from your iPhone CM.
The reading is 1.4g - no wonder you feel sluggish.

You want to check against the data of the other planets on your node, to see how the gravity from this planet compares, so you write a function to add the new planetary data and query it against the other planets:

```clojure
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

(xt/sync node)

(sort
 (xt/q
  (xt/db node)
  '{:find [g planet]
    :where [[planet :gravity g]]}))
```

Nice, you see that Kepra-5 has gravitational forces stronger than Neptune but weaker than Jupiter.

Now you’ve satisfied your curiosity, you head over to passport control.

# Passport Control

You find yourself at passport control where you are told your XTDB experience is needed.

Kaarlang has arrived there first and has been chatting to the manager here.
As an advanced civilization, they are quite happily using XTDB with no issues but still have a problem with human error.
Some employees have been handing out passports before putting the traveller's information into XTDB.
When it gets particularly busy, it’s not uncommon for the employees to forget to go back and put the data in, resulting in unregistered travellers.

Kaarlang has told the manager that you have a background in solving problems using XTDB, so has offered them your skills.

Your task is to make a function that ensures no passport is given before the traveller's data is successfully ingested into XTDB.

```clojure
(defn ingest-and-query
  [traveller-doc]
  (xt/submit-tx node [[::xt/put traveller-doc]])
  (xt/q
   (xt/db node)
   '{:find [n]
     :where [[id :passport-number n]]
     :in [id]}
   (:xt/id traveller-doc)))
```

You test out your function.

```clojure
(ingest-and-query
 {:xt/id :origin-planet/test-traveller
  :chosen-name "Test"
  :given-name "Test Traveller"
  :passport-number (java.util.UUID/randomUUID)
  :stamps []
  :penalties []})
```

This strikes you as peculiar - you received no errors from your XTDB node upon submitting, but the ingested traveller doc has not returned a passport number.

You are sure your query and ingest syntax is correct, but to check you try running the query again.
This time you get the expected result:

```clojure
(ingest-and-query
 {:xt/id :origin-planet/test-traveller
  :chosen-name "Test"
  :given-name "Test Traveller"
  :passport-number (java.util.UUID/randomUUID)
  :stamps []
  :penalties []})
```

**The plot thickens.**

Confused, you open your trusty XTDB manual, skimming through until you hit the page on `await-tx`:

> Blocks until the node has indexed a transaction that is at or past the supplied tx.
> Will throw on timeout.
> Returns the most recent tx indexed by the node.
>
> \- XTDB manual *[Read More](https://xtdb.com/reference/transactions.html#await)*

Of course.
Submit operations in XTDB are **asynchronous** - your query did not return the new data as it had not yet been indexed into XTDB.
You decide to rewrite your function using `await-tx`:

```clojure
(defn ingest-and-query
  "Ingests the given traveller's document into XTDB, returns the passport
  number once the transaction is complete."
  [traveller-doc]
  (xt/await-tx node
               (xt/submit-tx node [[::xt/put traveller-doc]]))
  (xt/q
   (xt/db node)
   '{:find [n]
     :where [[id :passport-number n]]
     :in [id]}
   (:xt/id traveller-doc)))
```

You run the function again, Changing the traveller-doc so you can see if it’s worked.
This time you receive the following:

```clojure
(ingest-and-query
 {:xt/id :origin-planet/new-test-traveller
  :chosen-name "Testy"
  :given-name "Test Traveller"
  :passport-number (java.util.UUID/randomUUID)
  :stamps []
  :penalties []})
```

> ## Caution
>
> *XTDB is fundamentally asynchronous: you submit a transaction to the central transaction log - then, later, each individual XTDB node reads the transaction from the log and indexes it.
> If you submit a transaction and then run a query without explicitly waiting for the node to have indexed the transaction - either via `sync` or `await-tx` - you’re not guaranteed that your query will reflect your recent transaction.
> On a small use-case, you might get lucky - but, if you want to reliably read your writes, use `await-tx`.
> If you’re ingesting a large batch of data though, calling `await-tx` after every transaction will slow the process significantly - you only need to await the final transaction to know that all of the preceding transactions are available.*

You show this to the manager at the passport control office.
They are happy that this will work.

> Thank you, this will save us so much time in dealing with missing traveller information.
> As a token of our gratitude, we would like to grant you with free entry to the planet.

You graciously accept.
For the passport, you must provide your origin planet, name and also a chosen name.
Many people come to Kepra-5 to start fresh, so your chosen name can be anything you wish.

Once chosen you can not easily change it, so you think carefully.

```clojure
(ingest-and-query
 {:xt/id :earth/ioelena
  :chosen-name "Ioelena"
  :given-name "Johanna"
  :passport-number (java.util.UUID/randomUUID)
  :stamps []
  :penalties []})
```

New name, new you.

Now that you are in the Gilese 667C you must keep your passport up to date wherever you travel.
You take a note of your passport number and put it somewhere safe.

Through customs, you are now free to explore the exciting new planet.
Taking in your surroundings, you see a hostel nearby.
You head over.
Although you’ve been in cryostasis for such a long time, the new gravity has made you very tired.

# THE END.
