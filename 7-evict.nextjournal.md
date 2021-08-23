# 7: Evict with XTDB – The secret mission.

![Clojure_logo.png][nextjournal#file#6e1e3414-7ad4-42fa-ace0-6939985e69e2]

# Introduction

This is the `evict` installment of the xtdb tutorial.

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

# Arrival

You arrive at the comet 'Oumuamua and pull along side, asking for permission to land. A voice comes over the communications system

```clojure no-exec id=a86efe70-c896-4cc1-ad16-2bdc46cb01b2
"How did you find us? Who sent you??"

— Mysterious person  
```

## Choose your path:

**"Kaarlang sent me"** : *You have permission to land, continue to the space port.*

**"I'm not sure how I got here, I found you by mistake"**: *You are sent away. You must return to [Jupiter](https://nextjournal.com/xtdb-tutorial/delete) and find Kaarlang.*

# Space Port

You land on the space port and are ushered inside. The ships captain, Ilex, greets you.

```clojure no-exec id=223ddbe8-8eed-4e69-ae1c-57f484971dcb
"Hello, it’s good to have you with us.

We are set to leave the solar system right away and as part of our service we offer people the right to be forgotten. Some are not worried that their information is kept here, however others want there to be no personal data left behind.

You may not have been told this yet, but this comet is actually a transportation vessel. It will take us to the star system Gilese 667C which is home to intelligent life far superior to our own. We all are hoping to find opportunities beyond our wildest dreams. All records of this transportation vessel and any life outside of the solar system are heavily monitored and wiped in the interest of preserving the normal technological advancement of the Human race. This means we know little of the beings we are going to meet.

Our task for you is to remove the records of the people who have chosen to be forgotten here."

— Ilex
```

You are excited by the prospect and agree to help. First you read the manual entry for `evict` as this will be the perfect tool.

```custom no-exec id=4ec8e24c-39cf-4080-be97-3de19d78af04
Currently there are only four transaction operations in xtdb: put, delete, match and evict.

		Transaction 	(Description)
    put    		   	(Writes a version of a document)
    delete    		(Deletes a version of a document)
    match         (Stops a transaction if the precondition is not met.)
    evict    			(Removes an document entirely)

Evict:
XTDB supports eviction of active and historical data to assist with technical compliance for information privacy regulations.

The main transaction log contains only hashes and is immutable. All document content is stored in a dedicated document log that can be evicted by compaction.

evict removes a document from xtdb. The transaction history will be available, but all versions at or within the provided valid time window are evicted.

A complete evict transaction has the form:
[:crux.tx/put eid]

- xtdb manual
```

*[Read More.](https://xtdb.com/reference/transactions.html#evict)*

You are happy with what you have read, and in anticipation of the assignment you define the standalone system.

```clojure id=2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153
(def crux (crux/start-node {}))
```

# Data Removal

You are given the data for the people on the ship and sync up your xtdb node. You decide that you are going to embark on this adventure along with them so you add your name to the list.

```clojure id=950de198-0847-4b3b-bd24-1d1300a30158
(crux/submit-tx crux
                [[:crux.tx/put
                  {:crux.db/id :person/kaarlang
                   :full-name "Kaarlang"
                   :origin-planet "Mars"
                   :identity-tag :KA01299242093
                   :DOB #inst "2040-11-23"}]

                 [:crux.tx/put
                  {:crux.db/id :person/ilex
                   :full-name "Ilex Jefferson"
                   :origin-planet "Venus"
                   :identity-tag :IJ01222212454
                   :DOB #inst "2061-02-17"}]

                 [:crux.tx/put
                  {:crux.db/id :person/thadd
                   :full-name "Thad Christover"
                   :origin-moon "Titan"
                   :identity-tag :IJ01222212454
                   :DOB #inst "2101-01-01"}]

                 [:crux.tx/put
                  {:crux.db/id :person/johanna
                   :full-name "Johanna"
                   :origin-planet "Earth"
                   :identity-tag :JA012992129120
                   :DOB #inst "2090-12-07"}]])
```

Before you start the eviction process you make a query function so you can see the full results of anything stored in xtdb:

```clojure id=99b0dd9c-d5cb-4c34-8a77-d71f941e97cd
(defn full-query
  [node]
  (crux/q
   (crux/db node)
   '{:find [(pull e [*])]
     :where [[e :crux.db/id id]]}))
```

You show the others the result:

```clojure id=9aaf2276-94b6-4c1e-a4e2-716c1dc3d7c3
(full-query crux)
```

The xtdb manual said that the `evict` operation will remove a document entirely. Ilex tells you the only person who whishes to exercise their right to be forgotten is Kaarlang.

```clojure id=188a6bc3-288a-4a96-b18d-bdbe893c7bcb
  (crux/submit-tx crux [[:crux.tx/evict :person/kaarlang]])
```

You use your function and see that the transaction was a success.

```clojure id=c8c2c663-6436-429b-8125-70350b4302e3
(full-query crux)
```

All the data associated with the the specified `:crux.db/id` has been removed from the xtdb along with the eid itself.

The transaction history is immutable. This means the transactions will never be removed. You assure Ilex that the documents are completely removed from xtdb, you can show this by looking at the `history-descending` information for each person.

```clojure id=00a1bb7a-46dc-4455-ba90-a50c485f7e46
(crux/entity-history (crux/db crux)
                     :person/kaarlang
                     :desc
                     {:with-docs? true})
```

You show the results to Kaarlang who is happy that there his details are no longer a part of the ships logs.

# Departure

The ship starts to shake as the engines are fired up.

Ilex thanks you and takes you to the Cryogenics department. You must be put into stasis as the journey will take around 25 years, even at the near light speeds of the ship.

You are astonished with the amount that you have done in one short week. How did little old you end up with an opportunity as big as this?

Your eyes get heavy as the cryogenicist initiates the hibernation process. As they do, you wonder if you’ll ever come back to the solar system.

![ice.jpg][nextjournal#file#f0026e73-9244-45f5-ac13-bab33663707d]

# This is not The End

I hope you enjoyed learning about the basics of xtdb. Although this is the final installment for the main tutorial series it is not the end of the xtdb tutorial. Watch this space for more tutorial releases of the tutorial where you will be sent on assignments to solve more specific and complex tasks using xtdb.

I’d love to hear any ideas for enhancements so don’t hesitate to get in touch.

![oumuamua.jpg][nextjournal#file#82b75d0b-67f2-4bc2-a36a-5bd2051e1807]


[nextjournal#file#6e1e3414-7ad4-42fa-ace0-6939985e69e2]:
<https://nextjournal.com/data/QmeRF9PNTDvthrnwMqDN3RooRGtNCWaUcAzHjEvGSwMUet?content-type=image/png&node-id=6e1e3414-7ad4-42fa-ace0-6939985e69e2&filename=Clojure_logo.png&node-kind=file>

[nextjournal#file#f0026e73-9244-45f5-ac13-bab33663707d]:
<https://nextjournal.com/data/Qmbs3zaMfcuUkqLb4aH1v3oaPPK6NmzT2xGQkfY4TUKEzb?content-type=image/jpeg&node-id=f0026e73-9244-45f5-ac13-bab33663707d&filename=ice.jpg&node-kind=file>

[nextjournal#file#82b75d0b-67f2-4bc2-a36a-5bd2051e1807]:
<https://nextjournal.com/data/QmXnPp5t6NYoZYK4iT438kr6KxV4h2TKXfjW4KbRBrt6XN?content-type=image/jpeg&node-id=82b75d0b-67f2-4bc2-a36a-5bd2051e1807&filename=oumuamua.jpg&node-kind=file>

<details id="com.nextjournal.article">
<summary>This notebook was exported from <a href="https://nextjournal.com/a/LPtrycCCeyMe5hDp9YQTU?change-id=CyhMmbqH2c4Wsqyu6fbq33">https://nextjournal.com/a/LPtrycCCeyMe5hDp9YQTU?change-id=CyhMmbqH2c4Wsqyu6fbq33</a></summary>

```edn nextjournal-metadata
{:article
 {:settings nil,
  :nodes
  {"00a1bb7a-46dc-4455-ba90-a50c485f7e46"
   {:compute-ref #uuid "5b7ccd91-a5eb-435d-81a7-e9274983b4e6",
    :exec-duration 50,
    :id "00a1bb7a-46dc-4455-ba90-a50c485f7e46",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "188a6bc3-288a-4a96-b18d-bdbe893c7bcb"
   {:compute-ref #uuid "b49a2b0a-e7d5-474d-bc5b-a13f84a54258",
    :exec-duration 46,
    :id "188a6bc3-288a-4a96-b18d-bdbe893c7bcb",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "223ddbe8-8eed-4e69-ae1c-57f484971dcb"
   {:id "223ddbe8-8eed-4e69-ae1c-57f484971dcb",
    :kind "code-listing",
    :name "Ship Captain"},
   "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153"
   {:compute-ref #uuid "ce645546-3408-48a2-8493-9df4d43771ed",
    :exec-duration 7791,
    :id "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "35dc65e9-f458-4e32-9a59-1af72cd12a78"
   {:compute-ref #uuid "aa535970-b502-4239-a928-b70413014d99",
    :exec-duration 12201,
    :id "35dc65e9-f458-4e32-9a59-1af72cd12a78",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "4ec8e24c-39cf-4080-be97-3de19d78af04"
   {:custom-language "txt",
    :id "4ec8e24c-39cf-4080-be97-3de19d78af04",
    :kind "code-listing",
    :name "xtdb Manual"},
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
   "82b75d0b-67f2-4bc2-a36a-5bd2051e1807"
   {:id "82b75d0b-67f2-4bc2-a36a-5bd2051e1807", :kind "file"},
   "8480e0d0-9ad2-440a-a7cb-6bebb50f77d8"
   {:compute-ref #uuid "2ccdf133-dafd-4f2b-be6e-c2f242798100",
    :exec-duration 43,
    :id "8480e0d0-9ad2-440a-a7cb-6bebb50f77d8",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "950de198-0847-4b3b-bd24-1d1300a30158"
   {:compute-ref #uuid "d2c7534e-fb26-45e5-b676-c0600e102d77",
    :exec-duration 359,
    :id "950de198-0847-4b3b-bd24-1d1300a30158",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "99b0dd9c-d5cb-4c34-8a77-d71f941e97cd"
   {:compute-ref #uuid "bc949a24-d4d2-4c92-ae9a-61347d13dd14",
    :exec-duration 69,
    :id "99b0dd9c-d5cb-4c34-8a77-d71f941e97cd",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "9aaf2276-94b6-4c1e-a4e2-716c1dc3d7c3"
   {:compute-ref #uuid "0ba83de0-427a-415a-95f6-a016b584c595",
    :exec-duration 283,
    :id "9aaf2276-94b6-4c1e-a4e2-716c1dc3d7c3",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "a86efe70-c896-4cc1-ad16-2bdc46cb01b2"
   {:id "a86efe70-c896-4cc1-ad16-2bdc46cb01b2",
    :kind "code-listing",
    :name "Top secret security"},
   "c8c2c663-6436-429b-8125-70350b4302e3"
   {:compute-ref #uuid "48509611-2e6b-43d5-a912-fed534082385",
    :exec-duration 96,
    :id "c8c2c663-6436-429b-8125-70350b4302e3",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "f0026e73-9244-45f5-ac13-bab33663707d"
   {:id "f0026e73-9244-45f5-ac13-bab33663707d", :kind "file"},
   "ffcf0396-b3f9-40e6-a0c2-654401879781"
   {:id "ffcf0396-b3f9-40e6-a0c2-654401879781",
    :kind "code-listing",
    :name "deps.edn"}},
  :nextjournal/id #uuid "02b53d9b-fbfd-4709-92f8-3de9201154f3",
  :article/change
  {:nextjournal/id #uuid "60ff0c45-65f9-45eb-b08b-f980bbc25e06"}}}

```
</details>
