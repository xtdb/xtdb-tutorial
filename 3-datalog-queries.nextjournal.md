# 3: Datalog Queries with XTDB – Mercury Assignment

![Mercury: Datalog Queries](https://github.com/xtdb/xtdb-tutorial/raw/main/images/3a-datalog-queries-mercury-title.png)

# Introduction

This is the third instalment of the XTDB tutorial, focusing on Datalog queries.

## Setup

You need to get XTDB running before you can use it.

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

# Arrival on Mercury

You come into range of the satellites orbiting Mercury.
Your communications panel lights up and you read:

> Greetings.
>
> You have reached Mercury, home to the greatest stock market in the Solar System.
> Your ship has been flagged as a transport vessel for business-related activities.
>
> Please have your flight manifest ready and prepare to land.
>
> \- Mercury Commonwealth

The government is asking to see your flight manifest.

## Choose your path:

- **You have your manifest**: 
    - *You have permission to land, continue to the spaceport.*

- **You do not have your manifest**: 
    - *You do not have permission to land. You can either return to [Pluto](https://nextjournal.com/xtdb-tutorial/put-transactions) or continue at your own risk.*

# Spaceport

You read your XTDB manual as you wait for an available landing pad.

> A Datalog query consists of a set of variables and a set of clauses.
> The result of running a query is a result set (or lazy sequence) of the possible combinations of values that satisfy all of the clauses at the same time.
> These combinations of values are referred to as "tuples".
>
> The possible values within the result tuples are derived from your database of documents.
> The documents themselves are represented in the database indexes as "entity–attribute–value" (EAV) facts.
> For example, a single document `{:xt/id :myid, :color "blue", :age 12}` is transformed into two facts `[[:myid :color "blue"][:myid :age 12]]`.
>
> In the most basic case, a Datalog query works by searching for "subgraphs" in the database that match the pattern defined by the clauses.
> The values within these subgraphs are then returned according to the list of return variables requested in the `:find` vector within the query."
>
> \- XTDB manual *[Read More](https://xtdb.com/reference/queries.html)*

You are happy with what you have read, and in anticipation of the assignment, you define the standalone node.

```clojure
(def node (xt/start-node {}))
```

# Assignment

You land on the surface of the tidally locked planet.
As you do, the job ticket for this assignment is issued.

## Ticket

> ### Task
> *Find information on products for stock buyers*
> 
> ### Company
> *Interplanetary Buyers & Sellers (IPBS)*
>
> ### Contact 
> *Cosmina Sinnett*
> 
> ### Submitted 
> *2115-06-20T10:54:27*
> 
> ### Additional information:
> *We have some new starters in the sales team.
> They need to be trained on how to query XTDB using Datalog to quickly find the information they need on a product.
> Traders must have access to up-to-date information when talking to their clients.
> We would also like you to create a function that can be used for the things we have to look up a lot.
> I will include example data so they can learn using relevant commodities.*

On your way over to the IPBS office, you input the data in the attachment using the easy ingest function you created on Pluto.

```clojure
(defn easy-ingest
  "Uses XTDB put transaction to add a vector of
  documents to a specified system"
  [node docs]
  (xt/submit-tx node
    (vec (for [doc docs]
              [::xt/put doc])))
  (xt/sync node))

(def data
  [{:xt/id :commodity/Pu
    :common-name "Plutonium"
    :type :element/metal
    :density 19.816
    :radioactive true}

   {:xt/id :commodity/N
    :common-name "Nitrogen"
    :type :element/gas
    :density 1.2506
    :radioactive false}

   {:xt/id :commodity/CH4
    :common-name "Methane"
    :type :molecule/gas
    :density 0.717
    :radioactive false}

   {:xt/id :commodity/Au
    :common-name "Gold"
    :type :element/metal
    :density 19.300
    :radioactive false}

   {:xt/id :commodity/C
    :common-name "Carbon"
    :type :element/non-metal
    :density 2.267
    :radioactive false}

   {:xt/id :commodity/borax
    :common-name "Borax"
    :IUPAC-name "Sodium tetraborate decahydrate"
    :other-names ["Borax decahydrate" "sodium borate" "sodium tetraborate" "disodium tetraborate"]
    :type :mineral/solid
    :appearance "white solid"
    :density 1.73
    :radioactive false}])

(easy-ingest node data)
```

This means you are ready to give them a tutorial when you get there.

> Oh good, you’re here.
>
> I have a room reserved and we have five new starters ready and waiting to learn how to query XTDB.
>
> We are in the middle of our double sunrise.
> The workers take this time to rest, but in half an Earth hour the sun will rise again and the workers will start back.
>
> You can take this time to prepare any training material if you wish.
>
> \- Cosmina Sinnett

You have the opportunity to prepare examples for the lesson ahead.

## Choose your path:

- **"You use the time wisely and plan some examples"**: 
    - *Continue to complete the assignment.*

- **"You decide to wing it and see how the tutorial goes"**: 
    - *You go and teach the new starters. They are not impressed with your lack of preparation. They learn next to nothing and you realize you made a mistake.*

# Datalog Tutorial

You put together examples and make notes so you can be confident in your lesson.

## Lesson Plan

### 1. Basic Query

```clojure
(xt/q (xt/db node)
        '{:find [element]
          :where [[element :type :element/metal]]})
```

*This basic query is returning all the elements that are defined as `:element/metal`.
The `:find` clause tells XTDB the variables you want to return.*

*In this case, we are returning the `:xt/id` due to our placement of `element`.*

### 2. Quoting

```clojure
(=
 (xt/q (xt/db node)
         '{:find [element]
           :where [[element :type :element/metal]]})

 (xt/q (xt/db node)
         {:find '[element]
          :where '[[element :type :element/metal]]})

 (xt/q (xt/db node)
         (quote
          {:find [element]
           :where [[element :type :element/metal]]})))
```

*The vectors given to the clauses should be quoted.
How you do it at this stage is arbitrary.*

### 3. Return the name of metal elements.

```clojure
(xt/q (xt/db node)
        '{:find [name]
          :where [[e :type :element/metal]
                  [e :common-name name]]})
```

*To find all the names of the commodities that have a certain property, such as `:type`, you need to use a combination of clauses.
Here we have bound the results of type `:element/metal` to `e`.
Next, we can use the results bound to `e` and bind the `:common-name` of them to `name`.
`name` is what has been specified to be returned and so our result is the common names of all the elements that are metals.*

*One way to think of this is that you are filtering to only get the results that satisfy all the clauses.*

### 4. More information.

```clojure
(xt/q (xt/db node)
        '{:find [name rho]
          :where [[e :density rho]
                  [e :common-name name]]})
```

*You can pull out as much data as you want into your result tuples by adding additional variables to the `:find` clause.*

*The example above returns the `:density` and the `:common-name` values for all entities in XTDB that have values of some kind for both `:density` and `:common-name` attributes.*

### 5. Arguments

```clojure
(xt/q (xt/db node)
      '{:find [name]
        :where [[e :type type]
                [e :common-name name]]
        :in [type]}
      :element/metal)
```

*`:in` can be used to further filter the results. Lets break down what is going down here.*

*First, we are assigning all `:xt/id`s that have a `:type` to `e`:*

`e` ← `#{[:commodity/Pu] [:commodity/borax] [:commodity/CH4] [:commodity/Au] [:commodity/C] [:commodity/N]}`

*At the same time, we are assigning all the `:types` to `type`:*

`type` ← `#{[:element/gas] [:element/metal] [:element/non-metal] [:mineral/solid] [:molecule/gas]}`

*Then we assign all the names within `e` that have a `:common-name` to `name`:*

`name` ← `#{["Methane"] ["Carbon"] ["Gold"] ["Plutonium"] ["Nitrogen"] ["Borax"]}`

*We have specified that we want to get the names out, but not before looking at `:in`*

*For `:in` we have further filtered the results to only show us the names of that have `:type` `:element/metal`.*

*We could have done that before inside the `:where` clause, but using `:in` removes the need for hard-coding inside the query clauses.*

*We pass the value(s) to be used as the third argument to `xt/q`.*

***

You give your lesson to the new starters when they return.
They are a good audience and follow it well.

To check their understanding you set them a task to create a function to aid their daily queries.
You are impressed with their efforts.

```clojure
(defn filter-type
  [type]
  (xt/q (xt/db node)
        '{:find [name]
          :where [[e :common-name name]
                  [e :type type]]
          :in [type]}
        type))

(defn filter-appearance
  [description]
  (xt/q (xt/db node)
        '{:find [name IUPAC]
          :where [[e :common-name name]
                  [e :IUPAC-name IUPAC]
                  [e :appearance appearance]]
          :in [appearance]}
        description))
```

```clojure
(filter-type :element/metal)
```

```clojure
(filter-appearance "white solid")
```

When you are finished, Cosmina thanks you and you head back to the spaceport.

# Spaceport

You are back at your spaceship.
Seeing another light on your communications panel, you realize there is another assignment ready for you.

> Congratulations on completing your assignment.
> You are getting the hang of things now, and we are impressed with your progress.
>
> We would like you to go to Neptune.
> They have recently lost a lot of data in a flood so they have decided to digitize their entire system and archives.
> We told them you could do it in such a way that the data is still time ordered as it was with their previous filing system.
>
> Good luck, and don’t forget to update your manifest."
>
> \- Helios Banking Inc.

You update your manifest with the latest badge.

```clojure
(xt/submit-tx
 node
 [[::xt/put
   {:xt/id :manifest
    :pilot-name "Johanna"
    :id/rocket "SB002-sol"
    :id/employee "22910x2"
    :badges ["SETUP" "PUT" "DATALOG-QUERIES"]
    :cargo ["stereo" "gold fish" "slippers" "secret note"]}]])

(xt/sync node)
```

You enter the countdown for liftoff to Neptune. [ See you soon.](https://nextjournal.com/xtdb-tutorial/bitemporality)

![Neptune: Bitemporality](https://github.com/xtdb/xtdb-tutorial/raw/main/images/3b-bitemporality-neptune.png)
