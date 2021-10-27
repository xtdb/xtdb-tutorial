# 1: Getting Started with XTDB – A Tale of Time and Space

![XTDB Tutorial: Start](https://github.com/xtdb/xtdb-tutorial/raw/main/images/1a-start-earth-title.png)

# Introduction

Welcome to the official xtdb tutorial, which complements the official [documentation](https://xtdb.com/reference/installation.html).

If you’re new to xtdb and want to get a practical hands-on guide, you’re in the right place.

## The Story

It’s the year 2115. You have been hired by an inter-planetary bank, Helios Banking Inc. Your task is to travel around the solar system completing assignments using xtdb.

You have been given a company spaceship so transport won’t be a problem. Space Customs require all astronauts to complete a flight manifest for every journey. You also have a handy xtdb manual with you so you can read up on some background information before you get stuck into each assignment.

Let’s begin.

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

You are now ready for your first assignment, so you head over to the space port.

# Space Port

On entering your spaceship, you notice a flashing blue light on the left of your communications panel. You submit an iris scan to unlock your first message.

```clojure no-exec id=0dfd4496-febc-41b0-962a-0fc4e590368a
"A warm welcome from the Helios family.

For your first assignment we would like you to go to Pluto and help Tombaugh Resources Ltd. set up their stock reporting system. There will be a ticket with more information waiting for you upon your arrival. Find Reginald if you have any questions.

Safe journeys, and don’t forget to fill in your flight manifest. They won’t let you land without one."

— Helios Banking Inc.
```

## Power up

Before you leave you must fill in your flight manifest. To do this, you must first set up a xtdb node. You want to get started as quickly as possible so you decide to use xtdb's inbuilt standalone node.

You read the xtdb manual entry for the standalone node to make sure this is OK.

```clojure no-exec id=bda0feab-462e-4584-be69-e9e3c23d25f7
"If you want to get up and running with xtdb fast, consider using the standalone node. There is a xtdb inbuilt standalone node which is the most simple way to start playing with xtdb. Bear in mind that this does not store any information beyond your session.

For persistent storage consider using RocksDB and for scale you should consider using Kafka."

— xtdb manual
```

*[Read More](https://xtdb.com/reference/installation.html)*

You decide this is fine for now, and so define your xtdb node.

```clojure id=2bdeaaa6-3672-48c1-bbc7-aa5d05fd1153
(def node
  (xt/start-node {}))
```

## Flight Manifest

You take a look around your ship and check the critical levels.

You read the manual entry for putting data into xtdb.

```clojure no-exec id=ca575f8d-8096-48d8-acef-9a05712e43c6
"XTDB takes information in document form. Each document must be in Extensible Data Notation (edn) and each document must contain a unique `:xt/id` value. However, beyond those two requirements you have the flexibility to add whatever you like to your documents because xtdb is schemaless."

— xtdb manual
```

*[Read More](https://xtdb.com/reference/transactions.html#put)*

Just as you’re about to write your manifest, one of the porters passes you a secret note and asks you to deliver it to a martian named Kaarlang. They are certain you will meet Kaarlang on your travels and so you see no harm in delivering the note for them.

```clojure id=4a6c4961-14b1-4ac9-96e7-7aec033b55a8
(def manifest
  {:xt/id :manifest
   :pilot-name "Johanna"
   :id/rocket "SB002-sol"
   :id/employee "22910x2"
   :badges "SETUP"
   :cargo ["stereo" "gold fish" "slippers" "secret note"]})
```

You put the manifest into xtdb.

```clojure id=bd1e9012-f10e-4bfb-bee3-83abf3be162e
(xt/submit-tx node [[::xt/put manifest]])
```

This is `put`, one of xtdb's four transaction operations. Check that this was successful by asking xtdb to show the whole entity.

```clojure id=3e0d81c5-2598-432f-9f83-038b47b5f5fc
(xt/entity (xt/db node) :manifest)
```

*Note: You should run this code block separately otherwise you may see only `nil` returned. The reason for this is covered in the [await-tx](https://nextjournal.com/xtdb-tutorial/await/) tutorial.*

You enter the countdown for lift off to Pluto. [See you soon](https://nextjournal.com/xtdb-tutorial/put).

![Pluto: tx/put](https://github.com/xtdb/xtdb-tutorial/raw/main/images/1b-put-tx-pluto.png)