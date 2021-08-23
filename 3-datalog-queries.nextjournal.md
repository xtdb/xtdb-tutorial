# 3: Datalog Queries with XTDB – Mercury Assignment

![Clojure_logo.png][nextjournal#file#6e1e3414-7ad4-42fa-ace0-6939985e69e2]

# Introduction

This is the third installment of the xtdb tutorial, focusing on Datalog queries.

## Setup

You need to get xtdb running before you can use it.

```edn no-exec id=ffcf0396-b3f9-40e6-a0c2-654401879781
{:deps
 {org.clojure/clojure {:mvn/version "1.10.0"}
  org.clojure/tools.deps.alpha
  {:git/url "https://github.com/clojure/tools.deps.alpha.git"
   :sha "f6c080bd0049211021ea59e516d1785b08302515"}
  juxt/crux-core {:mvn/version "RELEASE"}}}
```

```clojure id=35dc65e9-f458-4e32-9a59-1af72cd12a78
(require '[crux.api :as crux])
```

# Arrival on Mercury

You come into range of the satellites orbiting Mercury. Your communications panel lights up and you read:

```clojure no-exec id=a86efe70-c896-4cc1-ad16-2bdc46cb01b2
"Greetings.

You have reached Mercury, home to the greatest stock market in the Solar System. Your ship has been flagged as a transport vessel for business related activities.

Please have your flight manifest ready and prepare to land."

- Mercury Commonwealth
```

The government is asking to see your flight manifest.

## Choose your path:

**You have your manifest** : *You have permission to land, continue to the space port.*

**You do not have your manifest** : *You do not have permission to land. You can either return to [Pluto](https://nextjournal.com/xtdb-tutorial/put-transactions), or continue at your own risk.*

# Space Port

You read your xtdb manual as you wait for an available landing pad.

```clojure no-exec id=223ddbe8-8eed-4e69-ae1c-57f484971dcb
"A Datalog query consists of a set of variables and a set of clauses. The result of running a query is a result set (or lazy sequence) of the possible combinations of values that satisfy all of the clauses at the same time. These combinations of values are referred to as "tuples".

The possible values within the result tuples are derived from your database of documents. The documents themselves are represented in the database indexes as "entity–attribute–value" (EAV) facts. For example, a single document {:crux.db/id :myid :color "blue" :age 12} is transformed into two facts [[:myid :color "blue"][:myid :age 12]].

In the most basic case, a Datalog query works by searching for "subgraphs" in the database that match the pattern defined by the clauses. The values within these subgraphs are then returned according to the list of return variables requested in the :find vector within the query."

— xtdb manual
```

*[Read More](https://xtdb.com/reference/queries.html)*

You are happy with what you have read, and in anticipation of the assignment you define the standalone node.

```clojure id=2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153
(def crux (crux/start-node {}))
```

# Assignment

You land on the surface of the tidally locked planet. As you do, the job ticket for this assignment is issued.

## Ticket

```clojure no-exec id=b62c033a-93f1-438b-abf1-bbbd0050c31f
Task 			"Find information on products for stock buyers"
Company		"Interplanetary Buyers & Sellers (IPBS)"
Contact 			"Cosmina Sinnett"
Submitted "2115-06-20T10:54:27"
Additional information:
"We have some new starters in the sales team. They need to be trained on how to query xtdb using Datalog to quickly find the information they need on a product. Traders must have access to up-to-date information when talking to their clients. We would also like you to create a function that can be used for the things we have to look up a lot. I will include example data so they can learn using relevant commodities."
```

[example_data.txt][nextjournal#file#551cde09-6dd5-4f40-90ee-b2024a0ceceb]

On your way over to the IPBS office you input the data in the attachment using the easy ingest function you created on Pluto.

```clojure id=ff24c118-14bf-4e4a-a4d3-03e890292d1b
(defn easy-ingest
  "Uses xtdb put transaction to add a vector of
  documents to a specified system"
  [db docs]
  (crux/submit-tx db
                  (vec (for [doc docs]
                         [:crux.tx/put doc]))))
(def data
  [{:crux.db/id :commodity/Pu
    :common-name "Plutonium"
    :type :element/metal
    :density 19.816
    :radioactive true}

   {:crux.db/id :commodity/N
    :common-name "Nitrogen"
    :type :element/gas
    :density 1.2506
    :radioactive false}

   {:crux.db/id :commodity/CH4
    :common-name "Methane"
    :type :molecule/gas
    :density 0.717
    :radioactive false}

   {:crux.db/id :commodity/Au
    :common-name "Gold"
    :type :element/metal
    :density 19.300
    :radioactive false}

   {:crux.db/id :commodity/C
    :common-name "Carbon"
    :type :element/non-metal
    :density 2.267
    :radioactive false}

   {:crux.db/id :commodity/borax
    :common-name "Borax"
    :IUPAC-name "Sodium tetraborate decahydrate"
    :other-names ["Borax decahydrate" "sodium borate" "sodium tetraborate" "disodium tetraborate"]
    :type :mineral/solid
    :appearance "white solid"
    :density 1.73
    :radioactive false}])

(easy-ingest crux data)
```

This means you are ready to give them a tutorial when you get there.

```clojure no-exec id=0423d106-b075-40be-9988-8a9f8d5dab44
"Oh good, you’re here.

I have a room reserved and we have five new starters ready and waiting to learn how to query xtdb.

We are in the middle of our double sunrise. The workers take this time to rest, but in half an Earth hour the sun will rise again and the workers will start back.

You can take this time to prepare any training material if you wish."

— Cosmina Sinnett
```

You have the opportunity to prepare examples for the lesson ahead.

## Choose your path:

**"You use the time wisely and plan some examples"** : *Continue to complete the assignment.*

**"You decide to wing it and see how the tutorial goes"** : *You go and teach the new starters. They are not impressed with your lack of preparation. They learn next to nothing and you realize you made a mistake.*

# Datalog Tutorial

You put together examples and make notes so you can be confident in your lesson.

## Lesson Plan

*Example 1.* **Basic Query**

```clojure id=6e945190-c8d6-4f61-bbf5-6f0a96fa4726
(crux/q (crux/db crux)
        '{:find [element]
          :where [[element :type :element/metal]]})
```

*This basic query is returning all the elements that are defined as `:element/metal`. The `:find` clause tells xtdb the variables you want to return.* 

*In this case we are returning the `:crux.db/id` due to our placement of `element`.* 

*Example 2.* **Quoting**

```clojure id=4bbf4ada-e34a-4af9-977e-f8d8f2d9d42a
(=
 (crux/q (crux/db crux)
         '{:find [element]
           :where [[element :type :element/metal]]})

 (crux/q (crux/db crux)
         {:find '[element]
          :where '[[element :type :element/metal]]})

 (crux/q (crux/db crux)
         (quote
          {:find [element]
           :where [[element :type :element/metal]]})))
```

*The vectors given to the clauses should be quoted. How you do it at this stage is arbitrary, but it becomes more important if you are using `:args`, which we will cover momentarily.*

*Example 3*. **Return the name of metal elements.**

```clojure id=14f91b2a-9f3f-4ab5-9796-279bdfbcdf67
(crux/q (crux/db crux)
        '{:find [name]
          :where [[e :type :element/metal]
                  [e :common-name name]]})
```

*To find all the names of the commodities that have a certain property, such as `:type`, you need to use a combination of clauses. Here we have bound the results of type `:element/metal` to `e`. Next, we can use the results bound to `e` and bind the `:common-name` of them to `name`. `name` is what has been specified to be returned and so our result is the common names of all the elements that are metals.*

*One way to think of this is that you are filtering to only get the results that satisfy all the clauses.*

*Example 4.* **More information.**

```clojure id=9a20d1a9-a466-4143-8f77-b8154f362ab4
(crux/q (crux/db crux)
        '{:find [name rho]
          :where [[e :density rho]
                  [e :common-name name]]})
```

*You can pull out as much data as you want into your result tuples by adding additional variables to the `:find` clause.*

*The example above returns the `:density` and the `:common-name` values for all entities in xtdb that have values of some kind for both `:density` and `:common-name` attributes.*

*Example 5*. **Arguments***.*

```clojure id=9c836f78-21cc-4ddd-aef8-e40a755d99a2
(crux/q (crux/db crux)
        {:find '[name]
         :where '[[e :type t]
                  [e :common-name name]]
         :args [{'t :element/metal}]})
```

*`:args` can be used to further filter the results. Lets break down what is going down here.*

*First, we are assigning all `:crux.db/id`s that have a `:type` to `e`:*

`e` ← `#{[:commodity/Pu] [:commodity/borax] [:commodity/CH4] [:commodity/Au] [:commodity/C] [:commodity/N]}`

*At the same time we are assigning all the `:types` to `t`:*

`t` ← `#{[:element/gas] [:element/metal] [:element/non-metal] [:mineral/solid] [:molecule/gas]}`

*Then we assign all the names within `e` that have a `:common-name` to `name`:*

`name` ← `#{["Methane"] ["Carbon"] ["Gold"] ["Plutonium"] ["Nitrogen"] ["Borax"]}`

*We have specified that we want to get the names out, but not before looking at `:args`*

*In `:args` we have further filtered the results to only show us the names of that have `:type` `:element/metal`.*

*We could have done that before inside the `:where` clause, but using `:args` removes the need for hard-coding inside the query clauses.*

**- Notes.**

You give your lesson to the new starters when they return. They are a good audience and follow it well.

To check their understanding you set them a task to create a function to aid their daily queries. You are impressed with their efforts.

```clojure id=de58e539-f7db-47c7-974c-52c5450b69fe
(defn filter-type
  [type]
  (crux/q (crux/db crux)
        {:find '[name]
         :where '[[e :type t]
                  [e :common-name name]]
         :args [{'t type}]}))

(defn filter-appearance
  [description]
  (crux/q (crux/db crux)
        {:find '[name IUPAC]
         :where '[[e :common-name name]
                  [e :IUPAC-name IUPAC]
                  [e :appearance ?appearance]]
         :args [{'?appearance description}]}))
```

```clojure id=f46e8e06-61db-4e10-9235-4cb2b26892fe
(filter-type :element/metal)
```

```clojure id=93d471b1-4f0c-421a-84cb-a2295214de9a
(filter-appearance "white solid")
```

When you are finished, Cosmina thanks you and you head back to the space port.

# Space Port

You are back at your spaceship. Seeing another light on your communications panel, you realize there is another assignment ready for you.

```clojure no-exec id=efc84bb8-702c-4fa7-82f8-6cfef39dd934
"Congratulations on completing your assignment. You are getting the hang of things now, and we are impressed with your progress.

We would like you to go to Neptune. They have recently lost a lot of data in a flood so they have decided to digitize their entire system and archives. We told them you could do it in such a way that the data is still time ordered as it was with their previous filing system.

Good luck, and don’t forget to update your manifest."

— Helios Banking Inc. 
```

You update your manifest with the latest badge.

```clojure id=ab2ade6b-7352-4e44-b659-cad259ea12f9
(crux/submit-tx
 crux
 [[:crux.tx/put 
   {:crux.db/id :manifest
    :pilot-name "Johanna"
    :id/rocket "SB002-sol"
    :id/employee "22910x2"
    :badges ["SETUP" "PUT" "DATALOG-QUERIES"]
    :cargo ["stereo" "gold fish" "slippers" "secret note"]}]])
```

You enter the countdown for lift off to Neptune.[ See you soon.](https://nextjournal.com/xtdb-tutorial/bitemporality)

![neptune.png][nextjournal#file#175ff797-b4ba-4335-b5e5-be5b2086c24b]


[nextjournal#file#6e1e3414-7ad4-42fa-ace0-6939985e69e2]:
<https://nextjournal.com/data/QmTHuKgi3vjGTvyPg3AVNVt5amZ8C1P8xH22PeCd7s1sNm?content-type=image/png&node-id=6e1e3414-7ad4-42fa-ace0-6939985e69e2&filename=Clojure_logo.png&node-kind=file>

[nextjournal#file#551cde09-6dd5-4f40-90ee-b2024a0ceceb]:
<https://nextjournal.com/data/QmSYFEqzjKccDoa3u22xMyHYMphmxwygBrMt6hUGaHeVdy?content-type=text/plain&node-id=551cde09-6dd5-4f40-90ee-b2024a0ceceb&filename=example_data.txt&node-kind=file>

[nextjournal#file#175ff797-b4ba-4335-b5e5-be5b2086c24b]:
<https://nextjournal.com/data/QmdUdMvwwgpi6Jrne9hzDfs6BGoPYKZJnfqnUwa7cwZQRG?content-type=image/png&node-id=175ff797-b4ba-4335-b5e5-be5b2086c24b&filename=neptune.png&node-kind=file>

<details id="com.nextjournal.article">
<summary>This notebook was exported from <a href="https://nextjournal.com/a/LPUNww665btBQpne5piTg?change-id=CwhbAJ6BvJ3U1h324Sjhsg">https://nextjournal.com/a/LPUNww665btBQpne5piTg?change-id=CwhbAJ6BvJ3U1h324Sjhsg</a></summary>

```edn nextjournal-metadata
{:article
 {:settings nil,
  :nodes
  {"0423d106-b075-40be-9988-8a9f8d5dab44"
   {:id "0423d106-b075-40be-9988-8a9f8d5dab44",
    :kind "code-listing",
    :name "Head of Training - IPBS"},
   "14f91b2a-9f3f-4ab5-9796-279bdfbcdf67"
   {:compute-ref #uuid "0636f639-4dd1-4f88-9784-e7d454311e8d",
    :exec-duration 75,
    :id "14f91b2a-9f3f-4ab5-9796-279bdfbcdf67",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "175ff797-b4ba-4335-b5e5-be5b2086c24b"
   {:id "175ff797-b4ba-4335-b5e5-be5b2086c24b", :kind "file"},
   "223ddbe8-8eed-4e69-ae1c-57f484971dcb"
   {:id "223ddbe8-8eed-4e69-ae1c-57f484971dcb",
    :kind "code-listing",
    :name "xtdb manual"},
   "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153"
   {:compute-ref #uuid "f3c731fa-ca02-4472-8135-cc1057f1b916",
    :exec-duration 5938,
    :id "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "35dc65e9-f458-4e32-9a59-1af72cd12a78"
   {:compute-ref #uuid "7fb7c70c-70d1-4eea-9ff3-a05fbcff532a",
    :exec-duration 13370,
    :id "35dc65e9-f458-4e32-9a59-1af72cd12a78",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "4bbf4ada-e34a-4af9-977e-f8d8f2d9d42a"
   {:compute-ref #uuid "14a382c5-14ef-48de-b52a-2bb60ad6bb33",
    :exec-duration 144,
    :id "4bbf4ada-e34a-4af9-977e-f8d8f2d9d42a",
    :kind "code",
    :name "Example 2. Quoting",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "551cde09-6dd5-4f40-90ee-b2024a0ceceb"
   {:id "551cde09-6dd5-4f40-90ee-b2024a0ceceb", :kind "file"},
   "6e1e3414-7ad4-42fa-ace0-6939985e69e2"
   {:id "6e1e3414-7ad4-42fa-ace0-6939985e69e2",
    :kind "file",
    :layout :normal},
   "6e945190-c8d6-4f61-bbf5-6f0a96fa4726"
   {:compute-ref #uuid "4115b67a-59da-4fe0-8624-e257e8bb72ed",
    :exec-duration 567,
    :id "6e945190-c8d6-4f61-bbf5-6f0a96fa4726",
    :kind "code",
    :name "Example 1. Basic Query",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
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
   "93d471b1-4f0c-421a-84cb-a2295214de9a"
   {:compute-ref #uuid "9f4eba32-f25d-4ecd-8db4-f6eb1df05c97",
    :exec-duration 107,
    :id "93d471b1-4f0c-421a-84cb-a2295214de9a",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "9a20d1a9-a466-4143-8f77-b8154f362ab4"
   {:compute-ref #uuid "2eae8a9f-560c-446f-a1b7-b0339536b636",
    :exec-duration 203,
    :id "9a20d1a9-a466-4143-8f77-b8154f362ab4",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "9c836f78-21cc-4ddd-aef8-e40a755d99a2"
   {:compute-ref #uuid "4a006909-9ff3-4f92-8923-f951c99098ae",
    :exec-duration 88,
    :id "9c836f78-21cc-4ddd-aef8-e40a755d99a2",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "a86efe70-c896-4cc1-ad16-2bdc46cb01b2"
   {:id "a86efe70-c896-4cc1-ad16-2bdc46cb01b2", :kind "code-listing"},
   "ab2ade6b-7352-4e44-b659-cad259ea12f9"
   {:compute-ref #uuid "e747ccf7-df1e-4e6f-8fe6-634b5ddba4a8",
    :exec-duration 89,
    :id "ab2ade6b-7352-4e44-b659-cad259ea12f9",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "b62c033a-93f1-438b-abf1-bbbd0050c31f"
   {:id "b62c033a-93f1-438b-abf1-bbbd0050c31f",
    :kind "code-listing",
    :name "Job Ticket"},
   "de58e539-f7db-47c7-974c-52c5450b69fe"
   {:compute-ref #uuid "45f6c83e-9bb4-4fff-898d-9184f7413b4d",
    :exec-duration 67,
    :id "de58e539-f7db-47c7-974c-52c5450b69fe",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "efc84bb8-702c-4fa7-82f8-6cfef39dd934"
   {:custom-language "Communications Panel",
    :id "efc84bb8-702c-4fa7-82f8-6cfef39dd934",
    :kind "code-listing",
    :name "Communications Panel"},
   "f46e8e06-61db-4e10-9235-4cb2b26892fe"
   {:compute-ref #uuid "90768946-e2b4-43c2-b7b9-ef6fe768e8c3",
    :exec-duration 99,
    :id "f46e8e06-61db-4e10-9235-4cb2b26892fe",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "ff24c118-14bf-4e4a-a4d3-03e890292d1b"
   {:compute-ref #uuid "cde9c3da-4bc3-4a29-abb6-c57d2c826d59",
    :exec-duration 305,
    :id "ff24c118-14bf-4e4a-a4d3-03e890292d1b",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "ffcf0396-b3f9-40e6-a0c2-654401879781"
   {:id "ffcf0396-b3f9-40e6-a0c2-654401879781",
    :kind "code-listing",
    :name "deps.edn"}},
  :nextjournal/id #uuid "02b4fb03-3292-4053-ac81-326b0ac86047",
  :article/change
  {:nextjournal/id #uuid "60b7b3e2-ec29-4ac6-9c79-1243c28b1a5b"}}}

```
</details>
