# 4: Bitemporality with XTDB – Neptune Assignment

![Neptune: Bitemporality](https://github.com/xtdb/xtdb-tutorial/raw/main/images/4a-bitemporality-neptune-title.png)

# Introduction

This is the bitemporal instalment of the xtdb "choose your own adventure" tutorial series.

## Setup

You need to get xtdb running before you can use it.

<!--- Stil want to show the user deps.edn even though it's loaded in the repo. --->
```edn no-exec
{:deps
 {org.clojure/clojure {:mvn/version "1.10.0"}
  org.clojure/tools.deps.alpha
  {:git/url "https://github.com/clojure/tools.deps.alpha.git"
   :sha "f6c080bd0049211021ea59e516d1785b08302515"}
  com.xtdb/xtdb-core {:mvn/version "dev-SNAPSHOT"}} ;; "RELEASE"

  :mvn/repos
  {"snapshots" {:url "https://s01.oss.sonatype.org/content/repositories/snapshots"}}}
```

```clojure
(require '[xtdb.api :as xt])
```

# Arrival on Neptune

You enter the Neptunian atmosphere and your communications panel lights up.

> It is our honour to welcome you to the planet, Neptune.
>
> If you are visiting for business reasons, please present your manifest.
>
> Otherwise, have your visa ready for inspection.
>
> \- Poseidon Republic of Wealth


The government is asking to see your flight manifest.

## Choose your path:

- **You have your manifest**: 
    - *You have permission to land, continue to the spaceport.*

- **You do not have your manifest**: 
    - *You do not have permission to land. You can either return to [Mercury](https://nextjournal.com/xtdb-tutorial/datalog-queries) or continue at your own risk.*

# Spaceport

On your way down to the landing site, you take the time to read the xtdb manual.

> One or more documents can be inserted into xtdb via a put transaction at a specific valid-time.
> The valid-time can be any time (past, future or present).
>
> If no valid-time is provided, xtdb will default to the transaction time, i.e.
> the present.
> Each document survives until it is deleted or a new version of it is added.
>
> \- xtdb manual *[Read More](https://xtdb.com/articles/bitemporality.html)*

You are happy with what you have read, and in anticipation of the assignment, you define the standalone node.

```clojure
(def node (xt/start-node {}))
```

# Assignment

Upon landing on the ice giant, your communications panel lights up indicating that the job ticket is available.

## Ticket


> ### Task
> *Back fill insurance documents*
>
> ### Company
> *Coast Insurance*
>
> ### Contact
> *Lyndon Mercia-York*
>
> ### Submitted
> *2115-02-22T13:38:20*
>
> ### Additional information:
> *We have lost a lot of our records in a flood.
> I think it is prudent to start storing our data digitally.
> It is important to track policy holders' level of cover at the time of the incident.
> We have read a lot about the conveniences of xtdb's bitemporality in this situation.
> We need you to help us get up and running.
> Please send someone quickly though - the waters are rising.*

Outside your ship, you are met by a panicked looking Lyndon.

> Thank goodness you’re here.
>
> We need you to show us how to put our customers' information into xtdb in order of time.
> Working with insurance claims, we should be able to easily look back in time at what type of coverage the customer had at the time of the incident.
>
> Are you able to help us?
>
> \- Lyndon Mercia-York


## Choose your path:

  * **"Yes, I'll give it a go."**:
    * *Continue to complete the assignment.*
    
  * **"I'm not even sure how to begin"**:
    * *Take some time to read through the xtdb manual again. If you're still unsure then you can follow along anyway and see if things become clear.*

## Assignment

Lyndon gives you some data for a client that you can use as an example.
Coast Insurance need to know what kind of cover each customer has and if it was valid at a given time.

You show them how to ingest a document using a `valid-time` so that the information is backdated to when the customer took the cover out.

```clojure
(xt/submit-tx
 node
 [[::xt/put
   {:xt/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? true
    :cover-type :Full}
   #inst "2114-12-03"]])
```

The company needs to know the history of insurance for each cover.
You show them how to use the bitemporality of xtdb to do this.

```clojure
(xt/submit-tx
 node
 [[::xt/put	;; (1)
   {:xt/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? true
    :cover-type :Full}
   #inst "2113-12-03" ;; Valid time start
   #inst "2114-12-03"] ;; Valid time end

  [::xt/put  ;; (2)
   {:xt/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? true
    :cover-type :Full}
   #inst "2112-12-03"
   #inst "2113-12-03"]

  [::xt/put	;; (3)
   {:xt/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? false}
   #inst "2112-06-03"
   #inst "2112-12-02"]

  [::xt/put ;; (4)
   {:xt/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? true
    :cover-type :Promotional}
   #inst "2111-06-03"
   #inst "2112-06-03"]])
```

1. This is the insurance that the customer had last year. Along with the start `valid-time` you use an end `valid-time` so as not to affect the most recent version of the document.
2. This is the previous insurance plan. Again, you use a start and end `valid-time`.
3. There was a period when the customer was not covered,
4. and before that the customer was on a promotional plan.

## Queries through time

You now show them a few queries.
You know that you can query xtdb as of a given `valid-time`.
This shows the state of xtdb at that time.

First you chose a date that the customer had full cover:

```clojure
(xt/q (xt/db node #inst "2114-01-01")
        '{:find [cover type]
          :where [[e :consumer-id :RJ29sUU]
                  [e :cover? cover]
                  [e :cover-type type]]})
```

Next you show them a query for a the customer in a time when they had a different type of cover:

```clojure
(xt/q (xt/db node #inst "2111-07-03")
        '{:find [cover type]
          :where [[e :consumer-id :RJ29sUU]
                  [e :cover? cover]
                  [e :cover-type type]]})
```

And finally you show them a time when the customer had no cover at all.

```clojure
(xt/q (xt/db node #inst "2112-07-03")
        '{:find [cover type]
          :where [[e :consumer-id :RJ29sUU]
                  [e :cover? cover]
                  [e :cover-type type]]})
```

Confident in their ability to put the remainder of their records into xtdb, Lyndon thanks you.

> I can’t believe we’ve not digitized sooner.
> There was a huge push to start using more paper as the Neptune tree population was getting out of control from the accelerated terraforming, but since all these floods I’m not sure paper was the right choice."
> 
> \- Lyndon Mercia-York

You say goodbye to Lyndon and head back to the spaceport.

# Spaceport

Back at your spaceship, you check your communications panel.
There is a new assignment waiting for you.

> We have assigned you a quick task on Saturn helping a small company who are having some problems keeping their records in order.
>
> This shouldn’t take long, but don’t forget they will still need to see your manifest.
>
> — Helios Banking Inc.

You add the new badge to your manifest

```clojure
(xt/submit-tx
 node
 [[::xt/put
   {:xt/id :manifest
    :pilot-name "Johanna"
    :id/rocket "SB002-sol"
    :id/employee "22910x2"
    :badges ["SETUP" "PUT" "DATALOG-QUERIES" "BITEMP"]
    :cargo ["stereo" "gold fish" "slippers" "secret note"]}]])
```

Countdown to liftoff to the ringed plant. [See you soon.](https://nextjournal.com/xtdb-tutorial/match)

![Saturn: Match](https://github.com/xtdb/xtdb-tutorial/raw/main/images/4b-match-saturn.png)
