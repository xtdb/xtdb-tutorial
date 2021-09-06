# 6: Delete with XTDB – Jupiter Assignment

![Jupiter: Delete](https://github.com/xtdb/xtdb-tutorial/raw/main/images/6a-delete-jupiter-title.png)

# Introduction

This is the `delete` installment of the xtdb tutorial.

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

# Arrival on Jupiter

You approach Jupiter and marvel at its bands. You wish you could have seen it this close before the Great Red Spot dissipated.

As you enter the Jovian atmosphere your communications panel lights up with the now expected, but rather terse message from boundary control.

```clojure no-exec id=a86efe70-c896-4cc1-ad16-2bdc46cb01b2
"Jupiter’s boundary is controlled.
If you wish to enter, present your papers now."

— Zeus Confederacy
```

The government is asking to see your flight manifest.

## Choose your path:

**You have your manifest** : *You have permission to land, continue to the space port.*

**You do not have your manifest** : *You do not have permission to land. You can either return to [Saturn](https://nextjournal.com/xtdb-tutorial/match) or continue at your own risk.*

# Space Port

It’s a turbulent ride down to the space port. To take your mind off the colossal storm outside, you check the xtdb manual for the `delete` operation.

```clojure no-exec id=223ddbe8-8eed-4e69-ae1c-57f484971dcb
"Currently there are only four transaction operations in xtdb: put, delete, match and evict.
		Transaction 	(Description)
    put    		(Writes a version of a document)
    delete    (Deletes a version of a document)
    match     (Stops a transaction if the precondition is not met.)
    evict    	(Removes an document entirely)

delete:
Deletes a document at a given valid time. Historical version of the document will still be available.

The delete operation takes a valid eid with the option to include a start and end valid-time.

The document will be deleted as of the transaction time, or beteen the start and end valid-times if provided. Historical versions of the document that fall outside of the valid-time window will be preserved.

A complete delete transaction has the form:
[::xt/delete eid valid-time-start valid-time-end]"

— xtdb manual
```

*[Read More](https://xtdb.com/reference/transactions.html#delete)*

You are happy with what you have read, and in anticipation of the assignment you define the standalone node.

```clojure id=2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153
(def node (xt/start-node {}))
```

# Assignment

You land on the raised platform and open your job ticket

## Ticket

```clojure no-exec id=b62c033a-93f1-438b-abf1-bbbd0050c31f
Task 			"Remove assigned clients"
Company		"Helios Banking Inc."
Contact 	"Kaarlang"
Submitted "2115-02-10T13:38:20"
Additional information:
"Help Kaarlang delete client history from his xtdb node in accordance with Earth data protection laws."
```

As you leave your ship, you are met by the martian Kaarlang:

```clojure no-exec id=7b50754b-7b23-4cd7-a3ee-f7d3dbbca280
"Hi there, I believe you’re here to help me.

I’ve been told I need to delete my client history with today being my last day.

Is this something you can help me with?"

— Kaarlang
```

## Choose your path:

**"Yes, we'll work together to do this."** : *Continue to complete the assignment.*

**"I'm not even sure how to begin"** : *Take some time to read through the xtdb manual again. If you're still unsure then you can follow along anyway and see if things become clear.*

## Assignment

Kaarlang gives you his client history so you can sync up your xtdb node.

```clojure id=061c7ac5-9255-48ab-ac82-97bbdf7d9746
(xt/submit-tx node
                [[::xt/put {:xt/id :kaarlang/clients
                                :clients [:encompass-trade]}
                  #inst "2110-01-01T09"
                  #inst "2111-01-01T09"]

                 [::xt/put {:xt/id :kaarlang/clients
                                :clients [:encompass-trade :blue-energy]}
                  #inst "2111-01-01T09"
                  #inst "2113-01-01T09"]

                 [::xt/put {:xt/id :kaarlang/clients
                                :clients [:blue-energy]}
                  #inst "2113-01-01T09"
                  #inst "2114-01-01T09"]

                 [::xt/put {:xt/id :kaarlang/clients
                                :clients [:blue-energy :gold-harmony :tombaugh-resources]}
                  #inst "2114-01-01T09"
                  #inst "2115-01-01T09"]])
```

To get a good visual aid, you show Kaarlang how to view his client history. This way you both can see when the clients are deleted.

```clojure id=cf517ae0-bb75-40a0-b34d-d6a20a5c855b
(xt/entity-history
 (xt/db node #inst "2116-01-01T09")
 :kaarlang/clients
 :desc
 {:with-docs true})
```

[result][nextjournal#output#cf517ae0-bb75-40a0-b34d-d6a20a5c855b#result]

1. You explain that you are using a snapshot of xtdb with a future `valid-time` to see the full history.

The result shows the names of the clients that have been assigned to Kaarlang since he started at the company in 2110.

Next you delete the whole history of clients buy choosing a start and end `valid-time` that spans his entire employment time.

```clojure id=1b0cfda6-c1c6-4def-99bd-c071c6938be0
(xt/submit-tx
 node
 [[::xt/delete :kaarlang/clients #inst "2110-01-01" #inst "2116-01-01"]])
```

Using the same method as before you show Kaarlang the effect that this operation has had.

```clojure id=a9aa840c-db6e-49a0-aa74-6aca9b824072
(xt/entity-history
 (xt/db node #inst "2115-01-01T08")
 :kaarlang/clients
 :desc
 {:with-docs? true})
```

Kaarlang is impressed it is that easy. You point out that there are no longer any documents attached to the transactions.

```clojure no-exec id=01526534-9f3a-408e-9a0e-7671301dbae1
"I am grateful that you took the time to show me this.

Today is a sad day for me as I have very much enjoyed my time here.

Although, I was expecting to hear from a friend before I left. There is a very important message that I am waiting for."

- Kaarlang
```

You remember the secret note in your pocket and pass it to Kaarlang.

# The Secret Note.

Kaarlang reads the note.

He looks at you with the a peculiar facial expression.

```clojure no-exec id=a384cc47-f3a0-45f9-9997-0a0e9f08da00
"This is the note I was waiting for.

It has information about the a top secret stellar transport shuttle.

If you are interested in a great adventure, the shuttle comes once every hundred years or so to take a select few to a nearby star system. The system is home to a mysterious hyper-intelligent form of life.

I’m sure there would be a place for you if you were willing to help out. The passengers on the shuttle have the right to be forgotten. We need someone that can remove the passengers data from the solar system.

What do you think?"

- Kaarlang
```

## Choose your path

**"That sounds like a great opportunity, I'm in"** - *You head off to the [secret location](https://nextjournal.com/xtdb-tutorial/evict) of the shuttle.*

**"No thanks, not for me."** - *You choose not to got with Kaarlang. You continue working for Helios Banking Inc. for the rest of your days.*

![Oumuamua: Evict](https://github.com/xtdb/xtdb-tutorial/raw/main/images/6b-evict-meteor.png)


[nextjournal#output#cf517ae0-bb75-40a0-b34d-d6a20a5c855b#result]:
<https://nextjournal.com/data/Qmb916RdkE8C3ZUY7HYdwwucyBw2AXM71KjedQFMk9kveP?content-type=application/transit%2Bjson&node-id=cf517ae0-bb75-40a0-b34d-d6a20a5c855b&node-kind=output>

<details id="com.nextjournal.article">
<summary>This notebook was exported from <a href="https://nextjournal.com/a/LPt2uB1vJAVKPxEkT2UX1?change-id=CwhbFpziTmZXDBkHboZXFj">https://nextjournal.com/a/LPt2uB1vJAVKPxEkT2UX1?change-id=CwhbFpziTmZXDBkHboZXFj</a></summary>

```edn nextjournal-metadata
{:article
 {:settings nil,
  :nodes
  {"01526534-9f3a-408e-9a0e-7671301dbae1"
   {:id "01526534-9f3a-408e-9a0e-7671301dbae1",
    :kind "code-listing",
    :name "Speaking from the heart"},
   "061c7ac5-9255-48ab-ac82-97bbdf7d9746"
   {:compute-ref #uuid "b5f88edf-8d5b-41d4-937c-d7f985626430",
    :exec-duration 390,
    :id "061c7ac5-9255-48ab-ac82-97bbdf7d9746",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "1b0cfda6-c1c6-4def-99bd-c071c6938be0"
   {:compute-ref #uuid "bba557e8-1b08-483c-bd34-0488e8af4fa0",
    :exec-duration 82,
    :id "1b0cfda6-c1c6-4def-99bd-c071c6938be0",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "223ddbe8-8eed-4e69-ae1c-57f484971dcb"
   {:id "223ddbe8-8eed-4e69-ae1c-57f484971dcb",
    :kind "code-listing",
    :name "xtdb Manual"},
   "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153"
   {:compute-ref #uuid "8d2bc237-cb4f-4c44-970d-a91794d52eee",
    :exec-duration 6145,
    :id "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "35dc65e9-f458-4e32-9a59-1af72cd12a78"
   {:compute-ref #uuid "cf1250ed-a909-4cba-9231-6dcceaea60c0",
    :exec-duration 12560,
    :id "35dc65e9-f458-4e32-9a59-1af72cd12a78",
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
    :name "Helios Banking Inc."},
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
   "82b75d0b-67f2-4bc2-a36a-5bd2051e1807"
   {:id "82b75d0b-67f2-4bc2-a36a-5bd2051e1807", :kind "file"},
   "a384cc47-f3a0-45f9-9997-0a0e9f08da00"
   {:id "a384cc47-f3a0-45f9-9997-0a0e9f08da00", :kind "code-listing"},
   "a86efe70-c896-4cc1-ad16-2bdc46cb01b2"
   {:id "a86efe70-c896-4cc1-ad16-2bdc46cb01b2",
    :kind "code-listing",
    :name "Boundary Control"},
   "a9aa840c-db6e-49a0-aa74-6aca9b824072"
   {:compute-ref #uuid "12eab981-8ef4-4817-ab2c-9755b4f6858f",
    :exec-duration 61,
    :id "a9aa840c-db6e-49a0-aa74-6aca9b824072",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "b62c033a-93f1-438b-abf1-bbbd0050c31f"
   {:id "b62c033a-93f1-438b-abf1-bbbd0050c31f",
    :kind "code-listing",
    :name "Job Ticket"},
   "cf517ae0-bb75-40a0-b34d-d6a20a5c855b"
   {:compute-ref #uuid "512fbbba-bc28-4aae-965c-b8672c35c9ca",
    :exec-duration 767,
    :id "cf517ae0-bb75-40a0-b34d-d6a20a5c855b",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "ffcf0396-b3f9-40e6-a0c2-654401879781"
   {:id "ffcf0396-b3f9-40e6-a0c2-654401879781",
    :kind "code-listing",
    :name "deps.edn"}},
  :nextjournal/id #uuid "02b53b5a-c7fb-4040-aea5-13b251267e00",
  :article/change
  {:nextjournal/id #uuid "60b7b425-5983-4ed4-aa93-999bde9d0c2e"}}}

```
</details>
