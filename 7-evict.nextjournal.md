# 7: Evict with XTDB – The secret mission

![Oumuamua: Evict](https://github.com/xtdb/xtdb-tutorial/raw/main/images/7a-evict-meteor-title.png)

# Introduction

This is the `evict` installment of the xtdb tutorial.

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

# Arrival

You arrive at the comet 'Oumuamua and pull along side, asking for permission to land. A voice comes over the communications system


> How did you find us? Who sent you??
>
> \- Mysterious person


## Choose your path:


  * **"Kaarlang sent me"** : 
      * *You have permission to land, continue to the space port.*


  * **"I'm not sure how I got here, I found you by mistake"**:
      *  *You are sent away. You must return to [Jupiter](https://nextjournal.com/xtdb-tutorial/delete) and find Kaarlang.*

# Space Port

You land on the space port and are ushered inside. The ships captain, Ilex, greets you.

Hello, it’s good to have you with us.

> We are set to leave the solar system right away and as part of our service we offer people the right to be forgotten. Some are not worried that their information is kept here, however others want there to be no personal data left behind.
>
> You may not have been told this yet, but this comet is actually a transportation vessel. It will take us to the star system Gilese 667C which is home to intelligent life far superior to our own. We all are hoping to find opportunities beyond our wildest dreams. All records of this transportation vessel and any life outside of the solar system are heavily monitored and wiped in the interest of preserving the normal technological advancement of the Human race. This means we know little of the beings we are going to meet.
>
> Our task for you is to remove the records of the people who have chosen to be forgotten here.
>
> \- Ilex

You are excited by the prospect and agree to help. First you read the manual entry for `evict` as this will be the perfect tool.

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
> ## Evict
> XTDB supports eviction of active and historical data to assist with technical compliance for information privacy regulations.
>
> The main transaction log contains only hashes and is immutable. All document content is stored in a dedicated document log that can be evicted by compaction.
>
> evict removes a document from xtdb. The transaction history will be available, but all versions at or within the provided valid time window are evicted.
>
> A complete evict transaction has the form:
> `[::xt/evict eid]`
>
> \- xtdb manual *[Read More.](https://xtdb.com/reference/transactions.html#evict)*

You are happy with what you have read, and in anticipation of the assignment you define the standalone system.

```clojure
(def node (xt/start-node {}))
```

# Data Removal

You are given the data for the people on the ship and sync up your xtdb node. You decide that you are going to embark on this adventure along with them so you add your name to the list.

```clojure
(xt/submit-tx node
                [[::xt/put
                  {:xt/id :person/kaarlang
                   :full-name "Kaarlang"
                   :origin-planet "Mars"
                   :identity-tag :KA01299242093
                   :DOB #inst "2040-11-23"}]

                 [::xt/put
                  {:xt/id :person/ilex
                   :full-name "Ilex Jefferson"
                   :origin-planet "Venus"
                   :identity-tag :IJ01222212454
                   :DOB #inst "2061-02-17"}]

                 [::xt/put
                  {:xt/id :person/thadd
                   :full-name "Thad Christover"
                   :origin-moon "Titan"
                   :identity-tag :IJ01222212454
                   :DOB #inst "2101-01-01"}]

                 [::xt/put
                  {:xt/id :person/johanna
                   :full-name "Johanna"
                   :origin-planet "Earth"
                   :identity-tag :JA012992129120
                   :DOB #inst "2090-12-07"}]])
```

Before you start the eviction process you make a query function so you can see the full results of anything stored in xtdb:

```clojure
(defn full-query
  [node]
  (xt/q
   (xt/db node)
   '{:find [(pull e [*])]
     :where [[e :xt/id id]]}))
```

You show the others the result:

```clojure
(full-query node)
```

The xtdb manual said that the `evict` operation will remove a document entirely. Ilex tells you the only person who whishes to exercise their right to be forgotten is Kaarlang.

```clojure
  (xt/submit-tx node [[::xt/evict :person/kaarlang]])
```

You use your function and see that the transaction was a success.

```clojure
(full-query node)
```

All the data associated with the the specified `:xt/id` has been removed from the xtdb along with the eid itself.

The transaction history is immutable. This means the transactions will never be removed. You assure Ilex that the documents are completely removed from xtdb, you can show this by looking at the `history-descending` information for each person.

```clojure
(xt/entity-history (xt/db node)
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

![ice.jpg](https://github.com/xtdb/xtdb-tutorial/raw/main/images/7b-evict-ice.jpg)

# This is not The End

I hope you enjoyed learning about the basics of xtdb. Although this is the final installment for the main tutorial series it is not the end of the xtdb tutorial. There is one last bonus mission to complete: learning to `await` transactions on [Kepra-5](https://nextjournal.com/xtdb-tutorial/await).

![Kepra 5: Await](https://github.com/xtdb/xtdb-tutorial/raw/main/images/7b-await-kepra5.png)
