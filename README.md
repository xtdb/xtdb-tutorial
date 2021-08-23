# XTDB Tutorial

Official xtdb "Space Adventure" tutorial

## Quickstart

* Read the tutorial: https://nextjournal.com/xtdb-tutorial/
* Try online:
    1. https://nextjournal.com/try/xtdb-tutorial/start
    2. https://nextjournal.com/try/xtdb-tutorial/put
    3. https://nextjournal.com/try/xtdb-tutorial/datalog-queries
    4. https://nextjournal.com/try/xtdb-tutorial/bitemporality
    5. https://nextjournal.com/try/xtdb-tutorial/match
    6. https://nextjournal.com/try/xtdb-tutorial/delete
    7. https://nextjournal.com/try/xtdb-tutorial/evict
    8. https://nextjournal.com/try/xtdb-tutorial/await



## Install Tutorial on Nextjournal

The Quickstart is the easiest way to run the tutorial and you can use the Nextjournal
"Remix" feature to bring the tutorial into your own Nextjournal Clojure notebooks if you
like. However, you can import the Markdown files in this repository directly, if you would
prefer.

1. Create a new account on [Nextjournal](https://nextjournal.com).
2. Download a Markdown file from this repository root.
3. Click "+ NEW" to create a new notebook on the [Nextjournal Dashboard](https://nextjournal.com/dashboard).
4. At the bottom of the page, choose "Import: Select a Markdown file to import" and upload the `.md` file you downloaded in Step 2.
Do not use "Import from a (GitHub) URL" -- it will not work.
5. Once imported, scroll down to the EDN block which begins with `{:deps ...` under "Runtime Setup".
Hover your cursor over this EDN block and an ellipsis ("...") will appear on the lefthand side.
Click the ellipsis.
Choose "Assign Name" and enter `deps.edn`.
6. Open the Clojure Runtime Settings by clicking "Runtimes: Clojure" in the lefthand sidebar.
Scroll down to "Mounts" and click "+ Add mount".
Select `deps.edn`.
Click "Save changes and start" in the dialog that appears at the top.

You can now run the tutorial notebook.

## Reinstallation (for maintainers)

For now, the Nextjournal notebooks are the golden store for the Space Adventure tutorial.
As such, you should edit a Nextjournal notebook by following these steps:

1. Edit the notebook directly in Nextjournal
2. Select "Share: Publish Changes" to republish it
3. Select "Editor => Export => Export As Markdown" from the upper-right menu
4. Rename the exported file so it matches the corresponding file in this repository.
Do not edit any of the image references.
Although the images are tracked in this repo, they are not linked from the Markdown files.
(See below.)
5. Push your changed notebook to this repository.

TODO: In the future, it would make more sense to switch to the "Markdown-first" technique of using
GitHub as the golden store. To do this, we need to edit the Markdown files to remove Nextjournal
metadata and reference absolute GitHub URLs instad of Nextjournal URLs.
After that happens, please replace these instructions with reinstallation
instructions similar to those used for the "Learn XTDB Datalog Today" tutorial:
https://github.com/xtdb/learn-xtdb-datalog-today#re-installation-for-maintainers

## Copyright & License

The MIT License (MIT)

Copyright © 2019-2021 JUXT LTD.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
