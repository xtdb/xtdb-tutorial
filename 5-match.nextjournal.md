# 5: Match with XTDB – Saturn Assignment

![Saturn: Match Transactions](https://github.com/xtdb/xtdb-tutorial/raw/main/images/5a-match-saturn-title.png)

# Introduction

This is the `match` instalment of the XTDB tutorial.

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

# Arrival on Saturn

As you pass through the innermost ring of Saturn, A warning light appears on your communications panel.
You open the message.
It’s from Space Customs and reads:

> We extend you the warmest welcome.
>
> We must check papers before we can give you permission to land.
>
> \- Cronus Peaceful Nations


They are asking to see your flight manifest.

## Choose your path:


  * **You have your manifest**:
      * *You have permission to land, continue to the spaceport.*


  * **You do not have your manifest**:
      * *You do not have permission to land. You can either return to [Neptune](https://nextjournal.com/xtdb-tutorial/bitemporality) or continue at your own risk.*

# Spaceport

As you prepare to land you open your XTDB manual to the page on `match`

> Currently there are only four primitve transaction operations in XTDB: put, delete, match and evict.
>
>> **Transaction**    **(Description)**
>>
>> put                (Writes a version of a document)
>>
>> delete           (Deletes a version of a document)
>>
>> match           (Stops a transaction if the precondition is not met.)
>>
>> evict             (Removes a document entirely)
>
> ## match:
>
> match checks the current state of an entity - if the entity doesn’t match the provided doc, the transaction will not continue.
> You can also pass nil to check that the entity doesn’t exist prior to your transaction.
>
> A match transaction takes the entity id, along with an expected document.
> Optionally you can provide a valid time.
>
> Time in XTDB is denoted #inst 'yyyy-MM-ddThh:mm:ss'.
> For example, 9:30 pm on January 2nd 1999 would be written: `#inst "1999-01-02T21:30:00"`.
>
> A complete match transaction has the form:
>
> `[::xt/match entity-id expected-doc valid-time]`
>
> Note that if there is no old-doc in the system, you can provide `nil` in its place."
>
> \- XTDB manual *[Read More](https://xtdb.com/reference/transactions.html#match)*

You are happy with what you have read, and in anticipation of the assignment, you define the standalone node.

```clojure
(def node (xt/start-node {}))
```

# Assignment

As you land on the surface of Saturn the job ticket for this assignment is unlocked.

## Ticket

> ### Task
> *Secure trading system*
>
> ### Company
> *Cronus Market Technologies*
>
> ### Contact
> *Ubuku Eppimami*
>
> ### Submitted
> *2115-02-23T13:38:20*
>
> ### Additional information:
> *We need to be shown how to ensure no trades are done without the buyer and seller having the expected funds or stock respectively*

The next shuttle to the CMT office leaves in 5 Earth minutes.
While you wait you use the easy ingest function you created on Pluto to put the example data into your system.

```clojure
(defn easy-ingest
  "Uses XTDB put transaction to add a vector of
  documents to a specified node"
  [node docs]
  (xt/submit-tx node
                  (vec (for [doc docs]
                         [::xt/put doc])))
  (xt/sync node))

(def data
  [{:xt/id :gold-harmony
    :company-name "Gold Harmony"
    :seller? true
    :buyer? false
    :units/Au 10211
    :credits 51}

   {:xt/id :tombaugh-resources
    :company-name "Tombaugh Resources Ltd."
    :seller? true
    :buyer? false
    :units/Pu 50
    :units/N 3
    :units/CH4 92
    :credits 51}

   {:xt/id :encompass-trade
    :company-name "Encompass Trade"
    :seller? true
    :buyer? true
    :units/Au 10
    :units/Pu 5
    :units/CH4 211
    :credits 1002}

   {:xt/id :blue-energy
    :seller? false
    :buyer? true
    :company-name "Blue Energy"
    :credits 1000}])

(easy-ingest node data)
```

You also decide to make some Clojure functions so you can easily show Ubuku the stock and fund levels after the trades.

```clojure
(defn stock-check
  [company-id item]
  {:result (xt/q (xt/db node)
                 {:find '[name funds stock]
                  :where ['[e :company-name name]
                          '[e :credits funds]
                          ['e item 'stock]]
                  :in '[e]}
                 company-id)
   :item item})

(defn format-stock-check
  [{:keys [result item] :as stock-check}]
  (for [[name funds commod] result]
    (str "Name: " name ", Funds: " funds ", " item " " commod)))
```

Just as you are finishing off your shuttle arrives.

After a short journey through the icy lower clouds of Saturn, you are met by a friendly-faced Ubuku.

> Hello, friend.
>
> We have been using XTDB for a short time now and think it is great.
> The problem is a human one.
> Occasionally we process trades without first confirming the state of the funds in the buyer's account. This means that any trades happening at approximately the same time will not observe each other's effects on the current balance, and therefore the final state of the account will not reflect all the trades correctly.
>
> I know there is a way that we can stop this happening in XTDB.
>
> I sent you some example data in the job ticket for you to use, I trust you found it.
>
> Do you think you can help us?
>
> \- Ubuku Eppimami


## Choose your path:


  * **"Yes, I'll give it a go."**:
      * *Continue to complete the assignment.*


  * **"I'm not even sure how to begin"**:
      * *Take some time to read through the XTDB manual again. If you're still unsure then you can follow along anyway and see if things become clear.*

## Assignment

You explain to Ubuku that all they need to do to solve their problem is to use the `match` operation instead of `put` when they are processing their trades.

You show Ubuku the `match` operation for a valid transaction.
You move 10 units of Methane (`:units/CH4`) each at the cost of 100 credits to Blue Energy:

```clojure
(xt/submit-tx
 node
 [[::xt/match
   :blue-energy
   {:xt/id :blue-energy
    :seller? false
    :buyer? true
    :company-name "Blue Energy"
    :credits 1000}]
  [::xt/put
   {:xt/id :blue-energy
    :seller? false
    :buyer? true
    :company-name "Blue Energy"
    :credits 900
    :units/CH4 10}]

  [::xt/match
   :tombaugh-resources
   {:xt/id :tombaugh-resources
    :company-name "Tombaugh Resources Ltd."
    :seller? true
    :buyer? false
    :units/Pu 50
    :units/N 3
    :units/CH4 92
    :credits 51}]
  [::xt/put
   {:xt/id :tombaugh-resources
    :company-name "Tombaugh Resources Ltd."
    :seller? true
    :buyer? false
    :units/Pu 50
    :units/N 3
    :units/CH4 82
    :credits 151}]])

(xt/sync node)
```

You explain that because the old doc is as expected for both the buyer and the seller that the transaction goes through.

You show Ubuku the result of the trade using the function you created earlier:

```clojure
(format-stock-check (stock-check :tombaugh-resources :units/CH4))
```

```clojure
(format-stock-check (stock-check :blue-energy :units/CH4))
```

They are happy that this works as he sees the 100 credits move from Blue energy to Tombaugh Resources Ltd.
and 10 units of Methane the other way.

Ubuku asks if you can show them what would happen if the state of funds in the account of a buyer did not match expectations.
You show him a trade where the old doc is not as expected for Encompass trade, to buy 10,000 units of Gold from Gold Harmony.

```clojure
(xt/submit-tx
 node
 [[::xt/match
   :gold-harmony
   {:xt/id :gold-harmony
    :company-name "Gold Harmony"
    :seller? true
    :buyer? false
    :units/Au 10211
    :credits 51}]
  [::xt/put
   {:xt/id :gold-harmony
    :company-name "Gold Harmony"
    :seller? true
    :buyer? false
    :units/Au 211
    :credits 51}]

  [::xt/match
   :encompass-trade
   {:xt/id :encompass-trade
    :company-name "Encompass Trade"
    :seller? true
    :buyer? true
    :units/Au 10
    :units/Pu 5
    :units/CH4 211
    :credits 100002}]
  [::xt/put
   {:xt/id :encompass-trade
    :company-name "Encompass Trade"
    :seller? true
    :buyer? true
    :units/Au 10010
    :units/Pu 5
    :units/CH4 211
    :credits 1002}]])

(xt/sync node)
```

```clojure
(format-stock-check (stock-check :gold-harmony :units/Au))
```

```clojure
(format-stock-check (stock-check :encompass-trade :units/Au))
```

You explain to Ubuku that this time because you have both `match` operations in the same transaction, the trade does not go through.
The accounts remain the same, even though the failing `match` was the second operation.

Ubuku thanks you.
This is just what they are looking for.
You head back to the space station to see if there is another assignment waiting for you.

# Spaceport

Back at the spaceship, there is a light waiting for you on your communications panel.

> Well done, you’ve had a productive week.
> We have one final task for you to do before you finish for the week
>
> You need to go to Jupiter and meet Kaarlang, it’s his last day working for us and he needs to delete his trade clients from his personal XTDB node for data protection.
>
> \- Helios Banking Inc.


You update your manifest with your most recent badge.

```clojure
(xt/submit-tx
 node
 [[::xt/put
   {:xt/id :manifest
    :pilot-name "Johanna"
    :id/rocket "SB002-sol"
    :id/employee "22910x2"
    :badges ["SETUP" "PUT" "DATALOG-QUERIES" "BITEMP" "MATCH"]
    :cargo ["stereo" "gold fish" "slippers" "secret note"]}]])

(xt/sync node)
```

As you do so, you check to see if you still have the note that the porter gave you for Kaarlang back on Earth.

```clojure
(xt/q (xt/db node)
      '{:find [belongings]
        :where [[e :cargo belongings]]
        :in [belongings]}
      "secret note")
```

Feeling a bit apprehensive, you enter countdown for liftoff to Jupiter.
[See you soon.](https://nextjournal.com/xtdb-tutorial/delete)

![Jupiter: Delete](https://github.com/xtdb/xtdb-tutorial/raw/main/images/5b-delete-jupiter.png)
