# 2: Put Transactions with XTDB – Pluto Assignment

![XTDB Tutorial: tx/put](https://github.com/xtdb/xtdb-tutorial/raw/main/images/2a-put-tx-pluto-title.png)

# Introduction

This is the second part of the xtdb tutorial. The Earth installment looked at setting up a simple xtdb standalone node and a very simple `put` transaction.

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

# Arrival on Pluto

As you enter the Plutonian atmosphere, a message pops up on your communication panel:

> Welcome to the dwarf planet Pluto. You are entering privately governed space. If you do not have the correct papers, entry will be denied. We hope you enjoy your stay.
>
> Have a nice day.
>
> \- Anarchic Directorate of Pluto

The government of Pluto is asking to see your flight manifest.

## Choose your path:

- **You have your manifest** :
    - *You have permission to land, continue to the space port.*

- **You do not have your manifest** :
    - *You do not have permission to land. You can either return to [Earth](https://nextjournal.com/xtdb-tutorial/getting-started-with-xtdb), or continue at your own risk.*

# Space Port

As you circle the dwarf planet to land, you have a quick read of your xtdb manual. You know you will be using the `put` operation a lot for this assignment and although you used the operation to add your manifest before you left, you think it is a good idea to brush up on your knowledge.


> Currently there are only four transaction operations in xtdb: put, delete, match and evict.
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
> ## Put:
>
> The `put` transaction is used to write versions of a document (doc).
>
> Each document must be in Extensible Data Notation (edn) and must contain a unique :xt/id value. However, beyond those two requirements you have the flexibility to add whatever you like to your documents because xtdb is schemaless.
>
> Along with the document (doc), put has two optional additional arguments:
>> start `valid-time`   *(The time at which the entry will be valid from.)*
>>
>> end `valid-time`     *(The time at which the entry will be valid until.)*
>
> This means that you can query back through the database, you can use valid-time arguments to see the state of the database at a different time.
>
>
> Time in xtdb is denoted `#inst "yyyy-MM-ddThh:mm:ss"`. For example, 9:30 pm on January 2nd 1999 would be written:
>
>>    `#inst "1999-01-02T21:30:00".`
>
>
> A complete put transaction has the form:
>> `[::xt/put doc valid-time-start valid-time-end]`
>
> \- xtdb manual *[Read More](https://xtdb.com/reference/transactions.html)*


You are happy with what you have read, and in anticipation of your first assignment you define the standalone node.

```clojure
(def node (xt/start-node {}))
```

# Assignment

You land on the surface of the dwarf planet. As you do, the job ticket for this assignment is unlocked.

## Ticket

> ### Task
> *Commodity Logging*
>
> ### Company
> *Tombaugh Resources Ltd.*
>
> ### Contact
> *R. Glogofloon*
>
> ### Submitted
> *2115-02-20T13:38:20*
>
> ### Additional information:
> *We need help setting up a new recording system for our mine. I have enclosed a list of the commodities we deal with. Please send someone soon because we already have a week’s worth of unrecorded stocktakes.*

You make your way over to the mines on the next shuttle. On your way you decide to get a head start and put the commodities into xtdb.

```clojure
(xt/submit-tx
 node
 [[::xt/put
   {:xt/id :commodity/Pu
    :common-name "Plutonium"
    :type :element/metal
    :density 19.816
    :radioactive true}]

  [::xt/put
   {:xt/id :commodity/N
    :common-name "Nitrogen"
    :type :element/gas
    :density 1.2506
    :radioactive false}]

  [::xt/put
   {:xt/id :commodity/CH4
    :common-name "Methane"
    :type :molecule/gas
    :density 0.717
    :radioactive false}]])
```

Since it takes six hours for each transaction to reach your xtdb node on Earth from here, it is a good idea to batch up all the commodities in a single transaction.

## Stock Take

You arrive at the mine and are met by the CEO, Reginald Glogofloon, a 150 year old Plutonian.

> Hello, I’m glad you’re here.
>
> I would like you to fill in our last weeks worth of data on our commodities. We need to be able to look back at a given day and see what our stocks were for auditing purposes.
> 
> The stock for each day must be submitted at 6pm Earth time (UTC) for your banks records.
>
> Are you able to do that for me?
>
> \- R. Glogofloon

## Choose your path:

- **"Yes I'll give it a go"** :
    - *Continue to complete the assignment.*

- **"I'm not sure how to even begin"** :
    - *Go back and read the manual entry.*

You remember that with xtdb you have the option of adding a `valid-time`. This comes in useful now as you enter the weeks worth of stock takes for Plutonium.

```clojure
(xt/submit-tx
 node
 [[::xt/put
   {:xt/id :stock/Pu
    :commod :commodity/Pu
    :weight-ton 21 }
   #inst "2115-02-13T18"]

  [::xt/put
   {:xt/id :stock/Pu
    :commod :commodity/Pu
    :weight-ton 23 }
   #inst "2115-02-14T18"]

  [::xt/put
   {:xt/id :stock/Pu
    :commod :commodity/Pu
    :weight-ton 22.2 }
   #inst "2115-02-15T18"]

  [::xt/put
   {:xt/id :stock/Pu
    :commod :commodity/Pu
    :weight-ton 24 }
   #inst "2115-02-18T18"]

  [::xt/put
   {:xt/id :stock/Pu
    :commod :commodity/Pu
    :weight-ton 24.9 }
   #inst "2115-02-19T18"]])
```

You notice that the amount of Nitrogen and Methane has not changed which saves you some time:

```clojure
(xt/submit-tx
 node
 [[::xt/put
   {:xt/id :stock/N
    :commod :commodity/N
    :weight-ton 3 }
   #inst "2115-02-13T18"
   #inst "2115-02-19T18"]

  [::xt/put
   {:xt/id :stock/CH4
    :commod :commodity/CH4
    :weight-ton 92 }
   #inst "2115-02-15T18"
   #inst "2115-02-19T18"]])
```

The CEO is impressed with your speed, but a little skeptical that you have done it properly.

You gain their confidence by showing them the entries for Plutonium on two different days:

```clojure
(xt/entity (xt/db node #inst "2115-02-14") :stock/Pu)
```

```clojure
(xt/entity (xt/db node #inst "2115-02-18") :stock/Pu)
```

## Easy Ingest

As a parting gift to them you create an easy ingest function so that if they needed to add more commodities to their stock list they could do it fast.

```clojure
(defn easy-ingest
  "Uses xtdb put transaction to add a vector of documents to a specified
  node"
  [node docs]
  (xt/submit-tx node
                  (vec (for [doc docs]
                         [::xt/put doc]))))
```

Tombaugh Resources Ltd. are happy that this will be simple enough to use. They thank you for the extra help and you head back to your ship.

# Space Port

You are back at your ship and check your communications panel. There is a new assignment waiting for you:

> Congratulations on completing your first assignment.
>
> We would like you to go to Mercury, the hub of the trade world. Their main trade center has a new IT department and want you to show them how to query xtdb"
>
> \- Helios Banking Inc.

It’s a long flight so you refuel, and update your manifest. You have been awarded a new badge, so you add this to your manifest.

```clojure
(xt/submit-tx
 node
 [[::xt/put
   {:xt/id :manifest
    :pilot-name "Johanna"
    :id/rocket "SB002-sol"
    :id/employee "22910x2"
    :badges ["SETUP" "PUT"]
    :cargo ["stereo" "gold fish" "slippers" "secret note"]}]])
```

You enter the countdown for lift off to Mercury. [See you soon.](https://nextjournal.com/xtdb-tutorial/datalog)

![Mercury: Datalog](https://github.com/xtdb/xtdb-tutorial/raw/main/images/2b-datalog-mercury.png) 