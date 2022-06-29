# 1: Getting Started with XTDB – A Tale of Time and Space

![XTDB Tutorial: Start](https://github.com/xtdb/xtdb-tutorial/raw/main/images/1a-start-earth-title.png)

# Introduction

Welcome to the official xtdb tutorial, which complements the official [documentation](https://docs.xtdb.com/administration/installing/).

If you’re new to XTDB and want to get a practical hands-on guide, you’re in the right place.

## The Story

It’s the year 2115.
You have been hired by an inter-planetary bank, Helios Banking Inc.
Your task is to travel around the solar system completing assignments using XTDB.

You have been given a company spaceship so transport won’t be a problem.
Space Customs require all astronauts to complete a flight manifest for every journey.
You also have a handy XTDB manual with you so you can read up on some background information before you get stuck into each assignment.

Let’s begin.

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

You are now ready for your first assignment, so you head over to the spaceport.

# Spaceport

On entering your spaceship, you notice a flashing blue light on the left of your communications panel.
You submit an iris scan to unlock your first message.


> A warm welcome from the Helios family.
>
> For your first assignment we would like you to go to Pluto and help Tombaugh Resources Ltd. set up their stock reporting system.
> There will be a ticket with more information waiting for you upon your arrival.
> Find Reginald if you have any questions.
>
>Safe journeys, and don’t forget to fill in your flight manifest. They won’t let you land without one.
>
> — Helios Banking Inc.

## Power up

Before you leave you must fill in your flight manifest.
To do this, you must first set up a XTDB node.
You want to get started as quickly as possible so you decide to use XTDB's inbuilt standalone node.

You read the XTDB manual entry for the standalone node to make sure this is OK.

> If you want to get up and running with XTDB fast, consider using the standalone node.
> There is a XTDB inbuilt standalone node which is the most simple way to start playing with XTDB.
> Bear in mind that this does not store any information beyond your session.
>
> For persistent storage consider using RocksDB and for scale you should consider using Kafka.
>
> — xtdb manual *[Read More](https://docs.xtdb.com/administration/installing/)*

You decide this is fine for now, and so define your XTDB node.

```clojure
(def node (xt/start-node {}))
```

## Flight Manifest

You take a look around your ship and check the critical levels.

You read the manual entry for putting data into XTDB.

> XTDB takes information in document form.
> Each document must be in Extensible Data Notation (edn) and each document must contain a unique `:xt/id` value.
> However, beyond those two requirements you have the flexibility to add whatever you like to your documents because XTDB is schemaless.
>
> — XTDB manual *[Read More](https://xtdb.com/reference/transactions.html#put)*

Just as you’re about to write your manifest, one of the porters passes you a secret note and asks you to deliver it to a martian named Kaarlang.
They are certain you will meet Kaarlang on your travels and so you see no harm in delivering the note for them.

```clojure
(def manifest
  {:xt/id :manifest
   :pilot-name "Johanna"
   :id/rocket "SB002-sol"
   :id/employee "22910x2"
   :badges "SETUP"
   :cargo ["stereo" "gold fish" "slippers" "secret note"]})
```

You put the manifest into XTDB.

```clojure
(xt/submit-tx node [[::xt/put manifest]])
```

This is `put`, one of XTDB's four transaction operations.

Make sure this transaction has taken effect using `sync` which ensures that the node's indexes are caught up with the latest transaction.

```clojure
(xt/sync node)
```

Check that this was successful by asking XTDB to show the whole entity.

```clojure
(xt/entity (xt/db node) :manifest)
```

You enter the countdown for lift off to Pluto. [See you soon](https://nextjournal.com/xtdb-tutorial/put).

![Pluto: tx/put](https://github.com/xtdb/xtdb-tutorial/raw/main/images/1b-put-tx-pluto.png)
