# 6: Delete with XTDB – Jupiter Assignment

![Jupiter: Delete](https://github.com/xtdb/xtdb-tutorial/raw/main/images/6a-delete-jupiter-title.png)

# Introduction

This is the `delete` installment of the xtdb tutorial.

## Setup

You need to get xtdb running before you can use it.

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

# Arrival on Jupiter

You approach Jupiter and marvel at its bands. You wish you could have seen it this close before the Great Red Spot dissipated.

As you enter the Jovian atmosphere your communications panel lights up with the now expected, but rather terse message from boundary control.

```clojure no-exec
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

```clojure no-exec
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

```clojure
(def node (xt/start-node {}))
```

# Assignment

You land on the raised platform and open your job ticket

## Ticket

```clojure no-exec
Task 			"Remove assigned clients"
Company		"Helios Banking Inc."
Contact 	"Kaarlang"
Submitted "2115-02-10T13:38:20"
Additional information:
"Help Kaarlang delete client history from his xtdb node in accordance with Earth data protection laws."
```

As you leave your ship, you are met by the martian Kaarlang:

```clojure no-exec
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

```clojure
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

```clojure
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

```clojure
(xt/submit-tx
 node
 [[::xt/delete :kaarlang/clients #inst "2110-01-01" #inst "2116-01-01"]])
```

Using the same method as before you show Kaarlang the effect that this operation has had.

```clojure
(xt/entity-history
 (xt/db node #inst "2115-01-01T08")
 :kaarlang/clients
 :desc
 {:with-docs? true})
```

Kaarlang is impressed it is that easy. You point out that there are no longer any documents attached to the transactions.

```clojure no-exec
"I am grateful that you took the time to show me this.

Today is a sad day for me as I have very much enjoyed my time here.

Although, I was expecting to hear from a friend before I left. There is a very important message that I am waiting for."

- Kaarlang
```

You remember the secret note in your pocket and pass it to Kaarlang.

# The Secret Note.

Kaarlang reads the note.

He looks at you with the a peculiar facial expression.

```clojure no-exec
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