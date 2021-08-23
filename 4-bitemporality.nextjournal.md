# 4: Bitemporality with XTDB – Neptune Assignment

![Neptune: Bitemporality](https://github.com/xtdb/xtdb-tutorial/raw/main/images/4a-bitemporality-neptune-title.png)

# Introduction

This is the bitemporal installment of the xtdb "choose your own adventure" tutorial series.

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

# Arrival on Neptune

You enter the Neptunian atmosphere and your communications panel lights up.

```clojure no-exec id=a86efe70-c896-4cc1-ad16-2bdc46cb01b2
"It is our honor to welcome you to the planet Neptune.

If you are visiting for business reasons, please present your manifest.

Otherwise, have your visa ready for inspection."

- Poseidon Republic of Wealth
```

The government is asking to see your flight manifest.

## Choose your path:

**You have your manifest** : *You have permission to land, continue to the space port.*

**You do not have your manifest** : *You do not have permission to land. You can either return to [Mercury](https://nextjournal.com/xtdb-tutorial/datalog-queries) or continue at your own risk.*

# Space Port

On your way down to the landing site you take the time to read the xtdb manual.

```clojure no-exec id=223ddbe8-8eed-4e69-ae1c-57f484971dcb
"One or more documents can be inserted into xtdb via a put transaction at a specific valid-time. The valid-time can be any time (past, future or present).

If no valid-time is provided, xtdb will default to the transaction time, i.e. the present. Each document survives until it is deleted or a new version of it is added."

— xtdb manual
```

*[Read More](https://xtdb.com/articles/bitemporality.html)*

You are happy with what you have read, and in anticipation of the assignment you define the standalone node.

```clojure id=2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153
(def crux (crux/start-node {}))
```

# Assignment

Upon landing on the ice giant, your communications panel lights up indicating that the job ticket is available.

## Ticket

```clojure no-exec id=b62c033a-93f1-438b-abf1-bbbd0050c31f
Task 			"Back fill insurance documents"
Company		"Coast Insurance"
Contact 			"Lyndon Mercia-York"
Submitted "2115-02-22T13:38:20"
Additional information:
"We have lost a lot of our records in a flood. I think it is prudent to start storing our data digitally. It is important to track policy holders' level of cover at the time of the incident. We have read a lot about the conveniences of xtdb's bitemporality in this situation. We need you to help us get up and running. Please send someone quickly though - the waters are rising."
```

Outside your ship you are met by a panicked looking Lyndon.

```clojure no-exec id=7b50754b-7b23-4cd7-a3ee-f7d3dbbca280
"Thank goodness you’re here.

We need you to show us how to put our customers information into xtdb in order of time. Working with insurance claims, we should be able to easily look back in time at what type of coverage the customer had at the time of the incident.

Are you able to help us?"

— Lyndon Mercia-York
```

## Choose your path:

**"Yes, I'll give it a go."** : *Continue to complete the assignment.*

**"I'm not even sure how to begin"** : *Take some time to read through the xtdb manual again. If you're still unsure then you can follow along anyway and see if things become clear.*

## Assignment

Lyndon gives you some data for a client that you can use as an example. Coast Insurance need to know what kind of cover each customer has and if it was valid at a given time.

You show them how to ingest a document using a `valid-time` so that the information is backdated to when the customer took the cover out.

```clojure id=061c7ac5-9255-48ab-ac82-97bbdf7d9746
(crux/submit-tx
 crux
 [[:crux.tx/put
   {:crux.db/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? true
    :cover-type :Full}
   #inst "2114-12-03"]])
```

The company needs to know the history of insurance for each cover. You show them how to use the bitemporality of xtdb to do this.

```clojure id=adaf6a80-4241-40a1-9016-d342e1a056c1
(crux/submit-tx
 crux
 [[:crux.tx/put	;; (1) 
   {:crux.db/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? true
    :cover-type :Full}
   #inst "2113-12-03" ;; Valid time start
   #inst "2114-12-03"] ;; Valid time end
  
  [:crux.tx/put  ;; (2)
   {:crux.db/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? true
    :cover-type :Full}
   #inst "2112-12-03"
   #inst "2113-12-03"]
  
  [:crux.tx/put	;; (3)
   {:crux.db/id :consumer/RJ29sUU
    :consumer-id :RJ29sUU
    :first-name "Jay"
    :last-name "Rose"
    :cover? false}
   #inst "2112-06-03"
   #inst "2112-12-02"]

  [:crux.tx/put ;; (4)
   {:crux.db/id :consumer/RJ29sUU
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

You now show them a few queries. You know that you can query xtdb as of a given `valid-time`. This shows the state of xtdb at that time.

First you chose a date that the customer had full cover:

```clojure id=64649b39-12e3-4cc8-b4bd-9b9877f07d58
(crux/q (crux/db crux #inst "2114-01-01")
        '{:find [cover type]
          :where [[e :consumer-id :RJ29sUU]
                  [e :cover? cover]
                  [e :cover-type type]]})
```

Next you show them a query for a the customer in a time when they had a different type of cover:

```clojure id=5cac500b-fcd3-4a45-a2af-aca30afb8b0e
(crux/q (crux/db crux #inst "2111-07-03")
        '{:find [cover type]
          :where [[e :consumer-id :RJ29sUU]
                  [e :cover? cover]
                  [e :cover-type type]]})
```

And finally you show them a time when the customer had no cover at all.

```clojure id=1f02d99e-b8c6-4de4-8cf1-8a2fe94e4f1a
(crux/q (crux/db crux #inst "2112-07-03")
        '{:find [cover type]
          :where [[e :consumer-id :RJ29sUU]
                  [e :cover? cover]
                  [e :cover-type type]]})
```

Confident in their ability to put the remainder of their records into xtdb, Lyndon thanks you.

```clojure no-exec id=25fb2d49-5c82-46fc-ac6b-9a3b3eb4a548
"I can’t believe we’ve not digitized sooner. There was a huge push to start using more paper as the Neptune tree population was getting out of control from the accelerated terraforming, but since all these floods I’m not sure paper was the right choice."
— Lyndon Mercia-York
```

You say goodbye to Lyndon and head back to the space port.

# Space Port

Back at your spaceship you check your communications panel. There is a new assignment waiting for you.

```clojure no-exec id=efc84bb8-702c-4fa7-82f8-6cfef39dd934
"We have assigned you a quick task on Saturn helping a small company who are having some problems keeping their records in order.

This shouldn’t take long, but don’t forget they will still need to see your manifest."

— Helios Banking Inc. 
```

You add the new badge to your manifest

```clojure id=ab2ade6b-7352-4e44-b659-cad259ea12f9
(crux/submit-tx
 crux
 [[:crux.tx/put 
   {:crux.db/id :manifest
    :pilot-name "Johanna"
    :id/rocket "SB002-sol"
    :id/employee "22910x2"
    :badges ["SETUP" "PUT" "DATALOG-QUERIES" "BITEMP"]
    :cargo ["stereo" "gold fish" "slippers" "secret note"]}]])
```

You enter the countdown for lift off the ringed plant. [See you soon.](https://nextjournal.com/xtdb-tutorial/match)

![Saturn: Match](https://github.com/xtdb/xtdb-tutorial/raw/main/images/4b-match-saturn.png)


<details id="com.nextjournal.article">
<summary>This notebook was exported from <a href="https://nextjournal.com/a/LPfueTz7gpQaDRhsbnrBH?change-id=CwhbCRtFmzWBDteGre3EjE">https://nextjournal.com/a/LPfueTz7gpQaDRhsbnrBH?change-id=CwhbCRtFmzWBDteGre3EjE</a></summary>

```edn nextjournal-metadata
{:article
 {:settings nil,
  :nodes
  {"061c7ac5-9255-48ab-ac82-97bbdf7d9746"
   {:compute-ref #uuid "d1bdde96-d07d-47fc-91ea-41a729319fdf",
    :exec-duration 359,
    :id "061c7ac5-9255-48ab-ac82-97bbdf7d9746",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "175ff797-b4ba-4335-b5e5-be5b2086c24b"
   {:id "175ff797-b4ba-4335-b5e5-be5b2086c24b", :kind "file"},
   "1f02d99e-b8c6-4de4-8cf1-8a2fe94e4f1a"
   {:compute-ref #uuid "deb9a847-fee0-4f5e-9eae-3ff8d33daaeb",
    :exec-duration 61,
    :id "1f02d99e-b8c6-4de4-8cf1-8a2fe94e4f1a",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "223ddbe8-8eed-4e69-ae1c-57f484971dcb"
   {:id "223ddbe8-8eed-4e69-ae1c-57f484971dcb",
    :kind "code-listing",
    :name "xtdb manual"},
   "25fb2d49-5c82-46fc-ac6b-9a3b3eb4a548"
   {:id "25fb2d49-5c82-46fc-ac6b-9a3b3eb4a548",
    :kind "code-listing",
    :name "Coast Insurance"},
   "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153"
   {:compute-ref #uuid "7e296671-fb10-4985-b194-c85ea50b1229",
    :exec-duration 5636,
    :id "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "35dc65e9-f458-4e32-9a59-1af72cd12a78"
   {:compute-ref #uuid "c54676cc-82d6-4c46-9ed6-bdcd7aa57498",
    :exec-duration 11652,
    :id "35dc65e9-f458-4e32-9a59-1af72cd12a78",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "5cac500b-fcd3-4a45-a2af-aca30afb8b0e"
   {:compute-ref #uuid "4e5306e5-ff57-4536-a34f-a58049b46409",
    :exec-duration 123,
    :id "5cac500b-fcd3-4a45-a2af-aca30afb8b0e",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "64649b39-12e3-4cc8-b4bd-9b9877f07d58"
   {:compute-ref #uuid "dd43802b-2bd4-4d86-88a8-3b86ab2761c0",
    :exec-duration 103,
    :id "64649b39-12e3-4cc8-b4bd-9b9877f07d58",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "6e1e3414-7ad4-42fa-ace0-6939985e69e2"
   {:id "6e1e3414-7ad4-42fa-ace0-6939985e69e2",
    :kind "file",
    :layout :normal},
   "7b50754b-7b23-4cd7-a3ee-f7d3dbbca280"
   {:id "7b50754b-7b23-4cd7-a3ee-f7d3dbbca280",
    :kind "code-listing",
    :name "Coast Insurance"},
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
   "a86efe70-c896-4cc1-ad16-2bdc46cb01b2"
   {:id "a86efe70-c896-4cc1-ad16-2bdc46cb01b2", :kind "code-listing"},
   "ab2ade6b-7352-4e44-b659-cad259ea12f9"
   {:compute-ref #uuid "7852905c-7d7b-4bb9-9636-2e419300e6d7",
    :exec-duration 82,
    :id "ab2ade6b-7352-4e44-b659-cad259ea12f9",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "adaf6a80-4241-40a1-9016-d342e1a056c1"
   {:compute-ref #uuid "7464c180-c204-4464-b4ec-e0e8bb230a25",
    :exec-duration 138,
    :id "adaf6a80-4241-40a1-9016-d342e1a056c1",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "b62c033a-93f1-438b-abf1-bbbd0050c31f"
   {:id "b62c033a-93f1-438b-abf1-bbbd0050c31f",
    :kind "code-listing",
    :name "Job Ticket"},
   "efc84bb8-702c-4fa7-82f8-6cfef39dd934"
   {:custom-language "Communications Panel",
    :id "efc84bb8-702c-4fa7-82f8-6cfef39dd934",
    :kind "code-listing",
    :name "Communications Panel"},
   "ffcf0396-b3f9-40e6-a0c2-654401879781"
   {:id "ffcf0396-b3f9-40e6-a0c2-654401879781",
    :kind "code-listing",
    :name "deps.edn"}},
  :nextjournal/id #uuid "02b51a5f-8ee9-4d13-bcdc-87b78174b4a0",
  :article/change
  {:nextjournal/id #uuid "60b7b3fc-8c3c-495b-bdea-369032509ce5"}}}

```
</details>
