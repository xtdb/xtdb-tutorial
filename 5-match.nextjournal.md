# 5: Match with XTDB – Saturn Assignment

![Clojure_logo.png][nextjournal#file#6e1e3414-7ad4-42fa-ace0-6939985e69e2]

# Introduction

This is the `match` installment of the xtdb tutorial.

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

# Arrival on Saturn

As you pass through the innermost ring of Saturn, A warning light appears on your communications panel. You open the message. It’s from Space Customs and reads:

```clojure no-exec id=a86efe70-c896-4cc1-ad16-2bdc46cb01b2
"We extend you the warmest welcome.

We must check papers before we can give you permission to land."

— Cronus Peaceful Nations 
```

They are asking to see your flight manifest.

## Choose your path:

**You have your manifest** : *You have permission to land, continue to the space port.*

**You do not have your manifest** : *You do not have permission to land. You can either return to [Neptune](https://nextjournal.com/xtdb-tutorial/bitempoarlity) or continue at your own risk.*

# Space Port

As you prepare to land you open your xtdb manual to the page on `match`

```clojure no-exec id=223ddbe8-8eed-4e69-ae1c-57f484971dcb
"Currently there are only four transaction operations in xtdb: put, delete, match and evict.
		Transaction 	(Description)
    put    		(Writes a version of a document)
    delete    (Deletes a version of a document)
    match     (Stops a transaction if the precondition is not met.)
    evict    	(Removes an document entirely)

match:

match checks the current state of an entity - if the entity doesn’t match the provided doc, the transaction will not continue. You can also pass nil to check that the entity doesn’t exist prior to your transaction.

A match transaction takes the entity id, along with an expected document. Optionally you can provide a valid time.

Time in xtdb is denoted #inst 'yyyy-MM-ddThh:mm:ss'. For example, 9:30 pm on January 2nd 1999 would be written: #inst \"1999-01-02T21:30:00\".

A complete match transaction has the form:

    [:crux.tx/match entity-id expected-doc valid-time]

Note that if there is no old-doc in the system, you can provide `nil` in its place."

— xtdb manual
```

*[Read More](https://xtdb.com/reference/transactions.html#match)*

You are happy with what you have read, and in anticipation of the assignment you define the standalone node.

```clojure id=2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153
(def crux (crux/start-node {}))
```

# Assignment

As you land on the surface of Saturn the job ticket for this assignment is unlocked.

## Ticket

```clojure no-exec id=b62c033a-93f1-438b-abf1-bbbd0050c31f
Task 			"Secure trading system"
Company		"Cronus Market Technologies"
Contact 			"Ubuku Eppimami"
Submitted "2115-02-23T13:38:20"
Additional information:
"We need to be shown how to ensure no trades are done without the buyer and seller having the necessary funds or stock respectively"
```

[example_data.txt][nextjournal#file#6b4d39da-314d-4dab-87f7-f2f61bd99ac4]

The next shuttle to the CMT office leaves in 5 Earth minutes. While you wait you use your easy ingest function you created on Pluto to put the example data into your system. 

```clojure id=c89cbbfb-52db-4b38-8e24-53fbe15ab1a6
(defn easy-ingest
  "Uses xtdb put transaction to add a vector of 
  documents to a specified node"
  [node docs]
  (crux/submit-tx node
                  (vec (for [doc docs]
                         [:crux.tx/put doc]))))

(def data
  [{:crux.db/id :gold-harmony
    :company-name "Gold Harmony"
    :seller? true
    :buyer? false
    :units/Au 10211
    :credits 51}

   {:crux.db/id :tombaugh-resources
    :company-name "Tombaugh Resources Ltd."
    :seller? true
    :buyer? false
    :units/Pu 50
    :units/N 3
    :units/CH4 92
    :credits 51}

   {:crux.db/id :encompass-trade
    :company-name "Encompass Trade"
    :seller? true
    :buyer? true
    :units/Au 10
    :units/Pu 5
    :units/CH4 211
    :credits 1002}

   {:crux.db/id :blue-energy
    :seller? false
    :buyer? true
    :company-name "Blue Energy"
    :credits 1000}])

(easy-ingest crux data)
```

You also decide to make some Clojure functions so you can easily show Ubuku the stock and fund levels after the trades.

```clojure id=c4a32745-7d07-4e86-9c00-13251bfaf172
(defn stock-check
  [company-id item]
  {:result (crux/q (crux/db crux)
                   {:find '[name funds stock]
                    :where ['[e :company-name name]
                            '[e :credits funds]
                            ['e item 'stock]]
                    :args [{'e company-id}]})
   :item item})


(defn format-stock-check
  [{:keys [result item] :as stock-check}]
  (for [[name funds commod] result]
    (str "Name: " name ", Funds: " funds ", " item " " commod)))
```

Just as you are finishing off your shuttle arrives.

After a short journey through the icy lower clouds of Saturn you are met by a friendly faced Ubuku.

```clojure no-exec id=7b50754b-7b23-4cd7-a3ee-f7d3dbbca280
"Hello friend.

We have been using xtdb for a short time now and think it is great. The problem is a human one. Occasionally we process trades without checking that are enough funds in the buyers account.

I know there is a way that we can stop this happening in xtdb.

I sent you some example data in the job ticket for you to use, I trust you found it.

Do you think you can help us?"

— Ubuku Eppimami
```

## Choose your path:

**"Yes, I'll give it a go."** : *Continue to complete the assignment.*

**"I'm not even sure how to begin"** : *Take some time to read through the xtdb manual again. If you're still unsure then you can follow along anyway and see if things become clear.*

## Assignment

You explain to Ubuku that all they need to do to solve their problem is to use the `match` operation instead of `put` when they are processing their trades.

You show Ubuku the `match` operation for a valid transaction. You move 10 units of Methane (`:units/CH4`) each at the cost of 100 credits to Blue Energy:

```clojure id=061c7ac5-9255-48ab-ac82-97bbdf7d9746
(crux/submit-tx
 crux
 [[:crux.tx/match
   :blue-energy
   {:crux.db/id :blue-energy
    :seller? false
    :buyer? true
    :company-name "Blue Energy"
    :credits 1000}]
  [:crux.tx/put
   {:crux.db/id :blue-energy
    :seller? false
    :buyer? true
    :company-name "Blue Energy"
    :credits 900
    :units/CH4 10}]

  [:crux.tx/match
   :tombaugh-resources
   {:crux.db/id :tombaugh-resources
    :company-name "Tombaugh Resources Ltd."
    :seller? true
    :buyer? false
    :units/Pu 50
    :units/N 3
    :units/CH4 92
    :credits 51}]
  [:crux.tx/put
   {:crux.db/id :tombaugh-resources
    :company-name "Tombaugh Resources Ltd."
    :seller? true
    :buyer? false
    :units/Pu 50
    :units/N 3
    :units/CH4 82
    :credits 151}]])
```

You explain that because the old doc is as expected for both the buyer and the seller that the transaction goes through.

You show Ubuku the result of the trade using the function you created earlier:

```clojure id=cf517ae0-bb75-40a0-b34d-d6a20a5c855b
(format-stock-check (stock-check :tombaugh-resources :units/CH4))
```

```clojure id=adaf6a80-4241-40a1-9016-d342e1a056c1
(format-stock-check (stock-check :blue-energy :units/CH4))
```

They are happy that this works as he sees the 1000 credits move from Blue energy to Tombaugh Resources Ltd. and 10 units of Methane the other way.

Ubuku asks if you can show them what would happen if there was not enough funds in the account of a buyer. You show him a trade where the old doc is not as expected for Encompass trade, to buy 10,000 units of Gold from Gold Harmony.

```clojure id=1b0cfda6-c1c6-4def-99bd-c071c6938be0
(crux/submit-tx
 crux
 [[:crux.tx/match
   :gold-harmony
   {:crux.db/id :gold-harmony
    :company-name "Gold Harmony"
    :seller? true
    :buyer? false
    :units/Au 10211
    :credits 51}]
  [:crux.tx/put
   {:crux.db/id :gold-harmony
    :company-name "Gold Harmony"
    :seller? true
    :buyer? false
    :units/Au 211
    :credits 51}]

  [:crux.tx/match
   :encompass-trade
   {:crux.db/id :encompass-trade
    :company-name "Encompass Trade"
    :seller? true
    :buyer? true
    :units/Au 10
    :units/Pu 5
    :units/CH4 211
    :credits 100002}]
  [:crux.tx/put
   {:crux.db/id :encompass-trade
    :company-name "Encompass Trade"
    :seller? true
    :buyer? true
    :units/Au 10010
    :units/Pu 5
    :units/CH4 211
    :credits 1002}]])
```

```clojure id=9a9183ff-90f0-40b1-a6bb-9e56562e6306
(format-stock-check (stock-check :gold-harmony :units/Au))
```

```clojure id=873d71ce-d675-4be1-b924-2eb3c842eb05
(format-stock-check (stock-check :encompass-trade :units/Au))
```

You explain to Ubuku that this time, because you have both `match` operations in the same transaction, the trade does not go through. The accounts remain the same, even though the failing `match` was the second operation.

Ubuku thanks you. This is just what they are looking for. You head back to the space station to see if there is another assignment waiting for you.

# Space Port

Back at the spaceship there is a light waiting for you on your communications panel.

```clojure no-exec id=efc84bb8-702c-4fa7-82f8-6cfef39dd934
"Well done, you’ve had a productive week. We have one final task for you to do before you finish for the week

You need to go to Jupiter and meet Kaarlang, it’s his last day working for us and he needs to delete his trade clients from his personal xtdb node for data protection."

— Helios Banking Inc. 
```

You update your manifest with your most recent badge.

```clojure id=ab2ade6b-7352-4e44-b659-cad259ea12f9
(crux/submit-tx
 crux
 [[:crux.tx/put 
   {:crux.db/id :manifest
    :pilot-name "Johanna"
    :id/rocket "SB002-sol"
    :id/employee "22910x2"
    :badges ["SETUP" "PUT" "DATALOG-QUERIES" "MATCH"]
    :cargo ["stereo" "gold fish" "slippers" "secret note"]}]])
```

As you do so, you check to see if you still have the note that the porter gave you for Kaarlang back on Earth.

```clojure id=b8cc575e-1643-4334-bbeb-bc1ba34bbcc0
(crux/q (crux/db crux)
          {:find '[belongings]
           :where '[[e :cargo belongings]]
           :args [{'belongings "secret note"}]})
```

Feeling a bit apprehensive, you enter countdown for lift off to Jupiter.[ See you soon.](https://nextjournal.com/xtdb-tutorial/delete)

![jupiter.png][nextjournal#file#e9e8d400-877f-491a-a4ab-28fc05c935c8]


[nextjournal#file#6e1e3414-7ad4-42fa-ace0-6939985e69e2]:
<https://nextjournal.com/data/QmXv8JdzfS4Un1t67vVb4rcFwM4sZKaTHAbxirEFxgHrGy?content-type=image/png&node-id=6e1e3414-7ad4-42fa-ace0-6939985e69e2&filename=Clojure_logo.png&node-kind=file>

[nextjournal#file#6b4d39da-314d-4dab-87f7-f2f61bd99ac4]:
<https://nextjournal.com/data/QmZccAoCmFKy7FwaS9bYhwC22jUzHfwR8jWXxgGZTYwayr?content-type=text/plain&node-id=6b4d39da-314d-4dab-87f7-f2f61bd99ac4&filename=example_data.txt&node-kind=file>

[nextjournal#file#e9e8d400-877f-491a-a4ab-28fc05c935c8]:
<https://nextjournal.com/data/QmR2qQSDkzSAyWXWwdzvmdFz9eshAKJ3bWVNJRCgMvrdJV?content-type=image/png&node-id=e9e8d400-877f-491a-a4ab-28fc05c935c8&filename=jupiter.png&node-kind=file>

<details id="com.nextjournal.article">
<summary>This notebook was exported from <a href="https://nextjournal.com/a/LPgjG6AfUeVJw6fqWZ98f?change-id=CwhbE6uZ7FGJSzanpYMcVb">https://nextjournal.com/a/LPgjG6AfUeVJw6fqWZ98f?change-id=CwhbE6uZ7FGJSzanpYMcVb</a></summary>

```edn nextjournal-metadata
{:article
 {:settings nil,
  :nodes
  {"061c7ac5-9255-48ab-ac82-97bbdf7d9746"
   {:compute-ref #uuid "da4b0721-a9f9-478a-abba-11126c9eb99a",
    :exec-duration 130,
    :id "061c7ac5-9255-48ab-ac82-97bbdf7d9746",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "1b0cfda6-c1c6-4def-99bd-c071c6938be0"
   {:compute-ref #uuid "a9812963-c594-4772-98c4-fbde4cccd4b3",
    :exec-duration 99,
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
   {:compute-ref #uuid "e4d1a815-2fd4-47a9-8be0-5de56dc0cb6f",
    :exec-duration 6327,
    :id "2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "35dc65e9-f458-4e32-9a59-1af72cd12a78"
   {:compute-ref #uuid "4d0dc584-134b-4595-98a1-8f6bcef607a9",
    :exec-duration 12142,
    :id "35dc65e9-f458-4e32-9a59-1af72cd12a78",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "6b4d39da-314d-4dab-87f7-f2f61bd99ac4"
   {:id "6b4d39da-314d-4dab-87f7-f2f61bd99ac4", :kind "file"},
   "6e1e3414-7ad4-42fa-ace0-6939985e69e2"
   {:id "6e1e3414-7ad4-42fa-ace0-6939985e69e2",
    :kind "file",
    :layout :normal},
   "7b50754b-7b23-4cd7-a3ee-f7d3dbbca280"
   {:id "7b50754b-7b23-4cd7-a3ee-f7d3dbbca280",
    :kind "code-listing",
    :name "CMT"},
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
   "873d71ce-d675-4be1-b924-2eb3c842eb05"
   {:compute-ref #uuid "9dc60cdc-bdad-4680-a3d9-2d7c3d7df334",
    :exec-duration 54,
    :id "873d71ce-d675-4be1-b924-2eb3c842eb05",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "9a9183ff-90f0-40b1-a6bb-9e56562e6306"
   {:compute-ref #uuid "800a1df7-cc4f-43bc-969d-9193a486f88c",
    :exec-duration 87,
    :id "9a9183ff-90f0-40b1-a6bb-9e56562e6306",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "a86efe70-c896-4cc1-ad16-2bdc46cb01b2"
   {:id "a86efe70-c896-4cc1-ad16-2bdc46cb01b2", :kind "code-listing"},
   "ab2ade6b-7352-4e44-b659-cad259ea12f9"
   {:compute-ref #uuid "202af012-52f7-4abe-afec-c2ee290279d9",
    :exec-duration 65,
    :id "ab2ade6b-7352-4e44-b659-cad259ea12f9",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "adaf6a80-4241-40a1-9016-d342e1a056c1"
   {:compute-ref #uuid "08028ee8-a944-48b0-a20c-4c5275256eff",
    :exec-duration 104,
    :id "adaf6a80-4241-40a1-9016-d342e1a056c1",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "b62c033a-93f1-438b-abf1-bbbd0050c31f"
   {:id "b62c033a-93f1-438b-abf1-bbbd0050c31f",
    :kind "code-listing",
    :name "Job Ticket"},
   "b8cc575e-1643-4334-bbeb-bc1ba34bbcc0"
   {:compute-ref #uuid "0ad6b5f5-1740-485a-a2b2-30b705b11b6e",
    :exec-duration 145,
    :id "b8cc575e-1643-4334-bbeb-bc1ba34bbcc0",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "c4a32745-7d07-4e86-9c00-13251bfaf172"
   {:compute-ref #uuid "268bd0b2-dfbf-46c1-af79-1f2078545501",
    :exec-duration 134,
    :id "c4a32745-7d07-4e86-9c00-13251bfaf172",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "c89cbbfb-52db-4b38-8e24-53fbe15ab1a6"
   {:compute-ref #uuid "31597493-0dee-4972-a968-956431fe3997",
    :exec-duration 323,
    :id "c89cbbfb-52db-4b38-8e24-53fbe15ab1a6",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "cf517ae0-bb75-40a0-b34d-d6a20a5c855b"
   {:compute-ref #uuid "25f0e3cb-9ff2-4692-a7c0-707a8cc350b2",
    :exec-duration 460,
    :id "cf517ae0-bb75-40a0-b34d-d6a20a5c855b",
    :kind "code",
    :output-log-lines {},
    :refs (),
    :runtime [:runtime "80403b0a-1226-48ff-9bcc-624ed02e3635"]},
   "e9e8d400-877f-491a-a4ab-28fc05c935c8"
   {:id "e9e8d400-877f-491a-a4ab-28fc05c935c8", :kind "file"},
   "efc84bb8-702c-4fa7-82f8-6cfef39dd934"
   {:custom-language "Communications Panel",
    :id "efc84bb8-702c-4fa7-82f8-6cfef39dd934",
    :kind "code-listing",
    :name "Communications Panel"},
   "ffcf0396-b3f9-40e6-a0c2-654401879781"
   {:id "ffcf0396-b3f9-40e6-a0c2-654401879781",
    :kind "code-listing",
    :name "deps.edn"}},
  :nextjournal/id #uuid "02b51c9b-3632-4059-af85-4466bd6352ac",
  :article/change
  {:nextjournal/id #uuid "60b7b410-a19f-4304-bc7e-60918c9c7d96"}}}

```
</details>
